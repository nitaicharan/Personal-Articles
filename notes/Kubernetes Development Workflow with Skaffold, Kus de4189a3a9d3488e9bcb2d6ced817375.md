# Kubernetes Development Workflow with Skaffold, Kustomize and Kind

Created: April 7, 2022 5:35 PM
Finished: Yes
Finished 📅: April 22, 2022
Source: https://webera.blog/kubernetes-development-workflow-with-skaffold-kustomize-and-kind-12d4a72a2cbf
Subjects: kustomize, skaffold

![Kubernetes%20Development%20Workflow%20with%20Skaffold,%20Kus%20de4189a3a9d3488e9bcb2d6ced817375/1Em9l1PJail-qOrPhWaZzqg.png](Kubernetes%20Development%20Workflow%20with%20Skaffold,%20Kus%20de4189a3a9d3488e9bcb2d6ced817375/1Em9l1PJail-qOrPhWaZzqg.png)

# Introduction

Hello guys, in this tutorial we will learn how to prepare our local environment to develop applications and automate our deployments in Kubernetes using Skaffold and kustomize let’s get started.

Lately, I have been working on a project in which we used Kubernetes to manage the deployments of the microservices the great difficulty of the team was to set up an identical local environment to the one we have in production, after some researches we decided to explore skaffold just to work in our environment locally.

In our example we will use two simple APIs developed in Golang and Gorilla Mux as our intention is not to go deeper into the development of this API below we have the link to our final project:

