# Providing Runtime configuration to an Angular Application

Created: May 10, 2022 5:27 PM
Finished: No
Source: https://imran3.medium.com/runtime-configuration-for-angular-c9d9082e1de3
Tags: #angular

![1*lYJG4QZqOq1RPEkzM9q_eg.jpeg](Providing%20Runtime%20configuration%20to%20an%20Angular%20Appl%20fb3017bda2444a2fbf2ae14dbacfdac8/1lYJG4QZqOq1RPEkzM9q_eg.jpeg)

Aarhus Havn, where the past meets with the future.

# Problem statement

There is quite often the need to provide some configuration values to a web application, these can be things like keys for initializing certain services (for example GoogleTagManager, Application Insights, AppDynamics), API Urls, set debug levels, input data for a specific service etc.

Furthermore, is it common to have a ***different set of config values for different use cases*:** for example, depending on the target environment (dev, test or production) we would like to set a specific debug level, initialize a third-party service like Application Insights with an environment-specific key.

Today, you’ll learn how to achieve this for an Angular web app.

> Note: for some of the steps presented here, I assume you are using Helm for Kuberneetes and Azure as your cloud service.
> 

# Providing a configuration value

Let consider a concrete example: [you want to add Application Insights](https://imran3.medium.com/integrating-application-insights-in-angular-15c0dc9e8d8f) in your app. Obviously, to use Application Insights you will need to provide a valid instrumentation key.

You could think of setting the key directly in your code and don’t have to worry about any of this config stuff, but trust me this is never a good idea:

- secrets should leave outside the code
- we could have multiple instrumentation keys, depending on the target environment.

Not entirely convinced? Have a look at [this article](https://withblue.ink/2021/05/07/storing-secrets-and-passwords-in-git-is-bad.html) and you’ll change your mind :D.

The following sections will present two possible solutions to this problem.

# 1. Compile time configuration: environment files

The main idea is to use the environment mechanism provided by Angular. It is possible to create multiple environment files:

Example of different environment files

Next, declare different configurations in angular.json to replace the default environment.ts with the correct one. Like this:

Example replacing env file for the test environment.

And finally, build the application with`-- configuration` option specifying the target environment:

```
ng build --configuration test-env
```

## Tradeoffs

The main drawback of this solution is that it does not work well with CI/CD pipelines. The environment files are consumed at build time by Angular, meaning that you will need to **build the application for each environment**. No *build-once => deploy everywhere* 😥 .

Furthermore, you will have to maintain multiple environment files, which might not be ideal.

# 2. Runtime configuration

A better solution is to set up *runtime configuration* in the Angular app so that the instrumentation key is set during application bootstrap.

This solution allows us to reuse the same build artifact for any target environment. *Build once => deploy everywhere* 😃.

First of all, you will need to create a config file in JSON format under `assets/`folder (we don’t want this file to be bundled during the build process) called`app.config.json` :

Example of simple app.config.json file with an entry for Application Insight Key.

Next, create a new Angular service, responsible for loading the configuration and providing it to any component/service requiring it:

Simple AppConfigService to load the config file

***Problem:*** How can we ensure that this configuration is loaded **before** the application is up and running (i.e. avoid cases when the config is required but has not been fully loaded yet)?

The ***solution*** is to use [Angular’s APP_INITIALIZER token](https://angular.io/api/core/APP_INITIALIZER) to hook in the application bootstrap step and load the config. To do this, edit the `app.module`and add the following:

## Using the AppConfigService

This service can be now used to provide the instrumentation key required by the Application Insight service.

Simply inject the AppConfigService wherever you need it and you are ready to use any config value from it.

# Providing instrumentation key value during deployment

As you might have noticed, the `app.config.json` file does not have a value for appInsightKeys.

The final step for having this solution up and running is to modify the CI/CD deployment pipeline of the application so that the correct instrumentation key value can be provided.

> Assumption: you are deploying the application using Helm charts.
> 

## Updating Helm Charts

The main idea is to replace the content of `assets/app.config.json`file with the values provided by Helm configmap.

First, make sure that the `value.yaml` file contains an `appConfigs`section like this:

Add appSettings section to Values.yaml file, containing entry for config value.

Next, edit the data section in `configmap.yaml`file to include the following:

Basically convert the values under appConfigs section into JSON format.

Then, edit the `deployment.yaml`file to include:

We mount the /assets/app.config.json file and replace its content with the value from configmap.

> Note: make sure to use the right path for mountPath. In this example our application resources are located under /var/www/ folder; this might not be the case for your app if so change the path accordingly.
> 

# Updating release pipeline

> Assumption: you are using Azure. For other service, the process is fairly similar.
> 

The last thing you need to do is update the release pipeline to provide the actual value for the instrumentation key (expected by Helm).

Open AZ, navigate to the release pipeline and go to the Variables section and add a new entry for the *ApplicationInsightsKey*: you can set the value directly or fetch it from the Key Vault (as done below).

**Notice:** you can set different values for the different environments by editing the *Scope* of the variable (Release scope sets the variable for all the environments).

Now, navigate to the *Task* section of the pipeline, select the *Deploy* task and update the *Set Values* field to make sure the Pipeline variable value is pulled: *appSettings.appInsightsKey=$(ApplicationInsightsKey)*: this will set the value for AppInsightsKey in Values.yaml file for Helm.

And that’s it: you are ready to go! Well done :) .

# Angular Universal (SSR)

If you have an Angular Universal application (using Server Side Rendering), in `deployment.yaml`you will need to mount the `/dist/browser/assets/app.config.json` folder as this is the assets folder for browser-side code.

You might also have to wrap the loadConfig() method from AppConfigService in platform check to ensure this code is only executed on the browser side. Like this:

Angular universal: request the config file only from client side.

*Alternatively*, you can read the app config data set by your pipeline on the server side of your app: in your `server.ts` file, you can read the config value as environment variables ( `process.env.APP_INSIGHT_KEY)`and do replace the content of `app.config.json` there. In this case, you do not need to do the replacement via `deployment.yaml` file.

# Resources