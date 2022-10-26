# AKS with Calico Network Policies

Created: September 28, 2022 9:19 AM
Finished: No
Source: https://medium.com/geekculture/aks-with-calico-network-policies-8cdfa996e6bb

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1oeI24CEd4oJLyYKkuGGxQw.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1oeI24CEd4oJLyYKkuGGxQw.png)

**[Network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** in **[Kubernetes](https://kubernetes.io/)** are essentially firewalls for pods. By default, pods are accessible from anywhere with no protections. If you like to to use **[network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)**, you’ll need to install a **[network plugin](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/)** that supports this feature. This article will demonstrate how to use this feature with **[Calico](https://www.tigera.io/project-calico/)** network plugin on **[AKS](https://azure.microsoft.com/services/kubernetes-service/)**.

## Default Kubenet network plugin

The default network plugin for **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** as well as many other **[Kubernetes](https://kubernetes.io/)** implementations is **[kubenet](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet):**

> a very basic, simple network plugin, on Linux only. It does not, of itself, implement more advanced features like cross-node networking or network policy (ref)
> 

Though the **[kubenet](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet)** network plugin is limited, it affords some security as pods are on an overlay network behind a NAT. Thus, the pod cannot be reached from outside the cluster, unless the operator (you) configure a **[service](https://kubernetes.io/docs/concepts/services-networking/service/)** resource (`NodePort` or `LoadBalancer`) or an **[ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)** resource.

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1CtioDjNVL67lQrE-e8RgVg.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1CtioDjNVL67lQrE-e8RgVg.png)

**About the Diagram**: With **[kubenet](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet)**, traffic that is headed toward pods running on the same worker node, then it will just go to the local bridge `cbr0` to reach the target pod. If the destination is to a pod on another worker node, then traffic is sent outside of the node with IP forwarding, and using Azure UDR (user defined routes), will land on the correct worker node and then sent ownward to that node’s local bridge `cbr0`.

## Securing internal cluster traffic

> Why do we need such a granular control of network packets between containers? (ref. colleague)
> 

Defining network policy allows you to enable things like **[defense in depth](https://en.wikipedia.org/wiki/Defense_in_depth_(computing))** when serving a multi-level application. Essentially, any reasons were you may want to restrict access to services traditional virtual machines would be the same you would restrict access to services running on containers.

Should a malicious or errant entity breach the top layer, such as a container providing a web service, **[network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** could restrict what further access or layers can be reached.

Here are some obvious scenarios where **[network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** could be used:

- restrict backend *sensitive* or *critical* services running in pods such as databases, secrets vaults, administration, etc.
- restrict traffic between *single tenants*, so that pods with services containing customer *tenants* are not able to communicate with other customer *tenants*.
- restrict external traffic from outside the **[Kubernetes](https://kubernetes.io/)** cluster, especially if the network plugin parks the pods on the same subnet as other virtual machines.
- restrict outbound traffic where services should not be talking to unknown services, especially on the public Internet.

## Azure network plugin

Azure provides a more robust plugin called **[Azure CNI](https://github.com/Azure/azure-container-networking)** that will park the pods on the same subnet as virtual machines. This is useful for other virtual machines to reach the **[Kubernetes](https://kubernetes.io/)** pods without going through a **[Kubernetes](https://kubernetes.io/)** service.

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1_a2rBQ5H9M8yu087MhWv7A.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1_a2rBQ5H9M8yu087MhWv7A.png)

The obvious ***danger*** here is that all pods are now exposed to external traffic outside of the **[Kubernetes](https://kubernetes.io/)** cluster, so **[network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** definitely become vital in this scenario.

**NOTE**: The **[Azure CNI](https://github.com/Azure/azure-container-networking)** network plugin comes with a limited subset of **[network policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** features and depends on underlying Azure network infrastructure. With **[Calico](https://www.tigera.io/project-calico/)**, you can enjoy the full set of network policy features without dependency on Azure network infrastructure.

## Overview of Tutorial

This article covers how to use network policies in a real world scenario with a sensitive and critical service: a highly performant distributed graph database called Dgraph.

The article will run through these tests or steps:

1. Deploy distributed graph database **[Dgraph](https://dgraph.io/)** and **[Python](https://www.python.org/)** client pod(s) in separate namespaces
2. Deploy a **[network policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** that block off all traffic except from pods within the same namespace or from pods in a namespace with the required labels
3. Add required labels on the namespace containing the client pod to allow access to database services (**[Dgraph](https://dgraph.io/)**)

# Articles in Series

This series shows how to both secure and load balance gRPC and HTTP traffic.

# Previous Article

In a previous article, I documented how to build and push container images to **[Azure Container Registry](https://azure.microsoft.com/services/container-registry/)**. Portions of this article will be reused to demonstrate **[HTTP](https://developer.mozilla.org/docs/Web/HTTP)** and **[gRPC](https://grpc.io/)** connectivity when services are blocked.

# Requirements

For creation of **[Azure](https://azure.microsoft.com/)** cloud resources, you will need to have a subscription that will allow you to create resources.

## Required tools

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1QnM1Sp-tk5RGZHla8-ycEQ.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1QnM1Sp-tk5RGZHla8-ycEQ.png)

These tools are required for this article:

- **[Azure CLI tool](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)** (`az`): command line tool that interacts with Azure API.
- **[Kubernetes client tool](https://kubernetes.io/docs/tasks/tools/)** (`kubectl`): command line tool that interacts with Kubernetes API
- **[Helm](https://helm.sh/)** (`helm`): command line tool for “*templating and sharing Kubernetes manifests*” (**[ref](https://tanzu.vmware.com/developer/guides/kubernetes/helmfile-what-is/)**) that are bundled as Helm chart packages.
- **[helm-diff](https://github.com/databus23/helm-diff)** plugin: allows you to see the changes made with `helm` or `helmfile` before applying the changes.
- **[Helmfile](https://github.com/roboll/helmfile)** (`helmfile`): command line tool that uses a “*declarative specification for deploying Helm charts across many environments*” (**[ref](https://tanzu.vmware.com/developer/guides/kubernetes/helmfile-what-is/)**).
- **[Docker](https://docs.docker.com/get-docker/)** (`docker`): command line tool to build, test, and push docker images.

## Optional Tools

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1Azk156albrWhDCa_actH3g.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1Azk156albrWhDCa_actH3g.png)

As most of the tools to interact with **[gRPC](https://grpc.io/)** or **[HTTP](https://developer.mozilla.org/docs/Web/HTTP)** are included in the **[Docker](https://www.docker.com/)** image, only shell is recommend to manage environment variables and run scripts:

- **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)** (`sh`) such as **[GNU Bash](https://www.gnu.org/software/bash/)** (`bash`) or **[Zsh](https://www.zsh.org/)** (`zsh`): these scripts in this guide were tested using either of these shells on macOS and Ubuntu Linux.

# Project setup

Below is a file structure that will be used for this article:

```
~/azure_calico/
├── env.sh
└── examples
    ├── dgraph
    │   ├── helmfile.yaml
    │   └── network_policy.yaml
    └── pydgraph
        ├── Dockerfile
        ├── Makefile
        ├── helmfile.yaml
        ├── requirements.txt
        ├── load_data.py
        ├── sw.nquads.rdf
        └── sw.schema
```

With either **[Bash](https://www.gnu.org/software/bash/)** or **[Zsh](https://www.zsh.org/)**, you can create the file structure with the following commands:

```
mkdir -p ~/azure_calico/examples/{dgraph,pydgraph}
cd ~/azure_calico

touch \
 env.sh \
 ./examples/dgraph/network_policy.yaml \
 ./examples/{dgraph,pydgraph}/helmfile.yaml \
 ./examples/pydgraph/{Dockerfile,Makefile,requirements.txt} \
 ./examples/pydgraph/{load_data.py,sw.schema,sw.nquads.rdf}
```

## Project environment variables

Setup these environment variables below to keep a consistent environment amongst different tools used in this article. If you are using a **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)**, you can save these into a script and source that script whenever needed.

Copy this source script and save as `env.sh`:

# Provision Azure resources

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1jzooiLqWoVg07dZP4leD7A.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1jzooiLqWoVg07dZP4leD7A.png)

Azure Resources

Both **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** with **[Azure CNI](https://github.com/Azure/azure-container-networking)** and **[Calico](https://www.tigera.io/project-calico/)** network policies and **[ACR](https://azure.microsoft.com/services/container-registry/)** cloud resources can be provisioned with the following steps outlined in the script below:

## Verify AKS and KUBCONFIG

Verify that the **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** cluster was created and that you have a **KUBCONFIG** that is authorized to access the cluster by running the following:

```
source env.sh
kubectl get all--all-namespaces
```

The results should look similar to the following:

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1O2gv-zRsTvKKC7V6gSp5kA.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1O2gv-zRsTvKKC7V6gSp5kA.png)

**AKS with Calico (before 2021-Aug-01)**

**NOTE**: Recent changes (2021 Aug 01) have moved **[Calico](https://www.tigera.io/project-calico/)** components into their own namespace.

For the nodes themselves, you can gleam information, such as the node name and IP address with:

```
JP='{range .items[*]}{@.metadata.name}{"\t"}{@.status.addresses[?(@.type == "InternalIP")].address}{"\n"}{end}'kubectl get nodes--output jsonpath="$JP"
```

This will show the worker nodes and their IP address on **[Azure VNET](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)** subnet:

```
aks-nodepool1-56788426-vmss000000       10.240.0.4
aks-nodepool1-56788426-vmss000001       10.240.0.35
aks-nodepool1-56788426-vmss000002       10.240.0.66
```

# The Dgraph service

**[Dgraph](https://dgraph.io/)** is a distributed graph database that can be installed with these steps below.

Save the following as `examples/dgraph/helmfile.yaml`:

Now run this below to deploy the **[Dgraph](https://dgraph.io/)** service:

```
source env.sh
helmfile --file examples/dgraph/helmfile.yaml apply
```

When completed, it will take about 2 minutes for the **[Dgraph](https://dgraph.io/)** cluster to be ready. You can check it with:

```
kubectl--namespace dgraph get all
```

This should show something like the following:

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/13tPGNCXKdtfuK86r07-v6g.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/13tPGNCXKdtfuK86r07-v6g.png)

Dgraph deployment

Interestingly, you can peak at the IP addresses allocated to the pods and not that they are the **[Azure VNET](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)** subnet, the same subnet used by virtual machines and shared with **[Kubernetes](https://kubernetes.io/)** work nodes:

```
JP='{range .items[*]}{@.metadata.name}{"\t"}{@.status.podIP}{"\n"}{end}'kubectl get pods--output jsonpath="$JP"
```

In this implementation, this shows:

```
demo-dgraph-alpha-0      10.240.0.74
demo-dgraph-alpha-1      10.240.0.30
demo-dgraph-alpha-2      10.240.0.49
demo-dgraph-zero-0       10.240.0.24
demo-dgraph-zero-1       10.240.0.38
demo-dgraph-zero-2       10.240.0.81
```

# The pydgraph client

In the previous blog, I documented steps to build and release a `pygraph-client` image, and then deploy a container using that image.

## Fetch build and deploy scripts

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1ClU8oud__fCPYC8lnMD7BA.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1ClU8oud__fCPYC8lnMD7BA.png)

Below is a script you can use to download the gists and populate the needed files run through these steps.

**NOTE**: These scripts and further details are covered in the previous article (see **[AKS with Azure Container Registry](https://joachim8675309.medium.com/aks-with-azure-container-registry-b7ff8a45a8a))**.

## Build, push, and deploy the pydgraph client

Now that all the required source files are available, build the image:

```
source env.sh
az acr login--name ${AZ_ACR_NAME}
pushd ~/azure_calico/examples/pydgraphmake build &&make push
helmfile apply
```

After running `kubectl get all -n pydgraph-client`, this should result in something like the following:

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1vJOUH5YA1HTncgDhOGzJPA.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1vJOUH5YA1HTncgDhOGzJPA.png)

pydgraph-client deployment

## Log into the pydgraph-client container

For the next three tests, you will need to log into the container. This can be done with the following.

```
PYDGRAPH_POD=$(kubectl get pods \
--namespace pydgraph-client \
--output name
)kubectl exec-ti --namespace pydgraph-client${PYDGRAPH_POD} --bash
```

# Test 0 (Baseline): No Network Policy

Conduct a basic check to verify that the things are working before running any tests with network policies.

In this sanity check and proceeding tests, both **[HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)** (port `8080`) and **[gRPC](https://grpc.io/)** (port `9080`) will be tested.

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1tAvRdgnyDz_0L_A8oo_nWw.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1tAvRdgnyDz_0L_A8oo_nWw.png)

No Network Policy

## HTTP check (no network policy)

Log into the `pydgraph-client` pod and run this command:

```
curl${DGRAPH_ALPHA_SERVER}:8080/health | jq
```

The expected results should be health status of one of the **[Dgraph](https://dgraph.io/)** Alpha nodes:

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1qoXmz1O5mq89EfAY15Ehvw.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1qoXmz1O5mq89EfAY15Ehvw.png)

/health (HTTP)

## gRPC check (no network policy)

Log into the `pydgraph-client` pod and run this command:

```
grpcurl -plaintext -proto api.proto \
${DGRAPH_ALPHA_SERVER}:9080 \
  api.Dgraph/CheckVersion
```

The expected results will be the **[Dgraph](https://dgraph.io/)** server version.

api.Dgraph/CheckVersion (gRPC)

# TEST 1: Apply a network policy

In this test, you will add a network policy that denies all traffic, unless the pods come from a namespace that correct label. The acceptance criteria for this test the client will not be able to connect to the Dgraph service.

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1HQIvmiepUygtatu77FXwLw.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1HQIvmiepUygtatu77FXwLw.png)

Network Policy added

## Adding a network policy

This policy will deny all traffic to the Dgraph Alpha pods, except for traffic from within the same namespace or traffic from pods in namespaces with labels of `app=dgraph-client` and `env=test`.

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1vY-Kgedm9DgC997YMVeIAw.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1vY-Kgedm9DgC997YMVeIAw.png)

Dgraph Network Policy (made with https://editor.cilium.io/)

Copy the following and say as `examples/dgraph/network_policy.yaml`:

When ready, apply this with:

```
kubectl --filename ./examples/dgraph/network_policy.yaml apply
```

## HTTP check (network policy applied)

Log into the `pydgraph-client` pod, and run this command:

```
curl${DGRAPH_ALPHA_SERVER}:8080/health
```

The expected results in this case, after a very long wait (about 5 minutes), the result will be a time out:

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1YcRTC-xS0VCt1tmrvBuz7Q.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1YcRTC-xS0VCt1tmrvBuz7Q.png)

## gRPC check (network policy apply)

Log into the `pydgraph-client` pod and run this command:

```
grpcurl -plaintext -proto api.proto \
${DGRAPH_ALPHA_SERVER}:9080 \
  api.Dgraph/CheckVersion
```

The expected results for **[gRPC](https://grpc.io/)** in about 10 seconds will be:

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1nu4Gmuwy_LvJTfZz4hcKFQ.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1nu4Gmuwy_LvJTfZz4hcKFQ.png)

api.Dgraph/CheckVersion (gRPC)

# TEST 2: Allow traffic to the service

Now that we demonstrated that connectivity is walled off from access, we can add the appropriate labels to the namespace so that traffic will be permitted.

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1mVYmEYqbmQwpwu2Yzzb6-w.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1mVYmEYqbmQwpwu2Yzzb6-w.png)

Label app=dgraph-client added

## Allow a client to access Dgraph

```
kubectl label namespaces pydgraph-client env=test app=dgraph-client
```

After this command new labels will be added:

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1YRVmbyCzBJCevKEPi1ACsQ.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1YRVmbyCzBJCevKEPi1ACsQ.png)

View of namespace labels (Lens tool https://k8slens.dev/)

## HTTP check (namespace label applied)

Log into the `pydgraph-client` pod, and run this command:

```
curl${DGRAPH_ALPHA_SERVER}:8080/health | jq
```

The expected results for this is that JSON data about the health from one of the **[Dgraph](https://dgraph.io/)** Alpha pods.

/health (HTTP)

## gRPC check (namespace label applied)

Log into the `pydgraph-client` pod and run this command:

```
grpcurl -plaintext -proto api.proto \
${DGRAPH_ALPHA_SERVER}:9080 \
  api.Dgraph/CheckVersion
```

The expected results for this is that JSON detailing the **[Dgraph](https://dgraph.io/)** server version.

![AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1ZMNwgF0BWgjOY-ZtgsOSUA.png](AKS%20with%20Calico%20Network%20Policies%202be7efa0b4bc4e6b9ce5986ab708cd48/1ZMNwgF0BWgjOY-ZtgsOSUA.png)

api.Dgraph/CheckVersion (gRPC)

# Cleanup

This will remove the **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** cluster as well as any provisioned resources from **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** including external volumes created through the **[Dgraph](https://dgraph.io/)** deployment.

```
az aks delete \
--resource-group$AZ_RESOURCE_GROUP \
--name$AZ_CLUSTER_NAME
```

# Resources

These are some of the resources I have come across online when researching this article.

## Blog source code

## Network policy tools

- **Online graphical net policy editor**: [https://editor.cilium.io/](https://editor.cilium.io/)

## Network policy articles

- **Guide to Kubernetes Ingress Network Policies**: [https://www.openshift.com/blog/guide-to-kubernetes-ingress-network-policies](https://www.openshift.com/blog/guide-to-kubernetes-ingress-network-policies)
- **Securing Kubernetes Cluster Networking**: [https://ahmet.im/blog/kubernetes-network-policy/](https://ahmet.im/blog/kubernetes-network-policy/)
- **Get started with Kubernetes network policy**: [https://docs.projectcalico.org/security/kubernetes-network-policy](https://docs.projectcalico.org/security/kubernetes-network-policy)

## Videos

## Documentation

## Network plugins supporting network policies

# Conclusion

Security is a vital and necessary part of infrastructure and with the introduction of rich container orchestration platforms, security doesn’t go away, for it is still needed at the the platform layer (**[Kubernetes](https://kubernetes.io/)**) as well as the infrastructure layer (**[Azure](https://azure.microsoft.com/)**).

In this tutorial, security is important for the backend distributed graph database **[Dgraph](https://dgraph.io/)**. Only designated clients and automation that manages operational aspects of **[Dgraph](https://dgraph.io/)**, such as **[backups](https://dgraph.io/docs/enterprise-features/binary-backups/)** and **[live loading](https://dgraph.io/docs/deploy/fast-data-loading/live-loader/)**, should be permitted, while everything else is denied access.

## Beyond network policies

Beyond network policies to restrict access to **[Kubernetes](https://kubernetes.io/)** pods, the traffic between pods should be secured, which is called **[encryption in transit](https://www.ncsc.gov.uk/collection/cloud-security/implementing-the-cloud-security-principles/data-in-transit-protection)**.

This article is an import step toward exploring traffic security toward with **[service meshes](https://buoyant.io/what-is-a-service-mesh)**, which can automate configuring **[mutual TLS](https://learn.akamai.com/en-us/webhelp/iot/internet-of-things-over-the-air-user-guide/GUID-21EC6B74-28C8-4CE1-980E-D5EE57AD9653.html)** for short lived ephemeral pods.

Thank you for reading my article. I hope this useful in your **[Kubernetes](https://kubernetes.io/)** journeys and I wish you the best of success.