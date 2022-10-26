# GitLab runner setup — CI/CD on AKS (Azure Kubernetes Service)

Created: September 27, 2022 8:21 PM
Finished: No
Source: https://faun.pub/gitlab-runner-setup-ci-cd-on-aks-azure-kubernetes-service-8ff3328ba5e9
Tags: #gitlab

Runners are processes that pick up and execute jobs for GitLab. You can install the runner manually on your local machine, [using docker on your local machine](https://jackwesleyroper.medium.com/gitlab-runner-setup-run-in-docker-container-on-windows-44fee102d02e), a VM or automatically on a Kubernetes cluster.

The following steps should help you register an AKS cluster with GitLab and then install the agent on the cluster, and then register the agent with GitLab. I’m using GitLab Enterprise Edition 13.2.4–ee.

Firstly create an AKS cluster on Azure, once complete you’ll see something similar to below:

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/13mqrPernFtV-x_73l0CqRw.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/13mqrPernFtV-x_73l0CqRw.png)

In Gitlab, first, enable CI/CD for the project under settings -> general -> pipelines.

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/11UWEQQqryWYe2Rr5gHfk5Q.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/11UWEQQqryWYe2Rr5gHfk5Q.png)

You should now see a CI/CD option in the settings menu, again in the project you wish to enable CI/CD for, go to Settings -> CI/CD -> Install runner on Kubernetes:

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1QTzReopmvCD0cdwPYxo9Rg.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1QTzReopmvCD0cdwPYxo9Rg.png)

Add Kubernetes Cluster:

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1jqA4hbjTL2bWgj7oacqr8g.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1jqA4hbjTL2bWgj7oacqr8g.png)

Add existing cluster tab and fill in the details as follows:

1. **Kubernetes cluster name** (required) — The name you wish to give the cluster.
2. **Environment scope** (required) — The [associated environment](https://faun.pub/index.md#setting-the-environment-scope-premium) to this cluster.
3. **API URL** (required) — Get this from the Azure portal and add https:// in front of the URL, e.g. kubegitlabrunner-dns-322ea5bc.hcp.uksouth.azmk8s.io
4. **CA certificate** (required) — A valid Kubernetes certificate is needed to authenticate to the cluster. We will use the certificate created by default.
- Connect to the Kubernetes Cluster, hit connect in the portal to get the commands to run in azure cloud shell.

```
az aks get-credentials — resource-group <RG> — name <KubeName>
```

- List the secrets with `kubectl get secrets`, and one should be named similar to `default-token-xxxxx`. Copy that token name for use below.

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1BulGcpdjJJLgA8itB9Q8tw.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1BulGcpdjJJLgA8itB9Q8tw.png)

- Get the certificate by running this command:

`kubectl get secret <secret name> -o jsonpath="{['data']['ca\.crt']}" | base64 --decode`

**5. Token** — GitLab authenticates against Kubernetes using service tokens, which are scoped to a particular `namespace`. **The token used should belong to a service account with `[cluster-admin](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles)` privileges.** To create this service account:

- Create a file called `gitlab-admin-service-account.yaml` with contents:

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: gitlab-admin
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: gitlab-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: gitlab-admin
    namespace: kube-system
