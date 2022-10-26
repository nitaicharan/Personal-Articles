# Building AKS Kubernetes with Terraform

Created: September 27, 2022 8:21 PM
Finished: No
Source: https://joachim8675309.medium.com/building-aks-with-terraform-662a61acb59c

![Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1du923nGqi0MM3p6KNbwytQ.png](Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1du923nGqi0MM3p6KNbwytQ.png)

This article covers provisioning a basic high-availability **[Kubernetes](https://kubernetes.io/)** cluster with **[AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)** (**[Amazon Kubernetes Service](https://azure.microsoft.com/en-us/services/kubernetes-service/)**) using **[Terraform](https://www.terraform.io/)** with the `azurerm` provider.

Previously, I covered how to do this using a shell script wrapper around **[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/)**. For this article, all cloud resources (**[Azure](https://azure.microsoft.com/)** and **[Kubernetes](https://kubernetes.io/)**) will be created with `terraform`.

**NOTE**:

[Medium](https://medium.com/u/504c7870fdb6?source=post_page-----662a61acb59c--------------------------------)

code blocks have been challenging (nearly impossible) to copy text, so to help readers, I have been moving code snippets to gists.

# Requirements

## The Tools

These are the baseline tools needed to work with **[Azure](https://azure.microsoft.com/)** and **[Kubernetes](https://kubernetes.io/)**.

- **[Kubernetes client tool](https://kubernetes.io/docs/tasks/tools/)** (`kubectl`): command line tool that interacts with Kubernetes API
- **[Terraform](https://www.terraform.io/)** (`terraform`): command tool to provision Azure cloud resources and deploy Kubernetes applications.

# Project Setup

The following file structure will be used:

```
~/azure_basic/
├── demos
│   └── hello-kubernetes
│       ├── main.tf
│       ├── provider.tf
│       └── terraform.tfvars
├── main.tf
├── provider.tf
└── terraform.tfvars
```

You can create this with the following commands:

For the rest of the article, commands will be executed from the **`$HOME**/azure_basic` directory.

```
cd ~/azure_basic
```

# Provision Azure resources

![Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1H5qiaZbAAAJcKdiMSUdJ4A.png](Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1H5qiaZbAAAJcKdiMSUdJ4A.png)

This process will provision an **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** cluster, where **[Azure](https://azure.microsoft.com/)** will then create a new resource group and provision cloud resources needed for the **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** cluster.

For a basic HA **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** cluster, two modules will be used: **[group](https://github.com/darkn3rd/blog_tutorials/tree/master/kubernetes/aks/series_0_provisioning/terraform/modules/group)** and **[aks](https://github.com/darkn3rd/blog_tutorials/tree/master/kubernetes/aks/series_0_provisioning/terraform/modules/aks)**. These will be fetched from the blog source code repository.

## Create the Terraform scripts

Create the file `provider.tf` with the following contents:

Create the file `main.tf` with the following contents:

## Create the variable definition

Create `terraform.tfvars` with the following content:

These are some example values for this project that you should change as appropriate. If you have an existing resource group that you wish to use, change `create_group` to false, otherwise, **[Terraform](https://www.terraform.io/)** will attempt to create it.

## Provision AKS with Terraform

When ready, run the following commands:

**NOTE**: Currently there is no direct method to create dependencies between modules in **[Terraform](https://www.terraform.io/)**. Thus if you need a resource created before other modules are executed, then you will have to manually orchestrate this with the `-target` command-line argument.

## Interact with the Kubernetes cluster

In order to interact with the cluster, we need to setup a `KUBECONFIG`. Run these commands to populate a configuration with credentials and point environment variable `KUBECONFIG` to that file.

After this run the command to see the components installed on a vanilla cluster:

```
kubectl get all--all-namespaces
```

This should show something like the following:

![Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/18aZq7Nm0J3jDDet-cuY4sA.png](Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/18aZq7Nm0J3jDDet-cuY4sA.png)

## Exploring Kubernetes networking: Kubenet

Run the following command to see the `nodes` and `pods` and their corresponding IP addresses:

This should show something like the following:

![Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/16hpahNIN1Enh7g0t4Hj6xA.png](Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/16hpahNIN1Enh7g0t4Hj6xA.png)

This output shows both `nodes` and `daemonset` privileged `pods` running on the **[Azure VNET](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)** created with the cluster, while other `pods` are on an overlay networks created by `kubenet` network plugin.

## Exploring Kubernetes networking: Routes

The `kubenet` network plugin will need external routes configured. You can see this in action by looking are the Azure **[virtual network traffic routing](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)** with the following command:

This should show something like the following:

![Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1hTh5pdTd0npT9eEx9CbiqA.png](Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1hTh5pdTd0npT9eEx9CbiqA.png)

# Demo: hello-kubernetes

For this demo application, `hello-kubernetes` will be used, which will display the names of the pods and nodes.

## Create the provider script

For the `kubernetes` provider, you need to specify where to access credentials, such as `KUBECONFIG` credentials. As we created the cluster with Azure, we can use the `azurerm` provider to fetch the credentials.

Create the file `demos/hello-kubernetes/provider.tf` with the following contents:

## Create the main script

The main part of the script will deploy two **[Kubernetes](https://kubernetes.io/)** resources: `service` and `deployment`.

Create the file `demos/hello-kubernetes/main.tf` with the following contents:

## Create the variable definition

Create `demos/hello-kubernetes/terraform.tfvars` with the following content:

## Deploy `hello-kubernetes`

When ready, run the following commands to deploy `hello-kubernetes` demo and verify the results.

**NOTE**: Embedding is currently not working. Refer to this link:

This should show something like the following:

![Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1h7oQjL3nI1DrsZNaeQOCaw.png](Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1h7oQjL3nI1DrsZNaeQOCaw.png)

You can access the one of the pods using `port-forward`:

Afterward, you should see something like this with `http://localhost:8080`:

![Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1lQoTb12FX9Mq2sLYdqoi_w.png](Building%20AKS%20Kubernetes%20with%20Terraform%20facc20bd221b464f8b6f02c44f04e5f6/1lQoTb12FX9Mq2sLYdqoi_w.png)

# Cleanup

## Kubernetes: hello-kubernetes

If you would like to just delete `hello-kubernetes` application, you can do the following:

```
pushd demos/hello-kubernetes &&terraform destroy &&popd
```

## AKS Cluster

If you just want to delete the Kubernetes cluster and associated resources, e.g. load balancer, managed identity, **[VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)**, and **[NSG](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview)**, then run this command:

```
terraformdestroy
```

# Resources

These are resources that I have discovered along the way when using **[Terraform](https://www.terraform.io/)** and **[AKS](https://azure.microsoft.com/services/kubernetes-service/)**.

## Blog Source Code

## Tutorials

# Conclusion

This is a very basic tutorial covering how to get started with Terraform for Azure, **[AKS](https://azure.microsoft.com/services/kubernetes-service/)**, and **[Kubernetes](https://kubernetes.io/)**. This tutorial doesn’t go deep into the intricacies of provisioning **[AKS](https://azure.microsoft.com/services/kubernetes-service/)**, as this will be covered in the source code supplement, should you want to delve further into this topic.

## The Takeaways

Main takeaways:

Extra takeaways are exposure to:

## Where to go next?

For **[Azure](https://azure.microsoft.com/)** and **[AKS](https://azure.microsoft.com/services/kubernetes-service/)**, there are more advance scenarios when configure **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** to work with **[Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)**, **[Azure Container Registry](https://azure.microsoft.com/services/container-registry/)**, **[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview)**, **[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)**, and other resources. These require setting up **[identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)** or **[service principals](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)**, which are created from **[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)** **,** which is **[Kerberos](https://wikipedia.org/wiki/Kerberos_(protocol))** + **[LDAP](https://wikipedia.org/wiki/Lightweight_Directory_Access_Protocol)** under the hood.

On the **[Kubernetes](https://kubernetes.io/)** side, **[Terraform](https://www.terraform.io/)** is becoming popular for deploying applications and other configurations. For advance scenarios where you need to use templates, **[Terraform](https://www.terraform.io/)** is not very robust in this area.

As an alternative you can use **[Helm](https://helm.sh/)** charts or orchestrate charts with **[Helmfile](https://github.com/roboll/helmfile)**. These can be used within **[Terraform](https://www.terraform.io/)** using **[Helm provider](https://registry.terraform.io/providers/hashicorp/helm/latest/docs)** and **[Helmfile providers](https://registry.terraform.io/providers/mumoshu/helmfile/latest)**.

Thanks for following along.