# How to manage angular application configurations for different environments with docker and kubernetes

Created: May 10, 2022 5:28 PM
Finished: No
Source: https://ychetankumarsarma.medium.com/how-to-manage-angular-application-configurations-for-different-environments-with-docker-and-8ff8c55a1c7d
Tags: #angular

[0*dhSbuLzwq8HWExQe](How%20to%20manage%20angular%20application%20configurations%20f%203370b62fc49d426aac07715abcc391c9/0dhSbuLzwq8HWExQe)

Photo by [Tim Easley](https://unsplash.com/@timeasley?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## Problem:

As containers are taking the software industry by storm, I decided to jump on the wagon and move my applications to containers. All of the applications are single page applications which talk to API’s.

Configuring API’s for different environments in containers is straightforward as these can read environment variables declared in the container. The main issue is how to configure angular applications in containers as these applications run in the browser and cannot read environment variables from the server using ***process.env***. The whole idea here is to configure the application’s environment-specific variables in containers during deployment instead of embedding them in an image and building an image per environment. After a while, I finally figured out a way that suits my needs. As there is no community defined approach on this, I am sharing my approach so it helps anyone in need.

> This approach can extend to any browser-based frameworks.
> 

I have a sample project written in angular to demonstrate this approach. As angular’s build approach doesn't satisfy Build Once, Deploy Everywhere rule, I decided to separate my application configuration in env.js using this very helpful article: [https://www.jvandemo.com/how-to-use-environment-variables-to-configure-your-angular-application-without-a-rebuild/](https://www.jvandemo.com/how-to-use-environment-variables-to-configure-your-angular-application-without-a-rebuild/)

Here is the link to my GitHub repo for your reference: [https://github.com/chetanku/frontend-env](https://github.com/chetanku/frontend-env)

I am using Docker to build my images and kubernetes to orchestrate them.

pipeline

**Solution:**

Configuration for the development environment in env.js:

```
window.__env.environmentName = “DEVELOPMENT”;})(this);
```

Build and run the docker images locally with the below command and browse to localhost:80:

```
docker build -f .docker/client.Dockerfile . -t frontend-env:latest
docker run -p 80:8080 --name frontend-env -d frontend-env:latest
```

http://localhost

If you are here, your development environment is all set.

To deploy the app in production, we have to change the env.js values dynamically to match production during deployment in kubernetes pods.

In order to do that, I created a new file called env_token.js and tokenized the environmentName value to ${ENV}.

```
window.__env.environmentName =“${ENV}”;})(this);
```

> You can push the image to your docker registry here, replace “IMAGE_REGISTRY” below with your image registry.
> 

To substitute this tokenized value, I used this very helpful Linux program **[envsubst](https://linux.die.net/man/1/envsubst)**. envsubst substitutes the tokenized value by the value of the environment variable. So here in the env_token.js file, ${ENV} will be replaced by the value of the environment variable ENV and saves it as env.js

```
envsubst < /usr/share/nginx/html/env_token.js > /usr/share/nginx/html/env.js”
```

I used a kubernetes resource called ***configmaps*** to declare environment variables in kubernetes pods.

```
kubectl create configmap test — from-literal=ENV=PRODUCTION
```

The kubernetes manifest file (.kube/client-kube.yaml) looks like below, please note the envsubst command is initiated after the pod is started which is declared under lifecycle.

client-kube.yaml

Create the pod in kubernetes cluster:

```
kubectl create -f .kube/client-kube.yaml
```

To test it out:

```
kubectl port-forward client-pod 8080:8080
```

and browse to localhost:8080, you should see DEVELOPMENT replaced by PRODUCTION

[http://localhost:8080](http://localhost:8080/)

You can also print out the environment variables running inside pod by running below command:

```
ENV=PRODUCTION
```

I hope this article helps others facing the same issue. Again, there might be many other ways of doing it but this method served my purpose.

I will soon write an article to show how I integrated this approach in my CI/CD pipeline using Azure DevOps.

Chao.