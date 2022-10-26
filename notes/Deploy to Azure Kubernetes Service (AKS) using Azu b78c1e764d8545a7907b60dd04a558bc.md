# Deploy to Azure Kubernetes Service (AKS) using Azure DevOps and Helm

Created: September 28, 2022 9:18 AM
Finished: No
Source: https://faun.pub/deploy-to-azure-kubernetes-service-aks-using-azure-devops-and-helm-77f3fa804ee7

Lately I was seeing lots of innovation and excitement on deployment automation using DevOps and Kubernetes. This have nothing to do with software development (I mean writing code). This is the business of the DevOps guys. Especially for someone like me who spent the last five years focusing on software development, and so much far away from anything Ops.

I was curious, but really curious so that I decided I want to learn more about these stuff. In this blog I want to share what I have learned.

I started with **Azure DevOps**, created the **CI/CD pipelines** for ASP.NET Core, Angular, Xamarin and Dockerized apps. I used **Azure** and App Center to deploy to.

Then I started thinking about the **Database** ! I found we can add it to the CI/CD pipelines. During the Build, we create the **.dacpac** file. Then we publish it during the release. I also used **ARM templates** (**IaC**) to automate the deployment of the infrastructure.

After that, I moved to **Kubernetes**, created a Docker container and deployed it to k8s. At first, I used **kubectl** to deploy manually. Then I automated the process of deployment through Azure DevOps.

This blog will serve as a continuation to these workshops. Should we start ? I hope you got a clear idea about k8s and you are ready to learn Helm Charts. So what is Helm ?

> Helm is the package manager for Kubernetes.
> 

It is npm for javascript, nuget for .NET and maven for Java. Helm makes it easier to deploy apps to Kubernetes. It can deploy multiple apps with multiple yaml files as one single package. It provides a way to solve dependencies between different resources. The package is called Chart. Charts could be shared and reused by different teams. If you want to deploy Cassandra, you’ll have a Chart for that. If you want to install SonarQube or Jenkins, then that becomes easy as running one single command. This means you don’t need to figure out how to install the web app, the database, mount a disk volume, connect web app to database, run a script that waits until the database starts...