**Final project link** ([https://github.com/wearewebera/k8s-setup-develop](https://github.com/wearewebera/k8s-setup-develop))

# Introduction to skaffold

- What is a Skaffold?
    
    Skaffold not only automates the process of building and deploying an application on k8s, but it is also able to monitor every change made to the code so it automatically performs the whole build and deployment process again.
    
- When is necessary?
    
    This tool is not only for remote use but also when we need to automate our local development flow using Minikube or Kind.
    

Our first step will be to install the necessary tools, if you already have Docker installed on your machine proceed to the next step, otherwise you can click on the link below:

# Installing Kind and Skaffold

In this tutorial we will create our local cluster with Kind ([https://kind.sigs.k8s.io/](https://kind.sigs.k8s.io/)), that we can use all the resources that we would use in a remote cluster and our final configuration will be to configure scaffold to monitor the changes in our files, we need to follow some steps to carry out the installation on our machine.

- How install kind?
    
    We have to download the binary using the curl and then moving that binary to the PATH of our machine, OSX users can choose Homebrew ([https://brew.sh/](https://brew.sh/)), I will assume that you are using Linux so you can run the code below in your terminal.
    
    ```bash
    curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.10.0/kind-linux-amd64
    chmod +x ./kind
    mv ./kind /<some dir in your PATH>/kind
    ```
    
    After executing the above steps, we can verify if Kind was successfully installed by running the command `kind --version`
    
- How install Skaffold?
    
    With kind installed what we now need is to install Skaffold ([https://skaffold.dev](https://skaffold.dev/)), to perform the installation just perform the same process as before with kind, download the binary and move it to PATH
    
    ```bash
    curl -Lo skaffold https://storage.googleapis.com/skaffold/releases/latest/skaffold-linux-amd64 && \
    ```
    
- How create a cluster with Kind?
    
    Now that we have Kind and Skaffold installed we will make use of it and create our cluster by running the command:
    
    ```bash
    kind create cluster —name demo-store
    ```
    

# Install kubectl binary with curl on Linux

- How install the Kubernetes client?
    
    You can download and install the latest release with the command:
    
    ```bash
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    
    #Test to ensure the version you installed
    kubectl version --client
    ```
    
    After install kubectl, run `kubectl config use-context demo-store` to set the default context to the cluster we just created.
    

# Installing Kustomize

- How install Kubtomize?
    
    Kustomize promotes configuration reuse with an inheritance and patch strategy. This approach is similar to object-oriented programming, where you can define a parent class and then extend that parent class and replace everything you need to modify for that specific environment. that you need to modify, users can run kustomize directly or, using the command `kubectl -k` to access its features, the command will download the binary to be moved to the PATH of our machine.
    
    ```bash
    curl -s "https://raw.githubusercontent.com/\ kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"| bash
    mv ./kustomize /some-dir-in-your-PATH/kustomize
    ```
    

# Dockerizing the Microservices (Customers and Products)

In this step I will show you how we can dockerize our applications written in Golang, we will use a multi-stage build and also explore the docker cache, below we have the result of our Dockerfile:

```bash
# Builder image
FROM golang:1.13-alpine3.11 as builder
RUN mkdir /build
ADD . /build/
WORKDIR /build

RUN go mod download
RUN CGO_ENABLED=0 GOOS=linux go build -a -o store-products .

# Generate clean, final image for end users
FROM alpine:3.11.3
COPY --from=builder /build/store-products .

EXPOSE 8080

# Executable
ENTRYPOINT [ "./store-products" ]
```

- In the first stage we build our application but before we copy only the files **go.mod**, **go.sum** and **main.go** to the build folder and install the dependencies of our application,
    
    ```yaml
    # Builder image
    FROM golang:1.13-alpine3.11 as builder
    RUN mkdir /build
    ADD . /build/
    WORKDIR /build
    ```
    
- then we execute the build and generate a binary with the name `store-products` the same will be done next with our customers service.
    
    ```yaml
    RUN go mod download
    RUN CGO_ENABLED=0 GOOS=linux go build -a -o store-products .
    ```
    
- Next, we copy the binary generated in the previous stage and execute it.
    
    ```yaml
    # Generate clean, final image for end users
    FROM alpine:3.11.3
    COPY --from=builder /build/store-products .
    ```
    

The command below can be executed if you want to analyze if your application after the above process is being executed correctly inside the container:

```bash
# Build
docker build -t store/customers .

# Run docker image at port 8080
docker run -d -p 8080:8080 store/customers
```

# Generate deployment files using imperative command

Following our process, we now need to create the Kubernetes manifests to deploy our application, I suppose you have cloned the repository mentioned at the beginning of this tutorial.

You can write all the specifications for your deployment or simply run the command:

```bash
kubectl create deploy customers --image=store/customers --dry-run -o yaml > customers-definition.yaml

kubectl create deploy products --image=store/products --dry-run -o yaml > products-definition.yaml
```

Below is a representation of the file generated after executing the above command:

*customers-definition.yaml*

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: customers
  name: customers
spec:
  replicas: 1
  selector:
    matchLabels:
      app: customers
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: customers
    spec:
      containers:
      - image: store/customers
        name: customers
        resources: {}
status: {}
```

Now we need to create our service, in the file generated above we will remove some lines from our deployment that we don’t need at the moment and we will the configuration add in this same file for our service that will look like this:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: customers
  name: customers
spec:
  replicas: 1
  selector:
    matchLabels:
      app: customers
  template:
    metadata:
      labels:
        app: customers
    spec:
      containers:
      - image:  store/customers
        name: appapiVersion: v1
kind: Service
metadata:
  labels:
    app: customers
  name: customers-service
spec:
  type: ClusterIP
  ports:
    - name: http
      port: 8000
      targetPort: 8000
  selector:
    app: customers
```

With our deployment and service configuration files created, the next step is to configure the Ingress Controller.

# Configuring ingress controller

> [Unlike other types of controllers that run as part of the binary kube-controller-manager, Ingress controllers are not automatically started with a cluster. Use this page to choose the input controller implementation that best suits your cluster.](https://webera.blog/kubernetes-development-workflow-with-skaffold-kustomize-and-kind-12d4a72a2cbf)
> 

# Why do we need an Ingress Controller?

It will abstract the routing complexity, it accepts traffic from outside the cluster and load balances for our running pods.

For this tutorial we chose the NGINX Ingress controller, so below we have the command that you must execute on your machine’s terminal:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
```

The ingress will create its own namespace and inside it will be your load balancer and the controllers, it configures two types of services for us one is Load Balancer and the other is ClusterIP.

Load Balancer is something that is external to the cluster it is mapping to localhost and ClusterIP remains inside the Cluster.

Once the Ingress Controller is configured the time has come to create the definition of our ticket for the customers and products service, I will copy the example of [Ingress Nginx documentation](https://kubernetes.github.io/ingress-nginx/user-guide/basic-usage/) and make some adjustments.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: ingress-store
  annotations:
    # use the shared ingress-nginx
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: store.local
    http:
      paths:
      - path: /v1/customers
        backend:
          serviceName: customers-service
          servicePort: 8080
      - path: /v1/products
        backend:
          serviceName: products-service
          servicePort: 8080
```

# Host File configuration

To access the **store.local** we need to add it to our **etc/hosts**, at the end of the file add **127.0.0.1 store.local** this will cause that when accessing the **store.local** url it will first be checked in your **etc/hosts** file if the domain is not found it does a search on the external DNS.

# Configuring Skaffold and Kustomize

We have reached the final stage of our saga of setting up a local development workflow with the resources of the cluster closest to the production environment, now we will configure skaffold to listen to all our modifications and automate our deployment in the cluster. also configure kustomize to organize all resources in a namespace instead of using the default, below we have kustomize and then skaffold:

*k8s/local/base/kustomization.yaml*

```yaml
resources:
  - customers-definition.yaml
  - products-definition.yaml
```

*k8s/local/kustomization.yaml*

```yaml
resources:
  - ../base
  - ingress.yaml
```

*./skaffold.yaml*

```yaml
apiVersion: skaffold/v2beta7
kind: Config
build:
  artifacts:
  - image: store/customers
    context: .
    docker:
      dockerfile: customers/Dockerfile
    sync:
      infer:
        - 'customers/*'
  - image: store/products
    context: .
    docker:
      dockerfile: products/Dockerfile
    sync:
      infer:
        - 'products/*'deploy:
  kustomize:
    paths:
    - k8s/local
```

After configuring Skaffold, Run the command **skaffold dev** and make some changes to **customers/main.go** and save. A new version with changes to your application will be built and automatically deployed by Skaffold in the Kubernetes cluster**.**

![Kubernetes%20Development%20Workflow%20with%20Skaffold,%20Kus%20de4189a3a9d3488e9bcb2d6ced817375/1en0RzSfIJiW_08ayCgUxgw.png](Kubernetes%20Development%20Workflow%20with%20Skaffold,%20Kus%20de4189a3a9d3488e9bcb2d6ced817375/1en0RzSfIJiW_08ayCgUxgw.png)

# Conclusion

With Skaffold, we gain productivity in our development by automating our deployments and also the ability to easily mirror the production deployment in our local cluster.

# References

[https://kind.sigs.k8s.io/docs/user/quick-start/](https://kind.sigs.k8s.io/docs/user/quick-start/)

[https://kubernetes.github.io/ingress-nginx/user-guide/](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)

[https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)

[https://skaffold.dev/docs/install/](https://skaffold.dev/docs/install/)

And that’s pretty much! I hope you enjoyed the tutorial and as always, feel free to comment in the section below, and contact us if you have any questions or ideas!

See you on #webera in a few weeks for another exciting story!

![Kubernetes%20Development%20Workflow%20with%20Skaffold,%20Kus%20de4189a3a9d3488e9bcb2d6ced817375/1Y1BdqNSmhFGNaNAcIfJ5_Q.jpeg](Kubernetes%20Development%20Workflow%20with%20Skaffold,%20Kus%20de4189a3a9d3488e9bcb2d6ced817375/1Y1BdqNSmhFGNaNAcIfJ5_Q.jpeg)

👋 Join [WEBERA](https://medium.com/wearewebera) today and receive similar stories every week in your inbox! ️ Receive your weekly dose of must-read technology stories, news, and tutorials