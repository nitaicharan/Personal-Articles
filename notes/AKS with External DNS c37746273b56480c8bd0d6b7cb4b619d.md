# AKS with External DNS

Created: September 28, 2022 9:20 AM
Finished: No
Source: https://joachim8675309.medium.com/extending-aks-with-external-dns-3da2703b9d52

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1c-dtAkyYKp2SzIUM_MB1zw.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1c-dtAkyYKp2SzIUM_MB1zw.png)

This article covers using **[ExternalDNS](https://github.com/kubernetes-sigs/external-dns)** to automate updating DNS records when applications are deployed on **[Kubernetes](https://kubernetes.io/)**. This is needed if you wish to use a public endpoint and would prefer a friendlier DNS name rather than a public IP address.

This article will configure the following components:

# Articles in the Series

These articles are part of a series, and below is a list of articles in the series.

# Requirements

## Azure Subscription

You will need get an **[Azure](https://azure.microsoft.com/)** subscription and **[Sign in with Azure CLI](https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli)**.

## Domain name

For this guide you can can use a *public domain name*, and point that domain or a subdomain to the **[Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)** zone. This guide will use the fictional name `example.com` as an example.

Alternatively, you can use a *private domain name*, such as `example.internal`. This will require using either local domain server, editing the local `/etc/hosts` file, using an SSH jump host or VPN to access the **[Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)** service, or a combination of these.

## Required Tools

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1hLryfJOgFjXHsLUG1T4Pyw.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1hLryfJOgFjXHsLUG1T4Pyw.png)

These tools are required.

- **[Azure CLI tool](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)** (`az`): command line tool that interacts with Azure API
- **[Kubernetes client tool](https://kubernetes.io/docs/tasks/tools/)** (`kubectl`): command line tool that interacts with Kubernetes API
- **[Helm](https://helm.sh/)** (`helm`): command line tool for “*templating and sharing Kubernetes manifests*” (**[ref](https://tanzu.vmware.com/developer/guides/kubernetes/helmfile-what-is/)**) that are bundled as Helm chart packages.
- **[helm-diff](https://github.com/databus23/helm-diff)** plugin: allows you to see the changes made with `helm` or `helmfile` before applying the changes.
- **[Helmfile](https://github.com/roboll/helmfile)** (`helmfile`): command line tool that uses a “*declarative specification for deploying Helm charts across many environments*” (**[ref](https://tanzu.vmware.com/developer/guides/kubernetes/helmfile-what-is/)**).

## Optional Tools

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1YBMvFQE5uLfrdn2CrpIhSg.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1YBMvFQE5uLfrdn2CrpIhSg.png)

I highly recommend these tools:

- **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)** (`sh`), e.g. **[GNU Bash](https://www.gnu.org/software/bash/)** (`bash`) or **[Zsh](https://www.zsh.org/)** (`zsh`): these scripts in this guide were tested using either of these shells on macOS and Ubuntu Linux.
- **[curl](https://curl.se/)** (`curl`): tool to interact with web services from the command line.
- **[jq](https://stedolan.github.io/jq/)** (`jq`): a JSON processor tool that can transform and extract objects from JSON, as well as providing colorized JSON output greater readability.

# Project Setup

As this project has a few moving parts (**[Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)**, **[AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)**, **[ExternalDNS](https://github.com/kubernetes-sigs/external-dns)** with example applications **[Dgraph](https://dgraph.io/)** and **[hello-kubernetes](https://github.com/paulbouwer/hello-kubernetes)**), these next few will help keep things consistent.

## Project File Structure

The following structure will be used:

```
~/azure_externaldns/
├── env.sh
├── examples
│   ├── dgraph
│   │   └── helmfile.yaml
│   └── hello
│       └── helmfile.yaml
└── helmfile.yaml
```

With either **[Bash](https://www.gnu.org/software/bash/)** or **[Zsh](https://www.zsh.org/)**, you can create the file structure with the following commands:

## Project Environment Variables

Setup these environment variables below to keep things consistent amongst a variety of tools: **`[helm](https://helm.sh/)`**, **`[helmfile](https://www.terraform.io/)`**, **`[kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)`**, **`[jq](https://stedolan.github.io/jq/)`**, **`[az](https://docs.microsoft.com/cli/azure/install-azure-cli)`.**

If you are using a **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)**, you can save these into a script and source that script whenever needed. Copy this source script and save as `env.sh`:

You will be required to change **`AZ_DNS_DOMAIN`** to a domain that you have registered, as `example.com` is already owned. Make sure you transfer domain control to **[Azure DNS](https://azure.microsoft.com/services/dns/)**.

Additionally, you can use the defaults or opt to change values for **`AZ_RESOURCE_GROUP`**, **`AZ_LOCATION`**, and **`AZ_CLUSTER_NAME`**.

This `env.sh` file will be used for the rest of the project. When finished with the necessary edits, source it:

```
source env.sh
```

# Provisioning Azure Resources

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1GqerFDpxCVDu6IcnZsHrQg.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1GqerFDpxCVDu6IcnZsHrQg.png)

Azure Resources

The Azure cloud resources can be created with the following steps:

**[AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)** (**[Azure Kubernetes Service](https://azure.microsoft.com/en-us/services/kubernetes-service/)**) configured with **[managed identity](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)** enabled, which is now enabled by default.

**NOTE**: Earlier guides on the public Internet may document the older name of ***[Managed Identity](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)*** called ***[MSI](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)*** (***[Managed Service Identity](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)***). These are the same thing.

**NOTE**: Earlier guides on the public Internet, may document a process of creating a **[service principal](https://docs.microsoft.com/en-us/azure/aks/kubernetes-service-principal)** with a client secret (password). While this can still be used, this process is more complex no longer needed with **[managed identities](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)**.

## Verifying Azure DNS Zone

Gather information on a particular domain with using the built-in `query` flag with **[JMESPath](https://jmespath.org/)** syntax:

```
az network dns zone list\
  --query "[?name=='$AZ_DNS_DOMAIN']"--output table
```

This should something like the following (changing the domain name to the one you specified of course):

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1-lMW5AcbEJ30h1mPdI6ShQ.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1-lMW5AcbEJ30h1mPdI6ShQ.png)

## Verifying Azure Kubernetes Service

When completed, you should be able to see resources already allocated in **[Kubernetes](https://kubernetes.io/)** with this command:

```
kubectl get all--all-namespaces
```

The results should be similar to this:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1IXVYmcnriO5AxcNcDYxREQ.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1IXVYmcnriO5AxcNcDYxREQ.png)

# Authorizing access Azure DNS

The **[AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)** cluster must be configured to allow `external-dns` pod to access access **[Azure DNS](https://azure.microsoft.com/services/dns/)** zone. This can be done using the *kubelet identity*, which is the user assigned **[managed identity](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)** that assigned to the nodepools (managed by **[VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)**) before their creation.

📔 **NOTE**: A ***[managed identity](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)*** (previously called *MSI* or *managed service identity*) is a wrapper around **[service principals](https://docs.microsoft.com/azure/aks/kubernetes-service-principal?tabs=azure-cli)** to make management simpler. Essentially, they are mapped to an Azure resource, so that when the Azure resource no longer exists, the associated **[service principal](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)** will be removed automatically.

⚠️ **WARNING**: Using the *kublet identity* may be fine for limited test environments, this **SHOULD NEVER BE USED IN PRODUCTION**. This violates the **[principal of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege)**. Alternatives would configuring access are **[AAD Pod Identity](https://github.com/Azure/aad-pod-identity)** or the more recent **[Workload Identity](https://azure.github.io/azure-workload-identity/docs/)**.

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1s8mii_eAPCEpZSq6a7uRKg.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1s8mii_eAPCEpZSq6a7uRKg.png)

ExternalDNS using Kubelet Identity

First we want to get the *scope*, that has a format like this:

```
/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/
```

## Attach role to AKS (kubelet identity object id)

Fetch the **`AZ_DNS_SCOPE`** and **`AZ_PRINCIPAL_ID`**, and then grant access grant access to this specific **[Azure DNS](https://azure.microsoft.com/services/dns/)** zone:

## Verify Role Assignment

You can verify the results with the following commands:

This should show something like the following:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1e-obcnU6Gd9Oaa7fjUKVgQ.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1e-obcnU6Gd9Oaa7fjUKVgQ.png)

# Installing External DNS

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1KFXISXcs9NL7MRNoCvAEIQ.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1KFXISXcs9NL7MRNoCvAEIQ.png)

Kubernetes components

Now comes the fun part, installing the automation so that services with endpoints can automatically register records in the **[Azure DNS](https://azure.microsoft.com/services/dns/)** zone when deployed.

## **Using Helmfile**

Copy the following below and save as `helmfile.yaml`:

Make sure that appropriate environment variables are setup before running this command: **`AZ_RESOURCE_GROUP`**, **`AZ_DNS_DOMAIN`**, **`AZ_TENANT_ID`**, **`AZ_SUBSCRIPTION_ID`**. Otherwise, this script will fail.

Once ready, simply run:

```
helmfile apply
```

## Verify external-dns configuration

As a sanity check in case things go wrong, you can verify the configuration in the `azure.json` file.

```
kubectl get secret external-dns \
--namespace kube-addons \
--output jsonpath="{.data.azure\.json}" |base64 --decode
```

This should show something like:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1clJyV0l_7hJX7usfHeYeLQ.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1clJyV0l_7hJX7usfHeYeLQ.png)

Verify ExternalDNS configuration

The `tenantId` and `suscriptonId` are obviously obfuscated. This should match the same values set the environment variables **`AZ_TENANT_ID`** and **`AZ_SUBSCRIPTION_ID`** after running `source env.sh`.

## Testing ExternalDNS is Running

You can test that the `external-dns` pod is running with:

If there are *errors* in the logs about authorization, you know immediately that the setup is not working. You’ll need to revisit that the appropriate access was added to a role and attached to the correct *service principal* that was created on **[VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)** node pool workers.

# Example using hello-kubernetes

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1inQF9x4vKGZP1nB8-2rtyg.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1inQF9x4vKGZP1nB8-2rtyg.png)

hello-kubernetes additional Resources and Components

The **[hello-kubernetes](https://github.com/paulbouwer/hello-kubernetes)** is a simple application that prints out the pod names. This application can demonstrate that automation with **[ExternalDNS](https://github.com/kubernetes-sigs/external-dns)** and **[Azure DNS](https://azure.microsoft.com/services/dns/)** have worked correctly.

A service of `LoadBalancer` type will be configured with the required annotation to tell **[ExternalDNS](https://github.com/kubernetes-sigs/external-dns)** the desired DNS `A` record to configure. **[ExternalDNS](https://github.com/kubernetes-sigs/external-dns)** will scan services for this annotation, and then trigger the automation.

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1ji_gVRwg-jzdrjWv9qo-IA.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1ji_gVRwg-jzdrjWv9qo-IA.png)

hello-kubernetes with a new Public IP resource

## Deploy hello-kubernetes using helmfile

Copy and paste the following manifest template below as `examples/hello/helmfile.yaml`:

We can deploy this using the following command:

```
source env.shhelmfile --fileexamples/hello/helmfile.yaml apply
```

## Verify Hello Kubernetes is deployed

```
kubectl get all --namespace hello
```

You similar resources like this deployed:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1QZy3dG7Ie4PpJeutJN5T6g.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1QZy3dG7Ie4PpJeutJN5T6g.png)

hello-kubernetes deployment

## Verify DNS Records were created

Run the following command to verify that the records were created by `external-dns`:

This should look something like the following:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1wHdd1D0sr-P2ne2BbiA_Eg.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1wHdd1D0sr-P2ne2BbiA_Eg.png)

Verify DNS record updates

To verify a specific record entries, this can be done with a **[JMESPath](https://jmespath.org/)** query:

The results should look something like the following:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1D_DVVqXQf2vrEUbIS_DGZA.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1D_DVVqXQf2vrEUbIS_DGZA.png)

## Verify Hello Kubernetes works

After a few moments, you can check the results `http://hello.example.com` (substituting `example.com` for your domain).

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1uQlNV0iI7pBNeDMdDWiRyw.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1uQlNV0iI7pBNeDMdDWiRyw.png)

## Cleanup Hello Kubernetes

The following will delete `kubernetes-hello` and the pubic ip address:

```
helm delete hello-kubernetes--namespace hello
```

# Example using Dgraph

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1dMIJHkLJinlXEL4GdY0GlQ.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1dMIJHkLJinlXEL4GdY0GlQ.png)

Dgraph additional resources and components

**[Dgraph](https://dgraph.io/)** is a distributed graph database and has a helm chart that can be used to install **[Dgraph](https://dgraph.io/)** into a **[Kubernetes](https://kubernetes.io/)** cluster. You can use either `helmfile` or `helm` methods to install **[Dgraph](https://dgraph.io/)**.

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1YODvpqoMN5WrxcDzy_Lkkw.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1YODvpqoMN5WrxcDzy_Lkkw.png)

Dgraph Alpha + Ratel with 2 Public IPs

**About the Illustration**: The above will show the relationship with networking resources and Kubernetes `service` configuration using an external load balancer. In AKS, a single load balancer is assigned to the cluster, which will map multiple public IP addresses to the appropriate Kubernetes `services`.

In this scenario, `20.99.224.105` will be mapped to the Dgraph Alpha `service` for ports `8080` and `9080`, while `20.99.224.114` will map port `80` to Dgraph Ratel `service`. Additionally, Azure DNS will have `alpha` and `ratel` address records that point to these two respective public IP addresses.

## Securing Dgraph

Normally, you would only have the database available through a private endpoints, not available on the public Internet. However for this *demonstration* purposes, public endpoints through the service of type `LoadBalancer` will be used.

We can take precaution to add a *whitelist* or an *allow list* that will contain your IP address as well as **[AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)** IP addresses used for pods and services. You can do that in **[Bash](https://www.gnu.org/software/bash/)** or **[Zsh](https://www.zsh.org/)** with the following commands:

## Deploy Dgraph with Helmfile

Copy the following gist below and save as `examples/dgraph/helmfile.yaml`:

When ready, run the following:

```
helmfile --fileexamples/dgraph/helmfile.yaml apply
```

## Verify Dgraph is deployed

Check that the services are running:

```
kubectl --namespace dgraph get all
```

This should output something similar to the following:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/15KUU3Gx3S93ERMMMpRa6xg.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/15KUU3Gx3S93ERMMMpRa6xg.png)

Dgraph deployment with LoadBalancer endpoints

## Verify DNS record updates

Run the following command to verify that the records were created by `external-dns`:

This should additional `ratel` and `alpha` records in the list:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1-koBfrgNVzd1wAQsc8Zukw.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1-koBfrgNVzd1wAQsc8Zukw.png)

To verify a specific address record entries (again using the spiffy **[JMESPath](https://jmespath.org/)** query):

With the addition of `ratel` and `alpha`, there should be three public IP addresses now:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1Ct4FcuG2rDIAEO2-JHDksQ.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1Ct4FcuG2rDIAEO2-JHDksQ.png)

## Verify Dgraph Alpha health check

Verify that the **[Dgraph](https://dgraph.io/)** Alpha is accessible by the domain name (substituting `example.com` for your domain):

```
curl --silent http://alpha.example.com:8080/health |jq
```

This should output something similar to the following:

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1Eti4hlty3FzB7KyA-az_kQ.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1Eti4hlty3FzB7KyA-az_kQ.png)

/health (HTTP)

## Test solution with Dgraph Ratel web user interface

After a few moments, you can check the results `http://ratel.example.com` (substituting `example.com` for your domain).

In the dialog for **`Dgraph Server Connection`**, configure the domain, e.g. `http://alpha.example.com:8080` (substituting `example.com` for your domain)

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1RiMVtwPwRH3vBiexzjmoMA.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1RiMVtwPwRH3vBiexzjmoMA.png)

From there, you can run through some tutorials like **[https://dgraph.io/docs/get-started/](https://dgraph.io/docs/get-started/)** to create a small Star Wars graph database and run some queries.

![AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1XxbsgZQ2v-DILZy-jQvCVw.png](AKS%20with%20External%20DNS%20c37746273b56480c8bd0d6b7cb4b619d/1XxbsgZQ2v-DILZy-jQvCVw.png)

## Cleanup Dgraph resources

You can remove **[Dgraph](https://dgraph.io/)** resources, public ip address, and external disks with the following:

```
helm delete demo--namespace dgraph
kubectl delete pvc--namespace dgraph--selector release=demo
```

# Cleanup the Project

You can cleanup resources that can incur costs with the following:

## Remove EVERYTHING with one command

This command is dangerous. Verify you are deleting only the designated resource group.

```
az group delete--resource-group $AZ_RESOURCE_GROUP
```

## Remove just the AKS Cluster

This will remove only the **[AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)** clusters and associated resources:

```
az aks delete \
--resource-group$AZ_RESOURCE_GROUP \
--name$AZ_CLUSTER_NAME
```

## Remove just the Azure DNS Zone

```
az network dns zone delete \
--resource-group$AZ_RESOURCE_GROUP \
--name$AZ_DNS_DOMAIN
```

# Resources and Further Reading

Here are some resources that I have come across that may be useful.

## Blog Source Code

- **[Azure DNS Automation](https://joachim8675309.medium.com/azure-linux-vm-with-dns-e54076bab296)**: how to setup Azure DNS Zone to be public domain or subdomain.

## External DNS

## Tools

## Azure Identity

## Azure DNS

## **Azure Kubernetes Service**

## Helm Charts

# Document History

- **2021年09月11日**: added more verification instructions; changed `westus` to `westus2` as `westus` doesn’t support HA; corrected illustration where only 1 LB is used for all external LB allocations under AKS; added illustrations on K8S components and Azure resources used.
- **2021年09月05日**: converted multi-line code blocks to gists as difficult to copy text from Medium.
- **2021年06月28日**: removed `envsubt` & `terraform` for simplicity

# Conclusion

On the surface, installing this seems easy:

As you can see, it is a little more complex, as configuring cloud resources, both **[Kubernetes](https://kubernetes.io/)** and **[Azure](https://azure.microsoft.com/)**, can zigzag through a number of tools.

## Takeaways

The important takeaways from this article include:

Some extra takeaways are exposure to:

## Where to go from here?

Here are some more advanced topics around either external-dns or provisioning AKS:

In any event, I hope this is useful and best of success in your **[AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)** journey.