The community creates the Charts and they are verified before they get published as official/stable. They are published here: [github.com/helm/charts/tree/master/stable](https://github.com/helm/charts/tree/master/stable)

## 1. Deployment to Kubernetes using kubectl and yaml files

The traditional way to deploy an application to k8s is through yaml files. These files contains k8s objects like Deployment, Service, ConfigMap, Secret… They describe the required configuration like which docker containers to use, the networking, the persisted volume, secret keys… The ones we use are here: [github.com/HoussemDellai/ProductsStoreOnKubernetes](https://github.com/HoussemDellai/ProductsStoreOnKubernetes)

![Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/1XOflySazizc2OcKVlqOeRw.png](Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/1XOflySazizc2OcKVlqOeRw.png)

Once we have the required yaml files, we can deploy them to Kubernetes using the following command:

> kubectl apply -f deployment.yaml
> 

## 2. Installing helm on client and Kubernetes cluster

Helm needs to be installed into both the machine (client) and Kubernetes cluster (server). For the client and depending on the OS, it could be installed by running one of the following commands:

```
Linux   :$ sudo snap install helm --classic
macOS   :$ brew install kubernetes-helm
Windows :$ choco install kubernetes-helm
```

For the server side (Helm is called Tiller in this land), it could be installed by:

```
$ helm init
```

Check the documentation here for more details on the install. *[docs.helm.sh/using_helm/#installing-helm](https://docs.helm.sh/using_helm/#installing-helm)*

## 3. Creating a sample Chart

![Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/1B_uAYCqaRo4FJ8TWhch9Cg.png](Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/1B_uAYCqaRo4FJ8TWhch9Cg.png)

To generate a sample Chart, we can use helm create command. This will create sample files for deployment, ingress and service. All they have values defined in values yaml file. The Chart file defines the version of the template. helmignore is the equivalent of gitignore, used to not include specific files into the package. Then, lint command to validate the template.

```
$ helm create samplechart
$ helm lint
```

## 4. Deployment to Kubernetes using Helm

With Helm, we will reuse the same k8s yaml files, but we’ll make them configurable and reusable. We’ll extract all the values that might change from one deployment to another. And we’ll put them inside a specific file that contains all these values defined as key-value pairs. This file is values.yaml.

Kubernetes configuration files (deployment, service, pvc, configmap, secret, pod…) can use these values to set its properties. The advantage is now more clear: we have one single file to configure k8s objects.

> Note: Another advantage of using Helm is to resolve dependencies between objects.
> 

Now these values should be substituted using Helm. For this reason, we need to install Helm on both the client machine and the cluster (server). Helm on the server is called Tiller. Here’s the official guide to install Helm (helm.sh/).

> Note: Helm on the server called Tiller will be removed by the v 3.0 of Helm.
> 

Now we can deploy the the k8s objects using Helm cli instead of kubectl cli. The first step is to create the package:

This will create a tgz compressed file that contains all the files with a specific version 0.1.0.

Then, to deploy the generated package, we run:

```
$ helm install productsstore
```

This will deploy the v 0.1.0 of the the package. To update the app, we make the changes to the files, generate a new package (v 0.1.1), and run:

```
$ helm upgrade productsstore
```

At this point you can check if the app was successfully deployed by running either viewing the kubernetes dashboard,

$ kubectl get {deployments/services/pvc/configmap} or using $ helm

## 5. Deployment to Kubernetes using Helm and Azure DevOps

We want to automate the deployment. The goal is that in each time we push new commit to the app’s source code, a new package will be created during the CI pipeline. And that package will be deployed during the CD pipeline.

The CI/CD pipelines are at the end a sort of sequence of command lines. This means that the task that will create the Helm package should run the command *$ helm package productsstore*.

But before that, we need to make sure Helm is installed on the client side. For that, we’ll use the task “Package and deploy Helm charts” with the command:

```
$ helm init --client-only
```

Now, we are ready to create the Helm package. We’ll use again the same previous task to generate the package, as following:

![Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/1rW3wXTHZ3zbAV95usq6rDA.png](Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/1rW3wXTHZ3zbAV95usq6rDA.png)

> Note: The first two tasks, will build and push a Docker container into Docker Hub. And the last task will copy and publish the Helm package to an artifact called drop.
> 

We are done with the CI/Build pipeline. We can run the pipeline using the Queue button and check from the console view that Helm was installed and the package was generated sucessfully.

Let’s move to the CD/Release pipeline. In this stage, we want to get the package published during the Build and deploy it to Kubernetes. Let’s start creating the pipeline by choosing the template “Deploy an application to a Kubernetes cluster by using its Helm chart.”

After configuring the connection to AKS cluster, we need to go through 3 steps. First, we need to install helm in both client and server (Tiller). That’s the role of the first task which will install 2.11.0. Then, we need to run *helm init*, to configure helm on client side. Finally, we run *helm upgrade* with the specified package.

![Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/1zfqCQgvKYaw-3QJpOHXlOg.png](Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/1zfqCQgvKYaw-3QJpOHXlOg.png)

> Note: Despite we already installed Helm on client side during the Build pipeline, we need to install it again during the Release. That is because sometimes it is not the same machine that runs both Build and release.
> 

Now we are ready to deploy our app by running the Release pipeline.

**Join our community Slack and read our weekly Faun topics ⬇**

[Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/0oSdFkACJxs5iy1oR](Deploy%20to%20Azure%20Kubernetes%20Service%20(AKS)%20using%20Azu%20b78c1e764d8545a7907b60dd04a558bc/0oSdFkACJxs5iy1oR)

## If this post was helpful, please click the clap 👏 button below a few times to show your support for the author! ⬇