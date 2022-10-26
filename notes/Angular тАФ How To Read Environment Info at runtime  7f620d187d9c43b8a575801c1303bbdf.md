# Angular — How To Read Environment Info at runtime for CI/CD

Created: May 10, 2022 5:28 PM
Finished: No
Source: https://medium.com/bb-tutorials-and-thoughts/angular-how-to-read-environment-info-at-runtime-for-ci-cd-9a788478aa9b
Tags: #angular

[0*l6jhY5rB8BalN_nO](Angular%20%E2%80%94%20How%20To%20Read%20Environment%20Info%20at%20runtime%20%207f620d187d9c43b8a575801c1303bbdf/0l6jhY5rB8BalN_nO)

Photo by [Tim Gouw](https://unsplash.com/@punttim?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Angular provides configuration options at build time which means you need to define different environment files for each environment and Angular takes appropriate configuration while building the project by providing `--configuration` flag to the `ng build.` [You can check out this article about reading environment info during build time.](https://medium.com/bb-tutorials-and-thoughts/how-to-read-environment-specific-variables-in-angular-9f2cee0b2b4)

But, [the twelve-factor methodology](https://medium.com/bb-tutorials-and-thoughts/cloud-native-development-a-diagrammatic-approach-to-the-twelve-factor-methodology-b13f9b4a129b) and today's DevOps strategies suggest that we need to build once and run everywhere which means you will have only one chance to provide configuration file. Angular configuration options are not enough. You need to provide configuration or environment info at runtime. In this post, we will see how we can achieve that and read configuration settings or environment info at runtime.

- ***Example Project***
- ***The problem we are facing***
- ***Solution***
- ***Implementation***
- ***How To Debug***
- ***Summary***
- ***Conclusion***

# Example Project

Here is an example project for the demonstration. You can clone and run it on your machine.

```
// for local development
npm install
npm start
```

This is a simple Angular project which loads the configuration file ***app.config.json*** from the ***/assets*** folder.

**app.config.json**

We are using APP_INITIALIZER to load this **app.config.json** file before bootstrapping and use those settings. Here are the **app.module.ts and app.service.ts files.**

**app.module.ts and app.service.ts**

Once you load the settings and you can read these settings in **app.component.ts** like below.

**app.component.html and app.component.ts**

Based on the configuration, you can see the header color, heading, and table. For example, If it is a development environment header color is black and heading is development. You can see a similar screen as below.

**development screen**

If you change the backgroundColor and heading to red and production respectively. You can see a screen as below.

**production screen**

# ***The problem we are facing***

The twelve-factor methodology suggests that we need to ***build once run anywhere*** but in this case, we are building for each environment. For simplicity, we are considering only two environments **development** and **production.** Since we are serving the Angular app with NGINX and we can only provide configuration at build time rather than run time. We can’t even provide environment variables at release since the browser doesn’t read those environment variables either.

**Passing configuration at build time**

We are building for each environment because we had to pass environment-specific information at build time. We need to find a way that we should pass this info at run time. If we pass at the run time all we need to build once and run anywhere as we see in the below diagram.

**Passing configuration at run time**

# Solution

Let’s see how we can solve this problem. One way to solve this is to read the browser URL with the window ***location.href*** and put all the configuration in the app and load appropriate configuration based on some specific part of the URL such as dev, prod, etc.

If you using Java or Nodejs with the Angular app we can get this configuration form the server before bootstrapping the app with the APP_INITIALIZER. But how do we do this if we are using NGINX as the webserver?

We can use Kubernetes configmap to inject configuration into the Pods volume which is mounted at **/usr/share/nginx/html/assets** folder so that the Angular app gets it before bootstrapping the app with the help of **APP_INITIALIZER.** Let’s look at the below diagram to understand better.

**Reading configuration at run time using configmap**

# ***Implementation***

Let’s implement the solution with the Kubernetes configMap object. ConfigMap object makes your containers portable by decoupling configuration from them. This is how it works.

The first thing we need to do is build the docker image and push it to DockerHub. Here is the multi-stage Dockerfile which builds the Angular app in the first stage and take those static assets and put it in the root folder of NGINX.

**Dockerfile**

These are the instructions to build the Docker image and push it to the DockerHub. You can actually see it on the DockerHub in the following image. This is a public image you can directly pull it from the registry.

```
// build the image
docker build -t bbachin1/envdemo .// list images
docker images// login and push it to docker hub
docker login
docker push bbachin1/envdemo
```

Docker Hub

Now we need to create a deployment, service and configmap objects. we are putting all these objects in one file called ***manifest.yml.*** We are creating Configmap first with the required config.json. If you look at the deployment object Kubernetes pulls the above image ***bbachin1/envdemo*** from the Docker Hub and creates 5 replicas. Finally, we have a service object with the Nodeport type which exposes to the outside world.

We have loaded configmap into the volume which is mounted on the path ***/usr/share/nginx/html/assets/*** folder. We create all these objects in the namespace development.

**manifest.yml**

Here are the instructions to create objects and verify them.

```
// create objects
kubectl create -f manifest.yml// delete objects
kubectl delete -f manifest.yml// get the deployment
kubectl get deploy -n development// get the service
kubectl get svc -n development// get the pods
kubectl get po -n development
```

Get the Kubernetes public address from this command `kubectl cluster-info` and get the port from the service object `kubectl get svc -n development` and access the application running in the development namespace with this address `http://<public address of minikube>:<svc port>/appui`

**service port and public address**

In the above case, you can access the application at `http://192.168.64.6:31935/appui` Make sure you change from https to http. Notice that all the configuration is loaded from the configmap such as header backgroundColor, title, etc.

**Running the deployment on Minikube**

Let’s create the production deployment from the manifest-prod.yml file and follow the above steps to run the app on your local.

**manifest-prod.yml**

```
// create objects
kubectl create -f manifest-prod.yml// delete objects
kubectl delete -f manifest-prod.yml// get the deployment
kubectl get deploy -n production// get the service
kubectl get svc -n production// get the pods
kubectl get po -n production
```

The service in the production namespace is running at the port ***31633.***

**service is running at port 31633**

**Running the deployment on Minikube**

# ***How To Debug***

These are some of the debugging options if you have any problem implementing this solution.

First, we need to verify the configmap is created in the right way and in the right namespace.

```
// verify if configmap is created or not
kubectl get cm -n development// verify the data in the configmap
kubectl describe cm -n development
```

Once you verify the configmap. You can then check the mounted volume that is loaded with the configmap.

```
// get the one of the pod
kubectl get po -n development// exec into one of the pod
kubectl exec -it <podname> /bin/sh -n development
# cd /usr/share/nginx/html/envapp/assets
# cat app.config.json
```

**app.config.json**

# ***Summary***

- Angular provides configuration options at build time which means you need to define different environment files for each environment.
- We need to build once run everywhere is the recommended strategy.
- We can’t use the Angular environment option if we want to build once and deploy everywhere since we have to provide a separate configuration for each environment.
- Configmap provides a solution to decouple the configuration from the running the containers.
- If you are serving your Angular application with NGINX and you need a way to pass configuration at runtime then, ConfigMaps is the easiest solution.
- You should load the configmap into the volume which can be mounted on host path and Angular get that JSON from that path.
- You can delete the existing configmap and recreate one and the changes can be reflected in the running container without restarting the pods.

# Conclusion

Use Configmaps if you want to decouple the configuration from your containers and inject appropriate configuration at runtime.