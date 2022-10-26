# AKS with Istio Service Mesh

Created: September 28, 2022 9:20 AM
Finished: No
Source: https://joachim8675309.medium.com/istio-service-mesh-on-aks-1b6ed16f6890

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/11ExTjhI6JARo250XhjhdQQ.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/11ExTjhI6JARo250XhjhdQQ.png)

So now you have your distributed application, monolith, or microservices packaged as a container and deployed to **[Kubernetes](https://kubernetes.io/)**. Congratulations!

But now you need to have security, such as encrypted traffic and network firewalls, and for all of these secured services, you need to have monitoring and proper load balancing of **[gRPC](https://grpc.io/)** (**[HTTP/2](https://http2.github.io/)**) traffic, especially as **[Kubernetes](https://kubernetes.io/)** fails in this department (**[ref](https://kubernetes.io/blog/2018/11/07/grpc-load-balancing-on-kubernetes-without-tears/)**).

And all of these things need to happen every time you roll out an new pod.

## The Solution

This can all be down with a *service mesh*, that will add encryption-in-transit (**[mTLS](https://www.cloudflare.com/learning/access-management/what-is-mutual-tls/)** or **[Mutual TLS](https://www.cloudflare.com/learning/access-management/what-is-mutual-tls/)**), o11y (cloud native observability), load balancing, traffic management, as well as other features. For security outside of the service mesh (layer 4), you can use a network plugin, like **[Calico](https://www.tigera.io/project-calico/)**, that supports *network policies*.

This article will cover how to get started with this using **[Istio](https://istio.io/)**, coupled with the famous **[Envoy proxy](https://www.envoyproxy.io/)**, which is one of the most popular service mesh platforms on Kubernetes.

## Goals and Not Goals

This article will cover the following *goals*:

The *not goals* (reserved for later):

- Restricting traffic within the mesh to authorized clients.
- Automatic authorization (**[AuthN](https://en.wikipedia.org/wiki/Web_API_security)**, **[JWT](https://jwt.io/)**, etc) for mesh members
- Managing external inbound or outbound traffic through Gateways.
- Other traffic management features, like retries and circuit breaker.

## Architecture

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1LvzBpsafoh_YuaM6cxQsXw.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1LvzBpsafoh_YuaM6cxQsXw.png)

Istio Architecture: Control Plan vs. Data Plane

A service mesh can be logically organized into two primary layers:

> a control plane layer that’s responsible for configuration and management, and a data plane layer that provides network functions valuable to distributed applications. (ref)
> 

# Articles in Series

This series shows how to both secure and load balance gRPC and HTTP traffic.

# Previous Article

The previous article covered similar topics using the **[Linkerd](https://linkerd.io/)** service mesh.

# Requirements

For creation of **[Azure](https://azure.microsoft.com/)** cloud resources, you will need to have a subscription that will allow you to create resources.

## Required Tools

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1AHmb5UUzqLY9rrN1h7xD5g.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1AHmb5UUzqLY9rrN1h7xD5g.png)

- **[Azure CLI tool](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)** (`az`): command line tool that interacts with Azure API.
- **[Kubernetes client tool](https://kubernetes.io/docs/tasks/tools/)** (`kubectl`): command line tool that interacts with Kubernetes API
- **[Helm](https://helm.sh/)** (`helm`): command line tool for “*templating and sharing Kubernetes manifests*” (**[ref](https://tanzu.vmware.com/developer/guides/kubernetes/helmfile-what-is/)**) that are bundled as Helm chart packages.
- **[helm-diff](https://github.com/databus23/helm-diff)** plugin: allows you to see the changes made with `helm` or `helmfile` before applying the changes.
- **[Helmfile](https://github.com/roboll/helmfile)** (`helmfile`): command line tool that uses a “*declarative specification for deploying Helm charts across many environments*” (**[ref](https://tanzu.vmware.com/developer/guides/kubernetes/helmfile-what-is/)**).
- **Istio CLI** (`istioctl`): command line tool to configure and deploy the Istio environment.

## Optional tools

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1Azk156albrWhDCa_actH3g.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1Azk156albrWhDCa_actH3g.png)

- **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)** (`sh`) such as **[GNU Bash](https://www.gnu.org/software/bash/)** (`bash`) or **[Zsh](https://www.zsh.org/)** (`zsh`): these scripts in this guide were tested using either of these shells on macOS and Ubuntu Linux.
- **[Docker](https://docs.docker.com/get-docker/)** (`docker`): command line tool to build, test, and push docker images.

# Project setup

The following structure will be used:

```
~/azure_istio
├── addons
│   ├── grafana.yaml
│   ├── jaeger.yaml
│   ├── kiali.yaml
│   ├── prometheus.yaml
│   ├── prometheus_vm.yaml
│   └── prometheus_vm_tls.yaml
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

## Project environment variables

Setup these environment variables below to keep a consistent environment amongst different tools used in this article. If you are using a **[POSIX shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html)**, you can save these into a script and source that script whenever needed.

Copy this source script and save as `env.sh`:

# Provision Azure resources

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/16fjttM3naLOgurVYutqy4w.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/16fjttM3naLOgurVYutqy4w.png)

Azure Resources

Both **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** with **[Azure CNI](https://github.com/Azure/azure-container-networking)** and **[Calico](https://www.tigera.io/project-calico/)** network policies and **[ACR](https://azure.microsoft.com/services/container-registry/)** cloud resources can be provisioned with the following steps outlined in the script below.

## Verify AKS and KUBCONFIG

Verify that the **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** cluster with current configured **`KUBCONFIG`** environment variable:

```
source env.sh
kubectl get all--all-namespaces
```

The results should look something like the:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/14u44O6-JIaTvpVZx1cq1jA.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/14u44O6-JIaTvpVZx1cq1jA.png)

AKS with Azure CNI and Calico

**NOTE**: As of Aug 1, 2021, this will install Kubernetes `v1.20.7` and Calico cluster version is `v3.19.0`. This will reflect recent **[changes](https://github.com/Azure/AKS/issues/2089)** and introduce two new namespaces: `calico-system` and `tigera-operator`.

## Verify Azure CNI

Verify that nodes and pods are now on the same **[Azure VNET](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview)** subnet, which means that the **[Azure CNI](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md)** network plugin is installed as the default plugin.

You can print the IP addresses on the nodes and pods with the following:

This should show something like this:

```
Nodes:
------------
aks-nodepool1-56788426-vmss000000        10.240.0.4
aks-nodepool1-56788426-vmss000001        10.240.0.35
aks-nodepool1-56788426-vmss000002        10.240.0.66Pods:
------------
calico-kube-controllers-7d7897d6b7-qlrh6 10.240.0.36
calico-node-fxg66                        10.240.0.66
calico-node-j4hlq                        10.240.0.35
calico-node-kwfjv                        10.240.0.4
calico-typha-85c77f79bd-5ksvc            10.240.0.4
calico-typha-85c77f79bd-6cl7p            10.240.0.66
calico-typha-85c77f79bd-ppb8x            10.240.0.35
azure-ip-masq-agent-6np6q                10.240.0.66
azure-ip-masq-agent-dt2b7                10.240.0.4
azure-ip-masq-agent-pltj9                10.240.0.35
coredns-9d6c6c99b-5zl69                  10.240.0.28
coredns-9d6c6c99b-jzs8w                  10.240.0.85
coredns-autoscaler-599949fd86-qlwv4      10.240.0.75
kube-proxy-4tbs4                         10.240.0.35
kube-proxy-9rxr9                         10.240.0.66
kube-proxy-bjjq5                         10.240.0.4
metrics-server-77c8679d7d-dnbbt          10.240.0.89
tunnelfront-589474564b-k8s88             10.240.0.67
tigera-operator-7b555dfbdd-ww8sn         10.240.0.4
```

# The Istio service mesh

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1kFjcKrn1rkEESFOEkCljbQ.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1kFjcKrn1rkEESFOEkCljbQ.png)

Kubernetes components

There are a few ways to install **[Istio](https://istio.io/)**, helm charts, operators, or with `istioctl`. For this article, we take the easy road, the `istioctl` command.

## Istio Platform

Install and verify **[Istio](https://istio.io/)** service mesh with the following commands:

```
source env.shistioctl install--set profile=demo-y
kubectl get all--namespace istio-system
```

This should show something like the following below:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1XZlTqJhLulUPF1vNXZhc3w.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1XZlTqJhLulUPF1vNXZhc3w.png)

Deployment of Istio

## Istio addons

Download the addon manifests and install them with the following commands:

**NOTE**: The first time `kubectl apply` is run, there will be errors as CRD was not yet installed. Run `kubectl apply` again, to run the remaining manifests that depend on the CRD.

After adding these components, you can see new resources with `kubectl get all -n istio-system`:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1hL4RKgdoPEsyRusvRaH22g.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1hL4RKgdoPEsyRusvRaH22g.png)

Deployment of Istio + Addons

# The Dgraph service

**[Dgraph](https://dgraph.io/)** is a distributed graph database that can be installed with these steps below.

Save the following as `examples/dgraph/helmfile.yaml`:

**NOTE**: The namespace `dgraph` will need to have the required label `istio-injection: enabled` to signal **[Istio](https://istio.io/)** to install **[Envoy](https://www.envoyproxy.io/)** proxy side cars.

Both the namespace with the needed label and **[Dgraph](https://dgraph.io/)** can be installed and verified with these commands:

```
source env.sh
helmfile --file examples/dgraph/helmfile.yaml apply
kubectl--namespace dgraph get all
```

After about two minutes, this should show something like the following:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1haWOEaQ-HJpfBVyAPmCFeg.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1haWOEaQ-HJpfBVyAPmCFeg.png)

Deployment of Dgraph

# The pydgraph client

For the pydgraph client, we’ll run though these steps to show case **[Istio](https://istio.io/)**service mesh and Calico network policies:

1. Build `pydgraph-client` image and push to ACR
2. Deploy `pydgraph-client` in `pydgraph-allow` namespace. **[Istio](https://istio.io/)** will inject an Envoy proxy into the pod.
3. Deploy `pydgraph-client` in `pydgraph-deny` namespace.

**Fetch the build and deploy scripts**

In the previous blog, I documented steps to build and release a `pygraph-client` image, and then deploy a container using that image.

## Fetch build and deploy scripts

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1ClU8oud__fCPYC8lnMD7BA.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1ClU8oud__fCPYC8lnMD7BA.png)

Below is a script you can use to download the gists and populate the needed files run through these steps.

**NOTE**: These scripts and further details are covered in the previous article (see **[AKS with Azure Container Registry](https://joachim8675309.medium.com/aks-with-azure-container-registry-b7ff8a45a8a))**.

## Build and Push

Now that all the required source files are available, build the image:

```
source env.shaz acr login--name ${AZ_ACR_NAME}
pushd examples/pydgraph &&make build &&make push &&popd
```

## Deploy to pydgraph-deny namespace

The client in this namespace will not be apart of the service mesh.

```
helmfile \
--namespace "pydgraph-deny" \
--file examples/pydgraph/helmfile.yaml \
  apply
```

Afterward, you can check the results with `kubectl get all -n pydgraph-deny`:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1WMup_PWQ95NIYc9FwLUSFg.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1WMup_PWQ95NIYc9FwLUSFg.png)

Namespace: pydgraph-deny

## Deploy to pydgraph-allow namespace

The client in this namespace will be apart of the service mesh. Create the namespace `pydgraph-allow`, deploy pydgraph client into that namespace, and verify the results with the following commands:

The final results in `pydgraph-allow` namespace the should look similar to the following:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1gb1C7p-be-h-A2Q1wU__SA.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1gb1C7p-be-h-A2Q1wU__SA.png)

Namespace: pydgraph-allow

This will add the **[Envoy](https://www.envoyproxy.io/)** proxy sidecar container:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1xy5fLRO8beE7_7JhG9NOPQ.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1xy5fLRO8beE7_7JhG9NOPQ.png)

# Test 0 (Baseline): No Network Policy

Conduct a basic check to verify that the things are working before running any tests with network policies. In this sanity check and proceeding tests, both **[HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)** (port `8080`) and **[gRPC](https://grpc.io/)** (port `9080`) will be tested.

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1C4kQOWLBPUQ62vAg9XDm-Q.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1C4kQOWLBPUQ62vAg9XDm-Q.png)

No Network Policy

## Log into pydgraph-deny

Log into `pydgraph-deny` client:

```
PYDGRAPH_DENY_POD=$(
kubectl get pods--namespace "pydgraph-deny"--output name
)kubectl exec-ti --namespace "pydgraph-deny" \
  ${PYDGRAPH_DENY_POD} -- bash
```

## HTTP check (no network policy)

In the `pydgraph-client` container and run this command:

```
curl${DGRAPH_ALPHA_SERVER}:8080/health | jq
```

The expected results should be health status of one of the **[Dgraph](https://dgraph.io/)** Alpha nodes:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1s24SMzTa6y0i2gDOIV-YTg.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1s24SMzTa6y0i2gDOIV-YTg.png)

/health (HTTP)

## gRPC check (no network policy)

In the `pydgraph-client` container and run this command:

```
grpcurl -plaintext -proto api.proto \
${DGRAPH_ALPHA_SERVER}:9080 \
  api.Dgraph/CheckVersion
```

The expected results will be the **[Dgraph](https://dgraph.io/)** server version.

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/17G2CQ5ua_CXt9Qsy3nLqcQ.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/17G2CQ5ua_CXt9Qsy3nLqcQ.png)

api.Dgraph/CheckVersion (gRPC)

# TEST 1: Apply a network policy

The goal of this next test is to deny all traffic that is outside of service mesh. his can be done by using **[network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)** where only traffic from the service mesh is permitted.

After adding the policy, the expected results will timeouts as communication from the `pydgraph-client` that is not in the service mesh, from the `pydgraph-deny` namespace, will be blocked.

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1sp026Kv41_BAklM1CqrSwQ.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1sp026Kv41_BAklM1CqrSwQ.png)

Network Policy added to block traffic outside the mesh

## Adding a network policy

This policy will deny all traffic to the **[Dgraph](https://dgraph.io/)** Alpha pods, except for traffic from the service mesh, or more explicitly, from any pod from namespaces with the label `linkerd.io/control-plane-ns=linkerd`.

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1vTtLL8NtUs-B9cu3CWnKkg.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1vTtLL8NtUs-B9cu3CWnKkg.png)

Dgraph Network Policy for Istio (made with https://editor.cilium.io)

Copy the following and save as `examples/dgraph/network_policy.yaml`:

When ready, apply this with the following command:

```
kubectl --filename ./examples/dgraph/network_policy.yaml apply
```

## Log into pydgraph-deny

Log into `pydgraph-deny` client:

```
PYDGRAPH_DENY_POD=$(
kubectl get pods--namespace "pydgraph-deny"--output name
)kubectl exec-ti --namespace "pydgraph-deny" \
  ${PYDGRAPH_DENY_POD} -- bash
```

## HTTP check (network policy applied)

Log into the `pydgraph-client` pod, and run this command:

```
curl${DGRAPH_ALPHA_SERVER}:8080/health
```

The expected results in this case, after a very long wait (about 5 minutes) will be something similar to this:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/14eYjusWkVmzKoUlZPAgstg.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/14eYjusWkVmzKoUlZPAgstg.png)

## gRPC check (network policy apply)

Log into the `pydgraph-client` pod and run this command:

```
grpcurl -plaintext -proto api.proto \
${DGRAPH_ALPHA_SERVER}:9080 \
  api.Dgraph/CheckVersion
```

The expected results for **[gRPC](https://grpc.io/)** in about 10 seconds will be:

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1bJ8i66gcUyEtClrKDcNxVw.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1bJ8i66gcUyEtClrKDcNxVw.png)

# Test 2: Test with Envoy proxy side car

Now that we verified that network connectivity is not possible from the `pydgraph-deny` namespace, we can now try testing from `pydgraph-allow`, which has the Envoy proxy side car injected into the pod by **[Istio](https://istio.io/)**.

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/19Y_NCUyFJNXJoGwdcFX8ZQ.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/19Y_NCUyFJNXJoGwdcFX8ZQ.png)

## Log into pydgraph-allow

Log into `pydgraph-allow` client:

```
PYDGRAPH_ALLOW_POD=$(
kubectl get pods--namespace "pydgraph-allow"--output name
)kubectl exec-ti --namespace "pydgraph-allow" \
  ${PYDGRAPH_ALLOW_POD} -- bash
```

## HTTP check (namespace label applied)

Log into the `pydgraph-client` pod, and run this command:

```
curl${DGRAPH_ALPHA_SERVER}:8080/health | jq
```

The expected results for this is that JSON data about the health from one of the **[Dgraph](https://dgraph.io/)** Alpha pods.

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1PLS_CCeyKgUZi2MSwmOv_A.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1PLS_CCeyKgUZi2MSwmOv_A.png)

/health (HTTP)

## gRPC check (namespace label applied)

Log into the `pydgraph-client` pod and run this command:

```
grpcurl -plaintext -proto api.proto \
${DGRAPH_ALPHA_SERVER}:9080 \
  api.Dgraph/CheckVersion
```

The expected results for this is that JSON detailing the **[Dgraph](https://dgraph.io/)** server version.

api.Dgraph/CheckVersion (gRPC)

# Test 3: Listening to traffic steams

For this step, we will monitor traffic as it goes through the proxy and then generate some traffic. For monitoring, we’ll **[Kiali](https://kiali.io/)** graphical dashboard.

## Kiali dashboard

Run this command:

```
istioctl dashboard kiali
```

One in the dashboard, click on graph and select the `dgraph` for the `Namespace`.

## Generate Traffic

With this monitoring in place, log into the `pydgraph-client` pod and run these commands:

```
curl${DGRAPH_ALPHA_SERVER}:8080/healthgrpcurl-plaintext-proto api.proto\
 ${DGRAPH_ALPHA_SERVER}:9080 api.Dgraph/CheckVersionpython3 load_data.py--plaintext \
--alpha ${DGRAPH_ALPHA_SERVER}:9080 \
--files ./sw.nquads.rdf \
--schema ./sw.schemacurl "${DGRAPH_ALPHA_SERVER}:8080/query"--silent \
 --request POST \
--header "Content-Type: application/dql" \
 --data $'{ me(func: has(starring)) { name } }'
```

## Observe the resulting traffic

As both **[gRPC](https://grpc.io/)** and **[HTTP](https://developer.mozilla.org/docs/Web/HTTP)** traffic is generated, you can see two lines into the `demo-dgraph-alpha` service, which is depicted as a triangle △ icon.

![AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1eaYOHYb-7uck55wWylisig.png](AKS%20with%20Istio%20Service%20Mesh%208fbcbe64d5c4487ab25ed31ad426e73a/1eaYOHYb-7uck55wWylisig.png)

In the graph you can see the following content:

- Kubernetes *services* are represented by triangle △ icon and Pod *containers* as the square ◻ icon.
- Both gRPC and HTTP incoming traffic connect to the `demo-dgraph-alpha` service and then to the `alpha` container, which is called `latest`, due to lack of a `version` label.
- The Dgraph Alpha service then communicates to Dgraph zero service, also called latest, due to lack of a `version` label.

# Cleanup

This will remove the **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** cluster as well as any provisioned resources from **[AKS](https://azure.microsoft.com/services/kubernetes-service/)** including external volumes created through the **[Dgraph](https://dgraph.io/)** deployment.

```
az aks delete \
--resource-group$AZ_RESOURCE_GROUP \
--name$AZ_CLUSTER_NAME
```

# **Resources**

These are some resources I have come across when researching this article.

## Blog Source Code

## Service Mesh

General articles about service meshes.

## gRPC Load Balancing

Topics on gRPC load balancing on Kubernetes.

## Istio vs. Calico: Combining Network Policies with Istio

There are a few articles around using network policies with Istio.

- **[Network Policy and Istio: Deep Dive](https://www.tigera.io/blog/network-policy-and-istio-deep-dive/)** by Saurabh Mohan, 24 May 2017.
- **[Configuring Zero Trust Networking with Kubernetes](https://blog.darkedges.com/2019/01/17/configuring-zero-trust-with-kubernetes-Iistio-and-calico/)**, Istio and Calico, DarkEdges, 17 Jan 2019.
- **[Istio integration](https://docs.projectcalico.org/getting-started/kubernetes/hardway/istio-integration)**, Accessed 1 Aug 2021.

## Istio vs AKS: Installing Istio on AKS

These are specific pages related to AKS and Istio.

## Documentation

## Articles

Articles and blogs on Istio.

- **[StatefulSets Made Easier With Istio 1.10](https://istio.io/latest/blog/2021/statefulsets-made-easier/)** by Lin Sun, 19 May 2021.
- **[Introducing the Istio Operator](https://istio.io/latest/blog/2019/introducing-istio-operator/)** by Martin Ostrowski and Frank Budinsky, 14 Nov 2019

## Example Application

This is application from **[Istio](https://istio.io/)**. There are more examples in project source code:

# Conclusion

In this article I narrowly focused on the basics of **[Istio](https://istio.io/)** combined with network policies (**[Calico](https://www.tigera.io/project-calico/)**) for pods that are not in the mesh. One of the main reasons I wanted to look at **[Istio](https://istio.io/)** is due to issues regarding load balancing long-lived multiplexed **[gRPC](https://grpc.io/)** traffic, and the security (**[mTLS](https://www.cloudflare.com/learning/access-management/what-is-mutual-tls/)**) and observability were added bonuses.

There are a few things I would like to explore as the next step are around managing external traffic and further securing traffic within the mesh.

For traffic access or rather restricting traffic within the mesh network using **`[AuthorizationPolicy](https://istio.io/latest/docs/reference/config/security/authorization-policy/)`**, and exploring adding a later of authorization, so that service must authenticate to access the component.

## External Traffic

There comes a point where you may want to explore a service to an endpoint. **[Istio](https://istio.io/)** provides to customer resources with a **`[Gateway](https://istio.io/latest/docs/reference/config/networking/gateway/)`** resource, for L4-L6 properties of a load balancer, and a **`[VirtualService](https://istio.io/latest/docs/reference/config/networking/virtual-service/)`** resource that can be bound to a gateway to control the forwarding of traffic arriving at a particular host or gateway port.

For a public facing service, you would want to use a friendly DNS name like `https://dgraph.example.com`, as this is easier to remember than something like `https://20.69.65.109`. This can be automated with Kubernetes addons **[external-dns](https://github.com/kubernetes-sigs/external-dns)** and **[cert-manager](https://cert-manager.io/)**. Through these two addons you can automate DNS record updates and automate issuing X.509 certificate from a trusted certificate authority.

> So how do can I integrate these addons with Istio?
> 

You can integrate these addons using either the native **[Gateway](https://istio.io/latest/docs/reference/config/networking/gateway/)** and **[VirtualService](https://istio.io/latest/docs/reference/config/networking/virtual-service/)** or use an **`[ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)`** resource.

For the ingress, you can select the ingress by setting an annotation of `kubernetes.io/ingress.class: istio`. I wrote an earlier article, **[AKS with Cert Manager](https://medium.com/geekculture/aks-with-cert-manager-f24786e87b20)**, that demonstrates how to use **[ingress-nginx](https://kubernetes.github.io/ingress-nginx/)** with both **[external-dns](https://github.com/kubernetes-sigs/external-dns)** using **[Azure DNS](https://azure.microsoft.com/services/dns/)** and **[cert-manager](https://cert-manager.io/)** using **[Let’s Encrypt](https://letsencrypt.org/)**. The process is identical with exception of the annotation to select `istio` instead of `nginx`.

For **`[Gateway](https://istio.io/latest/docs/reference/config/networking/gateway/)`** and **`[VirtualService](https://istio.io/latest/docs/reference/config/networking/virtual-service/)`** resources, **[external-dns](https://github.com/kubernetes-sigs/external-dns)** has direct support to scan these sources directly. With **[cert-manager](https://cert-manager.io/)**, you would configure a **`[Certificate](https://cert-manager.io/docs/concepts/certificate/)`** resource, and then reference the secret it creates from the **`[Gateway](https://istio.io/latest/docs/reference/config/networking/gateway/)`** resource.

## Final Note

Thank you for following this article. I hope it is useful to get started and start using within your organization.