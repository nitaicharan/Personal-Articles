# Environment Management In Node.js

Created: August 3, 2022 9:00 AM
Finished: No
Source: https://rehansattar.dev/environment-management-in-nodejs
Tags: #nodejs

[Environment%20Management%20In%20Node%20js%2074994b8d1e5b44899bc46e32d590c6f7/image](Environment%20Management%20In%20Node%20js%2074994b8d1e5b44899bc46e32d590c6f7/image)

## A guide to managing multiple configuration environments and variables throughout the development process.

**Assalam u Alaikum & Hello Everyone! 👋**

Hope you all are good ✨🤗

## Introduction

Environment management is an essential component in backend development. Storing database credentials, third-party API keys and much more confidential information is important for security reasons.

There are a lot of techniques to manage the environment configurations. The most common way of managing this is via using **Environment Variables.** There are also a lot of third-party tools and vaults that help you to store confidential information.

In this post, we will learn how to manage different environments(production, stage, and development) in Node.js.

Let's start 🚀

## Creating A Node Project

Let's create a simple node application by using `npm init -y`

`npm init` is going to generate a `package.json` file for our project. And the `-y` flag is simply saying yes to all the steps in creating `package.json` file.

Here are the contents of `package.json`:

```
{
  "name": "environment-management",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}

```

You can modify the other fields as per need. For this post, the most important section of the `package.json` file is `scripts`.

## Introduction to Environment Variables

An environment variable is a variable whose value is set outside the program, typically through a functionality built into the operating system or microservice. An environment variable is made up of a name/value pair, and any number may be created and available for reference at a point in time.

## Structure Of Environment Variables

We create environment variables in name/value pair like:

## Default Configuration Support Using Node.js

In Node.js, you can pass the environment variables when you are running any javascript file using node.

This is the default behavior of how the node binds the variables to the `process.env` object. By default, there are a lot of environment variables defined.

Let's define our first Environment Variable using the command interface.

Let's create a file `app.js` at the root of your project and run it using node.

```
> app.js

console.log('Welcome to configuration management using Node.js');

```

Now, we can run the application using:

Output:

Now, let's pass an environment variable via command interface and run the file something like this:

Now, node will understand that there is an environment variable `GREETINGS` of which the value is `Hi` and I have to bind this variable with the `process.env` object.

If we console the `process.env.GREETINGS` in our app.js file like this:

The output will be:

The above method of passing environment variables is fine but it's not scalable.

## Environment Management Using `dotenv`

[dotenv](https://www.npmjs.com/package/dotenv) is one of the most highly used package to manage environment variables.

It's very rich in functionality and supports loading environment files with paths.

### Working with `dotenv`

Let's install `dotenv` by simply running

After the installation, create a `.env` file at the root of your project. `dotenv` will automatically pick this file and read the environment variables from it. After reading, it will assign all the environment variables to the `process.env` object.

Let's create a `.env` file and write a simple environment variable in it.

To use `dotenv` in our node application, import the package at the root of the project or the entry point of your server then call the `config` method of it.

```
console.log(process.env.GREETINGS); // undefined (dotenv is not loaded)

require('dotenv').config(); // load dotenv, all the environment variables are loaded now.

console.log(process.env.GREETINGS); // Hello World!

```

In this way, we can add as many environment variables into the `.env` file as we want. All of them will be parsed by `dotenv`.

### Multiple Environments Using `dotenv`

We have looked into how `dotenv` work, and how it loads the environment variables. But this is ideal when you have only one environment to work with.

In real-world projects, we have multiple environments like production, stage, QA and development, etc.

To handle this use case, we can use the `path` option in the `config` method.

Let's create three different files for the production, stage, and development environment. We can add a simple variable of `CONTEXT` in it.

```
$ touch .env.production

```

> 
> 
> 
> .evn.production
> 

```
$ touch .env.stage

```

> 
> 
> 
> .evn.stage
> 

```
$ touch .env.development

```

> 
> 
> 
> .evn.development
> 

Now we have environment variable files for every environment, we can load them according to the environment.

Let's load the development environment files. We can use the same traditional command pattern to provide our node app in the context of the development environment.

Let's modify the `script` section of the `package.json` file as:

```
 "scripts": {
    "start:dev": "ENVIRONMENT=development node app.js",
    "start:prod": "ENVIRONMENT=production node app.js",
    "start:stage": "ENVIRONMENT=stage node app.js"
  }

```

We have used the default method of loading environment variables to pass the environment context to our node app.

Now in the `app.js` file, we can create a small function that can return the environment file path based on the `ENVIRONMENT` variable passed in the command line. Note that the `path` option takes the **absolute path of the environment file**, not the relative. That's why we can use the `__dirname` to get the absolute path.

```
function getEnvironmentPath() {
  const environment = process.env.ENVIRONMENT;
  const environmentMap = {
    production: '.env.production',
    stage: '.env.stage',
    development: '.env.development'
  };
  return `${__dirname}/${environmentMap[environment] || '.env'}`;
}

```

Now we can call the `getEnvironmentPath` function within the `config` function of the `dotenv`.

```
require('dotenv').config({ path: getEnvironmentPath() });

console.log('Context: ', process.env.CONTEXT);

```

Let's test our code by running the respective commands.

Boom 💥 We are done! 🥳

Let' me know in the comments about how you manage your environments in nodejs 🚀

## Conclusion

It's very important to note that managing environments is a vast topic. This post is a simple guideline for engineers on how they can manage different environments during the development phase of the product.

In many cases, `dotenv` is not recommended in production deployments. You can always tweak the configuration according to it. The goal is to enable you to take effective decisions and understand how environmental management works.

That's it, folks! Thank you! ✨

### Subscribe to my newsletter

Read articles from **Rehan Sattar** directly inside your inbox. Subscribe to the newsletter, and don't miss out.

### Did you find this article valuable?

Support **Rehan Sattar** by becoming a sponsor. Any amount is appreciated!