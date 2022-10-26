# GrantBirki/k8s-kong-terraform

Created: May 19, 2022 8:57 AM
Finished: No
Source: https://github.com/GrantBirki/k8s-kong-terraform

# k8s-kong-terraform

# 

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/k8s.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/k8s.png)

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/kong.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/kong.png)

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/terraform.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/terraform.png)

Create a K8s cluster with a Kong Ingress using pure Terraform 
 Once deployed, a sample NGINX HTTP application will be up and running for you to test against

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/badge.svg](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/badge.svg)

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/badge%201.svg](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/badge%201.svg)

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/badge%202.svg](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/badge%202.svg)

## What you will create

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/2b50.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/2b50.png)

- A Kubernetes Cluster running on Azure Kubernetes Service ([AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/#overview))
- A K8s ingress controller using [Kong](https://konghq.com/)
- Grafana/Prometheus [dashboards](https://grafana.com/grafana/dashboards/7424) for viewing network metrics from Kong (made for you)
- A sample [NGINX](https://www.nginx.com/) application which serves HTTP requests (loadbalanced by Kong)
- (optionally) Enable TLS encryption on your external facing Kong ingress for security (using [cert-manager](https://cert-manager.io/docs/)!

## Prerequisites

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f6a9.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f6a9.png)

You will need a few things to use this project:

1. 
    
    An [Azure](https://azure.microsoft.com/en-us/free/) account (this project uses AKS)
    
2. 
    
    [tfenv](https://github.com/tfutils/tfenv) (for managing Terraform versions)
    
3. 
    
    [kubectl](https://kubernetes.io/docs/tasks/tools/) (for applying K8s manifests)
    
4. 
    
    [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
    
5.  
    
    A [Terraform Cloud](https://www.terraform.io/cloud) account to store your TF state remotely
    
    - See the `[terraform-cloud](https://github.com/GrantBirki/k8s-kong-terraform/blob/main/docs/terraform-cloud.md)` docs in this repo for more info (required if you are using Terraform Cloud)
6. 
    
    An Azure Service Principal for deploying your Terraform changes - [Create a Service Principal](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)
    
7. 
    
    Your Azure Service Principal will need `owner` permissions to your Azure Subscription. This is due to K8s needing to bind your ACR registiry to your K8s cluster with pull permissions - [Assign Roles to a Service Principal](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal?tabs=current)
    
8.   
    
    You will need to skim through the following files and edit the lines with "`(CHANGE ME)`" comments:
    
    - `[terraform\k8s-cluster\versions.tf](https://github.com/GrantBirki/k8s-kong-terraform/blob/main/terraform%5Ck8s-cluster%5Cversions.tf)`
    - `[terraform\k8s-cluster\variables.tf](https://github.com/GrantBirki/k8s-kong-terraform/blob/main/terraform%5Ck8s-cluster%5Cvariables.tf)`
    - `[terraform\k8s\k8s-cluster.tf](https://github.com/GrantBirki/k8s-kong-terraform/blob/main/terraform%5Ck8s%5Ck8s-cluster.tf)`
    
    > 
    > 
    > 
    > Example: Updating values with your own unique K8s cluster name and pointing to your own Terraform cloud workspaces
    > 

## Usage

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f4bb.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f4bb.png)

Build a K8s cluster with a single command!

> 
> 
> 
> Go make a coffee while this runs because it can take up to 15 minutes
> 

```
$ make build

🔨 Let's build a K8s cluster!
✅ tfenv is installed
✅ Azure CLI is installed
✅ kubectl is installed
✅ terraform/k8s-cluster/terraform.auto.tfvars.json exists
✅ terraform/k8s-cluster/terraform.auto.tfvars.json contains non-default credentials
🚀 Deploying 'terraform/k8s-cluster'...
⛵ Configuring kubectl environment
🔨 Time to build K8s resources and apply their manifests on the cluster!
✅ All manifests applied successfully
🦍 Kong LoadBalancer IP: 123.123.123.123
📊 Run 'script/grafana' to connect to the Kong metrics dashboard
✨ Done! ✨
```

The K8s cluster uses Kong as a Kubernetes Ingress Controller and comes with a sample NGINX backend to serve HTTP requests

To get the external IP of your `kong-proxy`, log into your Azure account and check your `Services and Ingresses` section of your newly deployed K8s cluster. You will see a link to the extranal IP of your new LoadBalancer to make an HTTP request for testing.

When you are done using your K8s cluster, you may destroy it by executing the following command:

```
$ make destroy

💥 Let's DESTROY your K8s cluster!
Continue with the complete destruction of your K8s cluster (y/n)? y
✅ Approval for destroy accepted
✅ tfenv is installed
✅ terraform/k8s-cluster/terraform.auto.tfvars.json exists
✅ terraform/k8s-cluster/terraform.auto.tfvars.json contains non-default credentials
💥 Destroying 'terraform/k8s-cluster'...
✨ Done! ✨
```

### Enabling TLS

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f512.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f512.png)

This is a bonus / expirmental section. It "works on my machine ™" but it will take a smidge of manual setup, knowledge of letsencrypt, DNS, etc

What you need first (pre-reqs):

- A domain name ([www.example.com](http://www.example.com/))
- A way to confirgure DNS records for your domain (route53, AzureDNS, etc)
- A working DNS cluster that has been built with `make build` (above) - Copy down your Kong Proxy IP

### Steps

These are a mix of steps and an outline of the `make enable-tls` helper script

1. Execute the following command: `make enable-tls` 
    - This will invoke a bash script which will swap around some files, prompt you for some input, and inject said input into K8s manifests via sed
2. It is recommended to say yes (y) to everything and enter the information requested
3. When prompted, create DNS records that point to your K8s cluster. You will need an A record that points to your Kong LoadBalancer ingress and a CNAME that maps to the A record at a minimum
4. When prompted, edit each listed K8s manifest file to your liking. This part requires you to have a bit of K8s knowledge in what you need to use and where. Each manifest file is commented to help you along!
5. The end of the script will run a full deployment of the cluster
6. It will take a few minutes for everything to settle and for your TLS certificates to be provisioned. Happy encryption!

## Project Folder Information

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f4c2.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f4c2.png)

- `script/` - Contains various scripts for deployments and maintenance
- `terraform/k8s-cluster` - The main terraform files for building the infrastructure of the K8s cluster. This folder contains configurations for the amount of K8s nodes, their VM size, their storage, etc
- `terraform/k8s/*` - Kubernetes deployment manifests and Terraform files for Kong, Grafana/Prometheus, and the NGINX example http server

## Purpose

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f4a1.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f4a1.png)

The purpose of this project/repo is to quickly build a minimal K8s cluster with Kong + Terraform to get a project going.

## Example Diagram

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f5fa.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f5fa.png)

The diagram below shows an example of what a K8s cluster would look like with this deployment.

> 
> 
> 
> Note: Rather than having a `kermit`, `cat`, and `dog` service - you would just have one service, the `nginx-example`
> 

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/k8s-kong-terraform.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/k8s-kong-terraform.png)

## Example Results

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f4ca.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f4ca.png)

Once your cluster is up and running the NGINX example will look like this:

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/nginx.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/nginx.png)

You can also view the Grafana dashboard either with `script/grafana` or by visiting your configured hostname when you configure TLS:

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/grafana.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/grafana.png)

## GitHub Actions

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/26a1.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/26a1.png)

Once you have successfully built your K8s cluster and tested its functionality, you can deploy it using CI/CD with GitHub actions!

To do so, check out the following documentation in this repo: `[github-actions](https://github.com/GrantBirki/k8s-kong-terraform/blob/main/docs/github-actions.md)`

## Contributing

![GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f469-1f4bb.png](GrantBirki%20k8s-kong-terraform%20d427190ebc9d442483f1a212463b95e0/1f469-1f4bb.png)

All contributions are welcome! If you have any questions or suggestions, please open an issue or fork this repo and create a pull request!