```

- Apply the service account and cluster role binding to your cluster:
- `kubectl apply -f gitlab-admin-service-account.yaml`

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1enquUXrEKkJLwGFXYoyD5w.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1enquUXrEKkJLwGFXYoyD5w.png)

- Retrieve the token for the `gitlab-admin` service account:
- `kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep gitlab-admin | awk '{print $1}')`
- Copy the `<authentication_token>` value from the output:

**6. GitLab-managed cluster** — Leave this checked if you want GitLab to manage namespaces and service accounts for this cluster.

**7. Project namespace** (optional) — You don’t have to fill it in; by leaving it blank, GitLab will create one for you. Also:

- Each project should have a unique namespace.
- The project namespace is not necessarily the namespace of the secret, if you’re using a secret with broader permissions, like the secret from `default`.
- You should **not** use `default` as the project namespace.
- If you or someone created a secret specifically for the project, usually with limited permissions, the secret’s namespace and project namespace may be the same.

8. Finally, click the **Create Kubernetes cluster** button.

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1a2XVfDVacpwpJPxiUs0-Qw.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1a2XVfDVacpwpJPxiUs0-Qw.png)

Your AKS cluster is connected to GitLab. Now you need to install Prometheus for monitoring and the GitLab runner itself on the cluster.

Click on the applications tab:

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/18As_qdqEKXh0IaeBQM6S7g.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/18As_qdqEKXh0IaeBQM6S7g.png)

At this point, you can hit install and GitLab should install the runner and Prometheus. Under namespaces in the AKS section of the Azure portal you’ll see the newly created ‘gitlab-managed-apps’.

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1FcQqJJMTw-IZjTXBbou_qw.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1FcQqJJMTw-IZjTXBbou_qw.png)

All looks good and this will likely be the end of the story…

However, I received the following error:

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1bQIDsldVMoBBemRMH8ling.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1bQIDsldVMoBBemRMH8ling.png)

So check the log using:

```
kubectl logs -n gitlab-managed-apps install-runner
```

I saw the following error:

Adding stable repo with URL: [https://kubernetes-charts.storage.googleapis.com](https://kubernetes-charts.storage.googleapis.com/)
Error: error initializing: Looks like “[https://kubernetes-charts.storage.googleapis.com](https://kubernetes-charts.storage.googleapis.com/)" is not a valid chart repository or cannot be reached: Failed to fetch [https://kubernetes-charts.storage.googleapis.com/index.yaml](https://kubernetes-charts.storage.googleapis.com/index.yaml) : 403 Forbidden

I think the GitLab runner install script is using the old API address. The new one should be [https://charts.helm.sh/stable](https://charts.helm.sh/stable) according to this source: [https://gitmemory.com/issue/SeldonIO/seldon-core/2808/758254624](https://gitmemory.com/issue/SeldonIO/seldon-core/2808/758254624)

I proceeded to attempt to install the runner manually on the AKS cluster using the following commands (creates a new namespace called GitLab first)

```
kubectl create namespace gitlab
helm repo add gitlabhttp://charts.gitlab.io/
helm install my-gitlab-runner gitlab/gitlab-runner --version 0.27.0 -n gitlab
```

FYI I found the above here: [https://artifacthub.io/packages/helm/gitlab/gitlab-runner?modal=install](https://artifacthub.io/packages/helm/gitlab/gitlab-runner?modal=install)

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1rtDd1ikrk8tbe2R9TEuzmw.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1rtDd1ikrk8tbe2R9TEuzmw.png)

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1Rj2tXwiyosozUrT4gTF1Jg.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1Rj2tXwiyosozUrT4gTF1Jg.png)

Lastly navigate back to Gitlab to get the URL and token under settings -> CI/CD -> Runners, and look under ‘set up a specific runner manually’:

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1Ni-jlXkXKSnCAGlViZeTqA.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1Ni-jlXkXKSnCAGlViZeTqA.png)

Run the command (adding your URL and token):

```
helm upgrade my-gitlab-runner \
        --set gitlabUrl=<URL>,runnerRegistrationToken=<TOKEN> \        gitlab/gitlab-runner -n gitlab
```

It should return the following:

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1hQVsdHMTfGYAYfUBuIET9A.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1hQVsdHMTfGYAYfUBuIET9A.png)

List the pods to check status:

```
kubectl get pods -n gitlab
```

![GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1x7OGOXziGm06VFl25EGm-Q.png](GitLab%20runner%20setup%20%E2%80%94%20CI%20CD%20on%20AKS%20(Azure%20Kubernet%2056cb54d0d7c64206b6c37058e32bda21/1x7OGOXziGm06VFl25EGm-Q.png)

Check some new code into GitLab and the runner should do its thing!

You can find more info on configuring GitLab runners here:

Want more Kubernetes content? Check out my other K8S articles [here](https://jackwesleyroper.medium.com/kubernetes-k8s-related-articles-index-54718769e390)!

Cheers! 🍻

**If this post was helpful, please click the clap 👏 button below a few times to show your support for the author 👇**