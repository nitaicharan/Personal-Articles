# AKS with Linkerd Service Mesh

Created: September 28, 2022 9:20 AM
Finished: No
Source: https://joachim8675309.medium.com/linkerd-service-mesh-on-aks-a75d60ef4f5a

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1SpXUt_t8W3PXZBSLLRyS5g.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1SpXUt_t8W3PXZBSLLRyS5g.png)

The two most often neglected domains of cloud operations are security and **[o11y](https://thatsamguy.com/observability-101/)** (*observability*). This should come as no surprise because adding security, such as *encryption-in-transit* with mutual TLS (where both client and server verify each other), and adding traffic monitoring and tracing on short lived transitory pods is by its very nature complex.

> What if you could add automation for both security and o11y in less than 15 minutes of effort?
> 

The solution to all of this complexity involves deploying a **[service mesh](https://en.wikipedia.org/wiki/Service_mesh)**, and as unbelievable as it seems, the above statement can really happen with **[Linkerd](https://linkerd.io/)**.

This article covers using the service mesh **[Linkerd](https://linkerd.io/)** installed into AKS (Azure Kubernetes Service) with an example application **[Dgraph](https://dgraph.io/)**.

## Architecture

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1OYS1l5hUfyO2NGA5V8wG4A.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1OYS1l5hUfyO2NGA5V8wG4A.png)

Linkerd Architecture: Control Plane vs Data Plane

A service mesh can be logically organized into two primary layers:

> a control plane layer that’s responsible for configuration and management, and a data plane layer that provides network functions valuable to distributed applications. (ref)
> 

## What is a service mesh?

The *service mesh* consist of **[proxies](https://en.wikipedia.org/wiki/Proxy_server)** and **[reverse proxies](https://en.wikipedia.org/wiki/Reverse_proxy)** for every service that will be put into a network called a *mesh*. This allows you to secure and monitor traffic between all members within the *mesh*.

A **[proxy](https://en.wikipedia.org/wiki/Proxy_server)**, for the uninitiated, is redirecting outbound web traffic to an intermediary web service that can apply security policies, such as blocking access to a malicious web site, before traffic is sent on its way to the destination.

The **[reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy)** is is an intermediary web service that can secure and route inbound traffic based set of defined rules, such as rules based on an HTTP path, a destination hostname, and ports.

The combined **[proxy](https://en.wikipedia.org/wiki/Proxy_server)** and **[reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy)** on every member node in the *mesh*, affords a refined level of security where you can allow only designated services to access other designated services, which is particularly useful for isolating services in case one of them is compromised.

# Articles in Series

This series shows how to both secure and load balance gRPC and HTTP traffic.

# Previous Article

# Requirements

For creation of **[Azure](https://azure.microsoft.com/)** cloud resources, you will need to have a subscription that will allow you to create resources.

## Required Tools

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1oWbAePWqHSyqg4bm4-13RQ.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1oWbAePWqHSyqg4bm4-13RQ.png)

- **[Azure CLI tool](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)** (`az`): command line tool that interacts with Azure API.
- **[Kubernetes client tool](https://kubernetes.io/docs/tasks/tools/)** (`kubectl`): command line tool that interacts with Kubernetes API
- **[Helm](https://helm.sh/)** (`helm`): command line tool for “*templating and sharing Kubernetes manifests*” (**[ref](https://tanzu.vmware.com/developer/guides/kubernetes/helmfile-what-is/)**) that are bundled as Helm chart packages.
- **[helm-diff](https://github.com/databus23/helm-diff)** plugin: allows you to see the changes made with `helm` or `helmfile` before applying the changes.
- **[Helmfile](https://github.com/roboll/helmfile)** (`helmfile`): command line tool that uses a “*declarative specification for deploying Helm charts across many environments*” (**[ref](https://tanzu.vmware.com/developer/guides/kubernetes/helmfile-what-is/)**).
- **[Linkerd CLI](https://github.com/linkerd/linkerd2/releases/)** (`linkerd`): command line tool that can configure, deploy, verify linkerd environment and extensions.

## Optional tools

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/11TWYw2TRfb10Oxwn38zFYA.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/11TWYw2TRfb10Oxwn38zFYA.png)

Many of the tools such as `grpcurl`, `curl`, `jq` will be accessible from the **[Docker](https://www.docker.com/)** container. For building images and running scripts, I highly recommend these tools:

- **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)** (`sh`) such as **[GNU Bash](https://www.gnu.org/software/bash/)** (`bash`) or **[Zsh](https://www.zsh.org/)** (`zsh`): these scripts in this guide were tested using either of these shells on macOS and Ubuntu Linux.
- **[Docker](https://docs.docker.com/get-docker/)** (`docker`): command line tool to build, test, and push docker images.
- **[SmallStep CLI](https://smallstep.com/cli/)** (`step`): A zero trust swiss army knife for working with certificates

# Project file structure

The following structure will be used:

```
~/azure_linkerd
├── certs
│   ├── ca.crt
│   ├── ca.key
│   ├── issuer.crt
│   └── issuer.key
├── env.sh
└── examples
    ├── dgraph
    │   ├── helmfile.yaml
    │   └── network_policy.yaml
    └── pydgraph
        ├── Dockerfile
        ├── Makefile
        ├── helmfile.yaml
        ├── load_data.py
        ├── requirements.txt
        ├── sw.nquads.rdf
        └── sw.schema
```

With either **[Bash](https://www.gnu.org/software/bash/)** or **[Zsh](https://www.zsh.org/)**, you can create the file structure with the following commands:

## Project Environment Variables

Setup these environment variables below to keep a consistent environment amongst different tools used in this article. If you are using a **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)**, you can save these into a script and source that script whenever needed.

Copy this source script and save as `env.sh`:

**NOTE**: The default container registry for **[Linkerd](https://linkerd.io/)** uses **[GHCR](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)** (**[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)**). In my experiments, this has been a source of problems with **[AKS](https://azure.microsoft.com/services/kubernetes-service/)**, so as an alternative, I recommend republishing the container images to another registry. Look for optional instructions below if you are interested in doing this as well.

# Provision Azure resources

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/199UoTJgGWemPq5en7mWD6w.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/199UoTJgGWemPq5en7mWD6w.png)

Azure cloud resources

Both **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** with **[Azure CNI](https://github.com/Azure/azure-container-networking)** and **[Calico](https://www.tigera.io/project-calico/)** network policies and **[ACR](https://azure.microsoft.com/services/container-registry/)** cloud resources can be provisioned with the following steps outlined in the script below.

**NOTE**: Though these instructions are oriented **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** and **[ACR](https://azure.microsoft.com/services/container-registry/)**, you can use any **[Kubernetes](https://kubernetes.io/)** with the **[Calico](https://www.tigera.io/project-calico/)** network plugin installed for network policies, and you can use any container registry, as long as it is accessible from the **[Kubernetes](https://kubernetes.io/)** cluster.

## Verify AKS and KUBCONFIG

Verify that the **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** cluster was created and that you have a **`KUBCONFIG`** that is authorized to access the cluster by running the following:

```
source env.shkubectl get all--all-namespaces
```

The final results can should look something like this:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1O2gv-zRsTvKKC7V6gSp5kA.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1O2gv-zRsTvKKC7V6gSp5kA.png)

# The Linkerd service mesh

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/138lWC_VvRk43QxmFy5kxig.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/138lWC_VvRk43QxmFy5kxig.png)

Kubernetes Components

**[Linkerd](https://linkerd.io/)** can be installed using either the **[linkerd cli](https://linkerd.io/2.10/reference/cli/)** or through the **[linkerd2 helm chart](https://artifacthub.io/packages/helm/linkerd2/linkerd2)**. For this article, the `linkerd` command will be use to generate the **[Kubernetes](https://kubernetes.io/)** manifests and then apply them with the `kubectl` command

## Generate Certificates

**[Linkerd](https://linkerd.io/)** requires a trust anchor certificate and an issuer certificates with the corresponding key to support mutual TLS connections between meshed pods. All certificates require **[ECDSA P-256](https://csrc.nist.gov/csrc/media/events/workshop-on-elliptic-curve-cryptography-standards/documents/papers/session6-adalier-mehmet.pdf)** algorithm, which is the default for the `step` command. You can alternatively use the `openssl ecparam -name prime256v1` command.

To generate certificates using the `step` command for this, run the following commands.

## Republish Linkerd Images (optional)

Linkerd uses **[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)**, which has been consistently unreliable when fetching images from **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** (`v1.19.11`). This leads to deploys to take around 20 to 30 minutes due to these errors.

As an optional step, the container images can be republished to **[ACR](https://azure.microsoft.com/services/container-registry/),** which can reduce deploy time significantly to about 3 minutes. In the script below, follow the steps to republish the images.

## Install Linkerd

You can install linkerd with the generated certificates using the `linkerd` command line tool:

The `linkerd` command will generate **[Kubernetes](https://kubernetes.io/)** manifests that are then piped to `kubectl` command.

When completed, run this command to verify the deployed **[Linkerd](https://linkerd.io/)** infrastructure:

```
kubectl get all --namespace linkerd
```

This will show something like the following below:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1GJVXeSrtza0m00YYfcWSAA.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1GJVXeSrtza0m00YYfcWSAA.png)

You can also run `linkerd check` to verify the health of **[Linkerd](https://linkerd.io/)**:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1pBKny6HMitxIu0qR4IM9wA.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1pBKny6HMitxIu0qR4IM9wA.png)

## Install the Viz extension

The **[Viz](https://linkerd.io/2.10/reference/cli/viz/)** extension adds some graphical web dashboards, **[Prometheus](https://prometheus.io/)** metric system, and **[Grafana](https://grafana.com/)** dashboards:

```
linkerd viz install |kubectl apply-f -
```

You can check the infrastructure with the following command:

```
kubectl get all--namespace linkerd-viz
```

This should show something like the following:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1VIWc9wVD0e_zht3besT_zQ.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1VIWc9wVD0e_zht3besT_zQ.png)

Additionally you can run `linkerd viz check`:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1uJulGpQtdRc9rCgNnQ_Qmg.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1uJulGpQtdRc9rCgNnQ_Qmg.png)

## Install the Jaeger extension

The Jaeger extension will install the Jaeger, a distributed tracing solution.

```
linkerd jaeger install |kubectl apply-f -
```

You can check up on the success of the deployment with the following command:

```
kubectl get all--namespace linkerd-jaeger
```

This should show something like the following:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1NYsRR9QbAvKkDngNqivMZQ.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1NYsRR9QbAvKkDngNqivMZQ.png)

Additionally you can run `linkerd jaeger check`:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1OwsXqaAgrg01luUYM31sYg.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1OwsXqaAgrg01luUYM31sYg.png)

## Access Viz Dashboard

You can port-forward to localhost with this command:

```
linkerd viz dashboard &
```

This should show something like the following

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1AOJaeNv9xPNG2BvqU4Gyfw.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1AOJaeNv9xPNG2BvqU4Gyfw.png)

# The Dgraph service

**[Dgraph](https://dgraph.io/)** is a distributed graph database consisting of three **[Dgraph](https://dgraph.io/)** Alpha member nodes, which host the graph data, and three **[Dgraph](https://dgraph.io/)** Zero nodes, which manage the state of **[Dgraph](https://dgraph.io/)** cluster including the timestamps. The **[Dgraph](https://dgraph.io/)** Alpha service supports both a **[HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)** (**[GraphQL](https://graphql.org/)**) on port `8080` and **[gRPC](https://grpc.io/)** interface on port `9080`

**[Linkerd](https://linkerd.io/)** magic takes place by injecting a proxy sidecar container into each pod that will be a member of the service mesh. This can be configured by adding template annotations to a `deployment` or `statefulset` controller.

## Deploy Dgraph with Linkerd

**[Dgraph](https://dgraph.io/)** will be deployed using the **[Dgraph chart](https://artifacthub.io/packages/helm/dgraph/dgraph)**, but instead of the normal route of installing **[Dgraph](https://dgraph.io/)** with `helmfile apply`, a **[Kubernetes](https://kubernetes.io/)** manifest will be generated with `helmfile template`., so that the `linkerd inject` command can be used.

**NOTE**: Currently **[Dgraph](https://dgraph.io/)** does yet not have direct support modifying the template annotations in the `statefulset`. Recently, I added a **[pull request](https://github.com/dgraph-io/charts/pull/71)** for this feature, and hopefully a new chart version will be published. In the mean time, `helmfile template` command will work.

Run these commands to deploy **[Dgraph](https://dgraph.io/)** with the injected **[Linkerd](https://linkerd.io/)** proxy side cars:

After about a minute, the **[Dgraph](https://dgraph.io/)** cluster will come up, you can verify this with the following command:

```
kubectl get all--namespace "dgraph"
```

We should see something like this:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1cJssnwNWiY62UgRpN-kjbw.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1cJssnwNWiY62UgRpN-kjbw.png)

## Service Profile

Should you want to run a **[gRPC client](https://dgraph.io/docs/clients/overview/)** to connect to **[Dgraph](https://dgraph.io/)** on the same **[Kubernetes](https://kubernetes.io/)** cluster, you will want to generate a **[service profile](https://linkerd.io/2.10/features/service-profiles/)**. This will allow more evenly distributed traffic across **[Dgraph](https://dgraph.io/)** member nodes.

Generate and deploy a service profile:

# The pydgraph client

In an **[earlier blog](https://joachim8675309.medium.com/aks-with-azure-container-registry-b7ff8a45a8a)**, I documented steps to build and release a `pygraph-client` image, and then deploy a pod that uses this image.

The `pydgraph-client` pod will have all the tools needed to test both **[HTTP](https://developer.mozilla.org/docs/Web/HTTP/Overview)** and **[gRPC](https://grpc.io/)**. We’ll use this client run though the following tests:

1. Establish basic connectivity works (baseline)
2. Apply a network policy to block all non-proxy traffic with Calico and verify connectivity no longer works.
3. Inject a proxy into the pydgraph and verify connectivity through proxy works

## Fetch build and deploy scripts

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1ClU8oud__fCPYC8lnMD7BA.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1ClU8oud__fCPYC8lnMD7BA.png)

Below is a script you can use to download the gists and populate the needed files run through these steps.

**NOTE**: These scripts and further details are covered in an earlier article (see **[AKS with Azure Container Registry](https://joachim8675309.medium.com/aks-with-azure-container-registry-b7ff8a45a8a))**.

## Build, push, and deploy the pydgraph client

Now that all the required source files are available, build the image:

After running **`kubectl** get all **--namespace** pydgraph-client`, this should result in something like the following:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1JCY-RS6sTIVpfdZT1gMq1g.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1JCY-RS6sTIVpfdZT1gMq1g.png)

## Log into the pydgraph-client container

For the next set of tests, you will need to log into the container. This can be done with the following commands:

```
PYDGRAPH_POD=$(kubectl get pods \
--namespace pydgraph-client \
--output name
)kubectl exec-ti \
  --namespace pydgraph-client${PYDGRAPH_POD}\
--container pydgraph-client --bash
```

# Test 0 (Baseline): No Proxy

Verify that the things are working without a proxy or network policies.

In this sanity check and proceeding tests, both **[HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)** (port `8080`) and **[gRPC](https://grpc.io/)** (port `9080`) will be tested.

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1MmaUEijzkpmqi-wtOb6UJw.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1MmaUEijzkpmqi-wtOb6UJw.png)

No proxy on pydgraph-client

## HTTP check (no proxy)

Log into the `pydgraph-client` pod and run this command:

```
curl ${DGRAPH_ALPHA_SERVER}:8080/health | jq
```

The expected results will be something similar to this:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1jdgoV-DfL597OmMJb8BAwQ.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1jdgoV-DfL597OmMJb8BAwQ.png)

## gRPC check (no proxy)

Log into the `pydgraph-client` pod and run this command:

```
grpcurl -plaintext -proto api.proto \
${DGRAPH_ALPHA_SERVER}:9080 \
  api.Dgraph/CheckVersion
```

The expected results will be something similar to this:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/17G2CQ5ua_CXt9Qsy3nLqcQ.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/17G2CQ5ua_CXt9Qsy3nLqcQ.png)

# Test 1: Add a network policy

The goal of this next test is to deny all traffic that is outside of service mesh. his can be done by using **[network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** where only traffic from the service mesh is permitted.

After adding the policy, the expected results will timeouts as communicate from the `pydgraph-client` will be blocked.

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1TA6FqW1UXvib4rVECGntlA.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1TA6FqW1UXvib4rVECGntlA.png)

Network Policy added to block traffic outside the mesh

## Adding a network policy

This policy will deny all traffic to the **[Dgraph](https://dgraph.io/)** Alpha pods, except for traffic from the service mesh, or more explicitly, from any pod with the label `linkerd.io/control-plane-ns=linkerd`.

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1yFijmmFgSVVzClfqOSs6BA.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1yFijmmFgSVVzClfqOSs6BA.png)

Dgraph Network Policy for Linkerd (made with https://editor.cilium.io)

Copy the following and save as `examples/dgraph/network_policy.yaml`:

When ready, apply this with the following command:

```
kubectl --filename ./examples/dgraph/network_policy.yaml apply
```

# HTTP check (network policy applied)

Log into the `pydgraph-client` pod, and run this command:

```
curl${DGRAPH_ALPHA_SERVER}:8080/health
```

The expected results in this case, after a very long wait (about 5 minutes) will be something similar to this:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/14eYjusWkVmzKoUlZPAgstg.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/14eYjusWkVmzKoUlZPAgstg.png)

# gRPC check (network policy apply)

Log into the `pydgraph-client` pod and run this command:

```
grpcurl -plaintext -proto api.proto \
${DGRAPH_ALPHA_SERVER}:9080 \
  api.Dgraph/CheckVersion
```

The expected results for **[gRPC](https://grpc.io/)** in about 10 seconds will be:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1bJ8i66gcUyEtClrKDcNxVw.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1bJ8i66gcUyEtClrKDcNxVw.png)

# Test 2: Inject Linkerd proxy side car

Now that we verified that network connectivity is not possible, we can inject a proxy side car so that traffic will be permitted.

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/15Se9iW8uT9GN0QcWiyOpgQ.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/15Se9iW8uT9GN0QcWiyOpgQ.png)

## Inject the proxy in order to access Dgraph

A new container `linkerd-proxy` is added to the pod:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1Nr3kIUaNCen9b-wY6h2xDA.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1Nr3kIUaNCen9b-wY6h2xDA.png)

View of containers (Lens tool https://k8slens.dev/)

## HTTP check (proxy)

Log into the `pydgraph-client` pod and run this command:

```
curl${DGRAPH_ALPHA_SERVER}:8080/health | jq
```

The expected results will look something similar to this:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1WYXYFrKH8ESK6mh4BnCa0Q.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1WYXYFrKH8ESK6mh4BnCa0Q.png)

## gRPC check (proxy)

Log into the `pydgraph-client` pod and run this command:

```
grpcurl -plaintext -proto api.proto \
${DGRAPH_ALPHA_SERVER}:9080 \
  api.Dgraph/CheckVersion
```

The expected results will look something similar to this:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1ZMNwgF0BWgjOY-ZtgsOSUA.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1ZMNwgF0BWgjOY-ZtgsOSUA.png)

# Test 3: Listening to traffic steams

For this step, we will monitor traffic as it goes through the proxy and then generate some traffic. For monitoring, we’ll use `tap` from the command line and the dashboard to listen to traffic streams.

## Viz Tap from the CLI

In a separate terminal tab or window, run this command to monitor traffic:

```
linkerd viz tap namespace/pydgraph-client
```

## Viz Tap from the dashboard

We can also do the same thing in the Linkerd Viz Dashboard under Tap area:

1. set the **Namespace** field to `pydgraph-client`
2. set the **Resource** field to `namespace/pydgraph-client`
3. click on the the **`[START]`** button

## Generate Traffic

With this monitoring in place, log into the `pydgraph-client` pod and run these commands:

## Observe the resulting traffic

In the separate terminal tab or window, you should see output like this below:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1aGcznNEoRNGfZif_ug8oPQ.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/1aGcznNEoRNGfZif_ug8oPQ.png)

NOTE: The colors were added manually to highlight traffic generated from the different commands.

In the Viz Dashboard, you should see something like this:

![AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/16Y0kK-a3olxXPZ7eP5fHDw.png](AKS%20with%20Linkerd%20Service%20Mesh%2033d860fa70974e299651abe98fce5918/16Y0kK-a3olxXPZ7eP5fHDw.png)

# Cleanup

This will remove the **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** cluster as well as any provisioned resources from **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** including external volumes created through the **[Dgraph](https://dgraph.io/)** deployment.

```
az aks delete \
--resource-group$AZ_RESOURCE_GROUP \
--name$AZ_CLUSTER_NAME
```

# Resources

Here are some links to topics, articles, and tools used in this article:

## Blog Source Code

This is the source code related to this blog.

## Example Applications

These are applications that can be used to walk through the features of a service mesh.

- [https://github.com/spikecurtis/yaobank](https://github.com/spikecurtis/yaobank)
- [https://github.com/BuoyantIO/emojivoto](https://github.com/BuoyantIO/emojivoto)
- [https://github.com/BuoyantIO/booksapp](https://github.com/BuoyantIO/booksapp)
- [https://github.com/istio/istio/tree/master/samples/bookinfo](https://github.com/istio/istio/tree/master/samples/bookinfo)
- [https://github.com/argoproj/argocd-example-apps/tree/master/helm-guestbook](https://github.com/argoproj/argocd-example-apps/tree/master/helm-guestbook)
- [https://github.com/dockersamples/example-voting-app](https://github.com/dockersamples/example-voting-app)

## General Service Mesh Articles

## gRPC Load Balancing

Topics on gRPC load balancing on Kubernetes.

## Linkerd Documentation

## About o11y (cloud native observability)

The o11y or observability is a new term to distinguish observability around patterns in cloud native infrastructure.

- **Learn about observability by Honeycomb**: [https://docs.honeycomb.io/getting-started/learning-about-observability/](https://docs.honeycomb.io/getting-started/learning-about-observability/)
- **What is the difference between monitoring and observability?** [https://www.splunk.com/en_us/data-insider/what-is-observability.html](https://www.splunk.com/en_us/data-insider/what-is-observability.html)

## Service Mesh Traffic Access

Here are some topics around service mesh traffic access in the community, and related to upcoming feature in the `stable-2.11` release.

- **Issue 3342** *Provide Service-to-Service Authorization*: [https://github.com/linkerd/linkerd2/issues/3342](https://github.com/linkerd/linkerd2/issues/3342)
- **Issue 2746** *Support Service Network Policies*: [https://github.com/linkerd/linkerd2/issues/2746](https://github.com/linkerd/linkerd2/issues/2746)
- **Linkerd Policy Exploration Design**: [https://github.com/linkerd/polixy/blob/main/DESIGN.md](https://github.com/linkerd/polixy/blob/main/DESIGN.md)
- **OPA**: [https://www.openpolicyagent.org/](https://www.openpolicyagent.org/)
- **SMI**: [https://smi-spec.io/](https://smi-spec.io/)
- **SMI Spec on GitHub**: [https://github.com/servicemeshinterface/smi-spec](https://github.com/servicemeshinterface/smi-spec)
- **Envoy External Auth w/ OPA**: [https://blog.openpolicyagent.org/envoy-external-authorization-with-opa-578213ed567c](https://blog.openpolicyagent.org/envoy-external-authorization-with-opa-578213ed567c)

## Document Changes for Blog

- 2021年9月4日 multiline code to gists, updated images
- 2021年8月6日 Updated Linkerd architecture image

# Conclusion

**[Linkerd](https://linkerd.io/)** is a breeze to setup and get off the ground despite the numerous components and processes happening behind the scenes.

## Load Balancing

Beyond attractive features like automation for **[o11y](https://thatsamguy.com/observability-101/)** (*cloud native observability*) and *encryption-in-transit* with Mutual TLS, the one often overlooked feature are the load balancing features, for not only **[HTTP](https://developer.mozilla.org/docs/Web/HTTP)** traffic, but **[gRPC](https://grpc.io/)** as well. Why this is important is because of this:

> gRPC also breaks the standard connection-level load balancing, including what’s provided by Kubernetes. This is because gRPC is built on HTTP/2, and HTTP/2 is designed to have a single long-lived TCP connection, across which all requests are multiplexed — meaning multiple requests can be active on the same connection at any point in time. (ref)
> 

Using the default **[Kubernetes](https://kubernetes.io/)** service resource (kube-proxy), connections are randomly selected, i.e. no load balancing, and for **[gRPC](https://grpc.io/)**, this means a single node in your highly available cluster will suck up all the traffic. Thus, **[load balancing feature](https://linkerd.io/2.10/features/load-balancing/)** of **[Linkerd](https://linkerd.io/)** becomes, in my mind, one of the most essential features.

## Restricting Traffic

For security beyond *encryption-in-transit* with Mutual TLS, the restriction access to pods is also important. This area is called *defense-in-depth*, a layered approached to restrict which services should be able to connect to each other.

In this article, I touched on how to do a little of this with **[network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** using the **[Calico](https://www.tigera.io/project-calico/)** network plugin.

It would be really nice to have some policies that can be applied to mesh traffic as well. Well, this is happening in an upcoming `stable-2.11` release, traffic access with two new CRDs: **`[Server](https://github.com/linkerd/polixy/blob/b448b30944a57dba26279f9466079cc272cdffcb/DESIGN.md#server)`** and **`[ServerAuthorization](https://github.com/linkerd/polixy/blob/b448b30944a57dba26279f9466079cc272cdffcb/DESIGN.md#serverauthorization)`.**

## Final Thoughts

Thank you for finishing this article; I hope that this helped you in your journey.