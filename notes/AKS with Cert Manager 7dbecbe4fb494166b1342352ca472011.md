# AKS with Cert Manager

Created: September 28, 2022 9:19 AM
Finished: No
Source: https://medium.com/geekculture/aks-with-cert-manager-f24786e87b20

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1e_h47YM0M9_YTI6Nz3f8KQ.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1e_h47YM0M9_YTI6Nz3f8KQ.png)

This article details how to secure web traffic using **[TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security)** with a certificate from a trusted **[CA](https://en.wikipedia.org/wiki/Certificate_authority)** and a public domain. This will use **[Let’s Encrypt](https://letsencrypt.org/)** through a popular **[Kubernetes](https://kubernetes.io/)** add-on **[cert-manager](https://cert-manager.io/)**.

In this article we’ll use the following components:

⚠️ **WARNING**: This uses the kublet identity, an identity or principal that is assigned to all nodes in the cluster, to access Azure DNS. This means that all containers on the cluster will have access to read/write DNS records for the domain. While this may be fine for limited test environments, this **SHOULD NEVER BE USED IN PRODUCTION**. This violates the [principal of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Alternatives would configuring access are **[AAD Pod Identity](https://github.com/Azure/aad-pod-identity)** or the more recent **[Workload Identity](https://azure.github.io/azure-workload-identity/docs/)**.

## Overview of cert-manager

One common scenario for securing web applications or services, it to have encrypted traffic with **[TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security)** certificates, where the encryption will be terminated at the load balancer. Before the arrival of **[Kubernetes](https://kubernetes.io/)**, **[nginx](https://www.nginx.com/)** was a popular solution for this process.

On the **[Kubernetes](https://kubernetes.io/)** platform, the **[ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)** resource through the **[ingress-nginx](https://github.com/kubernetes/ingress-nginx)** controller will perform the **[TLS termination](https://en.wikipedia.org/wiki/TLS_termination_proxy)**. This allows secure communication from the user to the endpoint, while everything behind the endpoint is not encrypted allowing for easier configuration, monitoring, and debugging.

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1mx6EBxp0sE3VVzCidPmqSw.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1mx6EBxp0sE3VVzCidPmqSw.png)

cert-manager process

The **[cert-manager](https://cert-manager.io/)** addon will monitor **[ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)** events, and then install or update a certificate for use with encryption of web traffic. This happens in the following steps:

1. a challenge (`DNS01`) is made to read/write a DNS record on Azure DNS to demonstrate the user owns the domain.
2. When the challenge is satisfied, a certificate is then issued by an **[ACME](https://en.wikipedia.org/wiki/Automated_Certificate_Management_Environment)** CA.
3. **[cert-manager](https://cert-manager.io/)** will then create the secret with the certificate, which will be used by the ingress controller to secure web traffic.

# Articles in the series

These articles are part of a series, and below is a list of articles in the series.

## Previous Articles

***AKS + ingress-ginx + external-dns*:** In the previous article I covered how to deploy **[ingress-nginx](https://github.com/kubernetes/ingress-nginx)** along with **[external-dns](https://github.com/kubernetes-sigs/external-dns)**:

***AKS + external-dns*:** In the first article in this series, I covered how to deploy **[external-dns](https://github.com/kubernetes-sigs/external-dns)** for use with service of `LoadBalancer` type.

# Requirements

These are some logistical and tool requirements for this article:

## Registered domain name

When securing web traffic with **[TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security)** certificates that are trusted (or in other words, a certificate issued by a trusted **[CA](https://en.wikipedia.org/wiki/Certificate_authority)**), you will need to own a *public domain name*, which can be purchased from a provider for about $2 to $20 per year.

A fictional domain of `example.com` will be used as an example. Thus depending on the examples used, there would be, for example, `hello.example.com`, `ratel.example.com`, and `alpha.example.com`.

## Required tools

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1hLryfJOgFjXHsLUG1T4Pyw.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1hLryfJOgFjXHsLUG1T4Pyw.png)

These tools are required for this article:

- **[Azure CLI tool](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)** (`az`): command line tool that interacts with Azure API
- **[Kubernetes client tool](https://kubernetes.io/docs/tasks/tools/)** (`kubectl`): command line tool that interacts with Kubernetes API
- **[Helm](https://helm.sh/)** (`helm`): command line tool for “*templating and sharing Kubernetes manifests*” that are bundled as Helm chart packages.
- **[helm-diff](https://github.com/databus23/helm-diff)** plugin: allows you to see the changes made with `helm` or `helmfile` before applying the changes.
- **[Helmfile](https://github.com/roboll/helmfile)** (`helmfile`): command line tool that uses a “*declarative specification for deploying Helm charts across many environments*”.

## Optional tools

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1YBMvFQE5uLfrdn2CrpIhSg.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1YBMvFQE5uLfrdn2CrpIhSg.png)

I highly recommend these tools:

- **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)** (`sh`) such as **[GNU Bash](https://www.gnu.org/software/bash/)** (`bash`) or **[Zsh](https://www.zsh.org/)** (`zsh`): these scripts in this guide were tested using either of these shells on macOS and Ubuntu Linux.
- **[curl](https://curl.se/)** (`curl`): tool to interact with web services from the command line.
- **[jq](https://stedolan.github.io/jq/)** (`jq`): a JSON processor tool that can transform and extract objects from JSON, as well as providing colorized JSON output greater readability.

# Project setup

As this project has a few moving parts (**[Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)**, **[AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)**, **[cert-manager](https://cert-manager.io/)**, **[external-dns](https://github.com/kubernetes-sigs/external-dns)**, **[ingress-nginx](https://github.com/kubernetes/ingress-nginx)**) with example applications **[Dgraph](https://dgraph.io/)** and **[hello-kubernetes](https://github.com/paulbouwer/hello-kubernetes)**, these next few will help keep things consistent.

## Project file structure

The following structure will be used:

```
~/azure_cert_manager/
├── env.sh
├── examples
│   ├── dgraph
│   │   └── helmfile.yaml
│   └── hello
│       └── helmfile.yaml
└── helmfile.yaml
```

With either **[Bash](https://www.gnu.org/software/bash/)** or **[Zsh](https://www.zsh.org/)**, you can create the file structure with the following commands:

```
mkdir -p ~/azure_cert_manager/examples/{dgraph,hello} && \
cd ~/azure_cert_manager

touch \
  env.sh \
  helmfile.yaml \
  ./examples/{dgraph,hello}/helmfile.yaml
```

These instructions from this point will assume that you are in the `~/azure_ingress_nginx` directory, so when in doubt:

```
cd ~/azure_cert_manager
```

## Project environment variables

Setup these environment variables below to keep things consistent amongst a variety of tools: **`[helm](https://helm.sh/)`**, **`[helmfile](https://www.terraform.io/)`**, **`[kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)`**, **`[jq](https://stedolan.github.io/jq/)`**, **`[az](https://docs.microsoft.com/cli/azure/install-azure-cli)`.**

If you are using a **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)**, you can save these into a script and source that script whenever needed. Copy this source script and save as `env.sh`:

# Azure components

Below are the **[Azure](https://azure.microsoft.com/)** specific configuration that is required. You can use this below, material from previous articles, or resources you provisioned with your own automation.

If you are provisioning **[Azure](https://azure.microsoft.com/)** cloud resources using your own automation, you will need to keep these requirements in mind:

- *domain control*: either domain control must be passed to the **[Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)** domain from your domain provider, or if using a sub domain, point NS records to **[Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)** name servers hosting the sub-domain.

## Resource group

In **[Azure](https://azure.microsoft.com/)**, resources are organized under resource groups.

```
source env.shaz group create\
  --resource-group${AZ_RESOURCE_GROUP} \
--location${AZ_LOCATION}
```

## Cloud resources

For simplicity, you can create the resources needed for this project with the following:

```
source env.shaz network dns zone create\
  --resource-group${AZ_RESOURCE_GROUP} \
--name${AZ_CLUSTER_NAME}az aks create \
--resource-group${AZ_RESOURCE_GROUP} \
--name${AZ_CLUSTER_NAME} \
--generate-ssh-keys \
--vm-set-type VirtualMachineScaleSets \
--node-vm-size${AZ_VM_SIZE:-Standard_DS2_v2} \
--load-balancer-sku standard \
--enable-managed-identity \
--node-count 3 \
--zones 1 2 3az aks get-credentials \
--resource-group${AZ_RESOURCE_GROUP} \
--name${AZ_CLUSTER_NAME} \
--file${KUBECONFIG:-$HOME/.kube/config}
```

You will need to transfer domain management to **[Azure DNS](https://azure.microsoft.com/services/dns/)** for root domain like `example.com`, or if you are using sub-domain like `dev.example.com`, you’ll need to update DNS namespace records to point to **[Azure DNS](https://azure.microsoft.com/services/dns/)** name servers. This process is fully detailed as well as how to provision the equivalent with Terraform in **[Azure Linux VM with DNS](https://joachim8675309.medium.com/azure-linux-vm-with-dns-e54076bab296)** article.

For a more robust script on provisioning **[Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)**, see **[Azure Kubernetes Service: Provision an AKS Kubernetes Cluster with Azure CLI](https://joachim8675309.medium.com/azure-kubernetes-service-b89cc52b7f02)** article.

## Authorizing access Azure DNS

We need to allow access to the ***[Managed Identity](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)*** installed on **[VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)** node pool workers to the **[Azure DNS](https://azure.microsoft.com/services/dns/)** zone. This will allow any pod running a **[Kubernetes](https://kubernetes.io/)** worker node to access the **[Azure DNS](https://azure.microsoft.com/services/dns/)** zone.

**NOTE**: A ***[Managed Identity](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)*** is a wrapper around service principals to make management simpler. Essentially, they are mapped to a **[Azure](https://azure.microsoft.com/)** resource, so that when the **[Azure](https://azure.microsoft.com/)** resource no longer exists, the associated **[service principal](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)** will be removed.

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1hlk35pxjgovLAxMl00BhqQ.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1hlk35pxjgovLAxMl00BhqQ.png)

Managed Identity authorized to access Azure DNS

Run these commands below to extract the scope and service principal object-id and grant access using these commands:

```
source env.shexport AZ_DNS_SCOPE=$(
az network dns zone list \
--query "[?name=='$AZ_DNS_DOMAIN'].id"\
    --output tsv
)exportAZ_PRINCIPAL_ID=$(
  az aks show\
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_CLUSTER_NAME \
--query "identityProfile.kubeletidentity.objectId" \
    --output tsv
)az role assignment create \
--assignee "$AZ_PRINCIPAL_ID" \
--role "DNS Zone Contributor" \
--scope "$AZ_DNS_SCOPE"
```

# Kubernetes components

The **[Kubernetes](https://kubernetes.io/)** add-ons can be installed with the following script below.

## Install cert-manager

Copy this script below and save as `helmfile.yaml`:

Once ready, simply run:

```
source env.shhelmfile apply
```

## Install cert-manager clusterissuers

Copy the following and save as `issuers.yaml`:

There will need to be a few seconds before the **[cert-manager](https://cert-manager.io/)** pods are ready and online. When ready, run this:

```
export ACME_ISSUER_EMAIL="<your-email-goes-here>"
source env.sh
helmfile--file issuers.yaml apply
```

# Kubernetes example: hello-kubernetes

The **[hello-kubernetes](https://github.com/paulbouwer/hello-kubernetes)** is a simple application that prints out the pod names. The **[helmfile](https://github.com/roboll/helmfile)** script below, which is simply raw **[Kubernetes](https://kubernetes.io/)** manifests folded into a helm chart so that we can use dynamic values, will do the following:

- deploy a `Deployment` to manage 3 pods
- deploy a `Service` that points to the pods
- deploy an `Ingress` (using **[ingress-nginx](https://github.com/kubernetes/ingress-nginx))** that configures a route to direct traffic to the `Service`. using a **[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)** hostname, e.g. `hello.example.com`.

The ingress resource will also do the following magical automation:

- instruct **[external-dns](https://github.com/kubernetes-sigs/external-dns)** to set up a record in **[Azure DNS](https://azure.microsoft.com/services/dns/)** zone, e.g. `hello.example.com`.
- issue a certificate to secure traffic using the issuer specified through **[cert-manager](https://cert-manager.io/)**.

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1ZSKyfPSqYkLGcoTOgajpKA.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1ZSKyfPSqYkLGcoTOgajpKA.png)

Example: hello-kubernetes

Copy the file below and save as `examples/hello/helmfile.yaml`:

## Deploy with the staging issuer

For staging to test the functionality, run the following

```
source env.sh
export ACME_ISSUER=letsencrypt-staginghelmfile--file  ./examples/hello/helmfile.yaml apply
```

Then verify the resources were deployed:

```
kubectl get all,ing,certificate--namespace hello
```

This should look something like this:

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/15eYDEww3NmA6aVS9NmP5wA.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/15eYDEww3NmA6aVS9NmP5wA.png)

hello-kubernetes deploy

You can look at the events to see that the certificate was issued successfully or if any issues occurred:

```
kubectl describe ingress--namespace hello
kubectl describe certificate--namespace hello
```

When all resources are ready, test the solution with (substituting `example.com` for your domain):

```
curl --insecure --silent --include https://hello.${AZ_DNS_DOMAIN}
```

**NOTE**: For staging environment, a certificate from an untrusted private CA will be used, so the argument `--insecure` or `-k` is needed.

When completed, remove the existing solution:

```
helm delete hello-kubernetes--namespace hello
```

## Deploy with the prod issuer

When satisfied the solution is working, we can try production issuer. The reason why we do this is in two phases, is because ACME servers have an extreme limit on requests.

Deploy using the production issuer with the following:

```
source env.sh
export ACME_ISSUER=letsencrypt-prod
helmfile--file ./examples/hello/helmfile.yaml apply
```

After a few moments, you can check the results `https://hello.example.com` (substituting `example.com` for your domain).

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1bxULNtOEg2H4Z3KM1fmH3w.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1bxULNtOEg2H4Z3KM1fmH3w.png)

# Kubernetes example: Dgraph

**[Dgraph](https://dgraph.io/)** is a distributed graph database and has a helm chart that can be used to install **[Dgraph](https://dgraph.io/)** into a **[Kubernetes](https://kubernetes.io/)** cluster. You can use either `helmfile` or `helm` methods to install **[Dgraph](https://dgraph.io/)**.

What is nice about this example is that it will deploy two endpoints through a single Ingress: one for the Dgraph Ratel graphical user interface client (**[React](https://reactjs.org/)**) and the database service itself Dgraph Alpha.

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1lImQZLEpeovABW3elHhcxw.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1lImQZLEpeovABW3elHhcxw.png)

Example: Dgraph Alpha + Dgraph Ratel

## Securing Dgraph

In a production scenario, public endpoints should be secured, especially a backend database, but for the keep things simple for demonstration, the endpoint will not be secured.

We can add some level of security on the **[Dgraph Alpha](https://github.com/dgraph-io/dgraph)** service itself by adding an *allow list* (also called a *whitelist*):

```
# get AKS pod and service IP addresses
DG_ALLOW_LIST=$(az aks show \
--name$AZ_CLUSTER_NAME \
--resource-group$AZ_RESOURCE_GROUP | \
jq -r '.networkProfile.podCidr,.networkProfile.serviceCidr' | \
  tr '\n' ','
)# append home office IP address
MY_IP_ADDRESS=$(curl --silent ifconfig.me)
DG_ALLOW_LIST="${DG_ALLOW_LIST}${MY_IP_ADDRESS}/32"
exportDG_ALLOW_LIST
```

## Deploy Dgraph

Copy the file below and save as `examples/dgraph/helmfile.yaml`:

In a similar fashion, we can test the solution in staging first, then try production.

## Deploy with the staging issuer

For staging to test the functionality, run the following

```
source env.sh
export ACME_ISSUER=letsencrypt-staginghelmfile --file./examples/dgraph/helmfile.yaml apply
```

Verify that the Dgraph services are all in a running state. This may take about a minute:

```
kubectlget all,ing,certificate--namespace dgraph
```

This should show something like the following:

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1h7H5nF1ixnapBVlJGH24xg.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1h7H5nF1ixnapBVlJGH24xg.png)

Dgraph Deployment with Certificate

Verify that the **[Dgraph Alpha](https://github.com/dgraph-io/dgraph)** is accessible by the domain name (substituting `example.com` for your domain):

```
curl --insecure --silenthttps://alpha.${AZ_DNS_DOMAIN}/health |jq
```

## Deploy with the prod issuer

For prod to test the functionality, run the following.

```
source env.sh
export ACME_ISSUER=letsencrypt-prod
helmfile --file./examples/dgraph/helmfile.yaml apply
```

Verify the certificate was updated with:

```
kubectl describe ingress--namespace dgraph
```

You should see an `updated Certificate` message:

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/16JvJWwn0Vp6H2S0n2sqhxQ.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/16JvJWwn0Vp6H2S0n2sqhxQ.png)

Ingress resource events

You can also see the certificate events with:

```
kubectl describe certificate--namespace dgraph
```

This should show events similar to this:

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1Jc-8N9W5MWGSB8xQ10nRmw.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1Jc-8N9W5MWGSB8xQ10nRmw.png)

Certificate resource events

Verify that the **[Dgraph Alpha](https://github.com/dgraph-io/dgraph)** is accessible by the domain name (substituting `example.com` for your domain):

```
curl --silenthttps://alpha.${AZ_DNS_DOMAIN}/health |jq
```

**NOTE**: Now that we are using public trusted certificates, instead of the private certificates in staging,`--insecure` (or `-k`) is no longer needed.

# Upload Data and Schema

There are some scripts adapted from tutorials **[https://dgraph.io/docs/get-started/](https://dgraph.io/docs/get-started/)** that you can down:

```
PREFIX=gist.githubusercontent.com/darkn3rd
RDF_GIST_ID=398606fbe7c8c2a8ad4c0b12926e7774
RDF_FILE=e90e1e672c206c16e36ccfdaeb4bd55a84c15318/sw.rdf
SCHEMA_GIST_ID=b712bbc52f65c68a5303c74fd08a3214
SCHEMA_FILE=b4933d2b286aed6e9c32decae36f31c9205c45ba/sw.schemacurl -sO https://$PREFIX/$RDF_GIST_ID/raw/$RDF_FILE
curl -sO https://$PREFIX/$SCHEMA_GIST_ID/raw/$SCHEMA_FILE
```

Once downloaded, you can then upload the schema and data with:

```
curl-s "https://alpha.$AZ_DNS_DOMAIN/mutate?commitNow=true" \
--request POST \
--header "Content-Type: application/rdf" \
--data-binary @sw.rdf | jqcurl-s "https://alpha.$AZ_DNS_DOMAIN/alter" \
--request POST \
--data-binary @sw.schema | jq
```

# Connect to Ratel UI

After a few moments, you can check the results `https://ratel.example.com` (substituting `example.com` for your domain).

In the dialog for **`Dgraph Server Connection`**, configure the domain, e.g. `https://alpha.example.com` (substituting `example.com` for your domain)

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1QeQaAqtzEDJCJLrgdwXrjg.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1QeQaAqtzEDJCJLrgdwXrjg.png)

## Test Using the Ratel UI

In the Ratel UI, paste the following query and click run:

```
{
me(func: allofterms(name, "Star Wars"),
           orderasc:release_date)
    @filter(ge(release_date, "1980")) {
name
release_date
revenue
      running_time
director {
name
      }
starring (orderasc:name) {
name
      }
  }
}
```

You should see something like this:

![AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1_yM4AgEbDzpgLcKIYaOXKQ.png](AKS%20with%20Cert%20Manager%207dbecbe4fb494166b1342352ca472011/1_yM4AgEbDzpgLcKIYaOXKQ.png)

# Cleanup the Project

You can cleanup resources that can incur costs with the following:

## Remove External Disks

Before deleting **[AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)** cluster, make sure any disks that were used are removed, otherwise, these will be left behind an incur costs.

```
############
# Delete the Dgraph cluster
############################################
helm delete demo--namespace dgraph############
# Delete external storage used by the Dgraph cluster
############################################
kubectl delete pvc--namespace dgraph--selector release=demo
```

**NOTE**: These resources cannot be deleted if they are in use. Make sure that the resources that use the PVC resources were deleted, i.e. **`helm** delete demo **--namespace** dgraph` .

## Remove the Azure Resources

This will remove the Azure resources:

```
############
# Delete the AKS cluster
############################################
az aks delete \
--resource-group$AZ_RESOURCE_GROUP \
--name$AZ_CLUSTER_NAME############
# Delete the Azure DNS Zone
############################################
az network dns zone delete \
--resource-group$AZ_RESOURCE_GROUP \
--name$AZ_DNS_DOMAIN
```

# Resources

Here are some resources I have come across in development of this article.

## Blog Source Code

## Articles

- **How to Set Up an Nginx Ingress with Cert-Manager on DigitalOcean Kubernetes**: [https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes)
- **Securing NGINX-ingress** (Tutorial): [https://cert-manager.io/docs/tutorials/acme/ingress/](https://cert-manager.io/docs/tutorials/acme/ingress/)
- **Create an HTTPS ingress controller on Azure Kubernetes Service (AKS)**: [https://docs.microsoft.com/azure/aks/ingress-tls](https://docs.microsoft.com/azure/aks/ingress-tls)
- **Certificate issuance with LetsEncrypt.org**: [https://azure.github.io/application-gateway-kubernetes-ingress/how-tos/lets-encrypt/](https://azure.github.io/application-gateway-kubernetes-ingress/how-tos/lets-encrypt/)

## Helm Chart (cert-manager)

- **ArtifactHub**: [https://artifacthub.io/packages/helm/cert-manager/cert-manager](https://artifacthub.io/packages/helm/cert-manager/cert-manager)
- **values.yaml**: [https://github.com/jetstack/cert-manager/blob/master/deploy/charts/cert-manager/values.yaml](https://github.com/jetstack/cert-manager/blob/master/deploy/charts/cert-manager/values.yaml)

## Documentation (cert-manager)

- **Configuration**: [https://cert-manager.io/docs/configuration/acme/](https://cert-manager.io/docs/configuration/acme/)

## Example Implementations

- **CloudPosse’s cert-manager helmfile**: [https://github.com/cloudposse/helmfiles/blob/master/releases/cert-manager/helmfile.yaml](https://github.com/cloudposse/helmfiles/blob/master/releases/cert-manager/helmfile.yaml#L135-L156)

# Conclusion

The main focus of this article was to secure a public facing website or application that has a web interface using **[Let’s Encrypt](https://letsencrypt.org/)** with an **[ACME issuer](https://cert-manager.io/docs/configuration/acme/)**. A part of this journey included installing the required ingress controller with **[ingress-nginx](https://github.com/kubernetes/ingress-nginx)** (**[OpenResty](https://openresty.org/en/)** under the hood) and automation for DNS record updates with **[external-dns](https://github.com/kubernetes-sigs/external-dns)**.

One last note on security, all pods running on the cluster will have the ability to update records in the **[Azure DNS](https://azure.microsoft.com/services/dns/)** zone as well as issue certificates, which validates using **[Azure DNS](https://azure.microsoft.com/services/dns/)** zone (for the `DNS01` challenge) as well. You can secure these further using **[aad-pod-identity](https://azure.github.io/aad-pod-identity/)**, so that only the pod has the appropriate credentials, allowing to apply the **[principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege)**. Note that this feature is currently a preview release for integration with **[AKS](https://azure.microsoft.com/services/kubernetes-service/)**.

With **[cert-manager](https://cert-manager.io/)**, there are so many configuration options. With the **[ACME** **issuer**](https://cert-manager.io/docs/configuration/acme/) used in this article, the **[DNS01](https://cert-manager.io/docs/configuration/acme/dns01/)** challenge used **[Azure DNS](https://azure.microsoft.com/services/dns/)**, but you are by no means limited to this one, as **[Route53](https://cert-manager.io/docs/configuration/acme/dns01/route53/)**, **[Cloud DNS](https://cert-manager.io/docs/configuration/acme/dns01/google/),** and **[CloudFlare](https://cert-manager.io/docs/configuration/acme/dns01/cloudflare/)** amongst other are supported, or alternatively use the **[HTTP01](https://cert-manager.io/docs/configuration/acme/http01/)** challenge. Besides **[ACME](https://cert-manager.io/docs/configuration/acme/)**, you could try out others CAs, such as **[Vault](https://cert-manager.io/docs/configuration/vault/)**, **[Venafi](https://cert-manager.io/docs/configuration/venafi/)**, CloudFlare **[origin-ca-issuer](https://github.com/cloudflare/origin-ca-issuer)**, **[FreeIPA](https://www.freeipa.org/)** and others. The possibilities are endless.

For this above and other reasons, it is no a surprise that **[cert-manager](https://github.com/jetstack/cert-manager)** is by far the most popular solution on **[Kubernetes](https://kubernetes.io/)** for certificate management.