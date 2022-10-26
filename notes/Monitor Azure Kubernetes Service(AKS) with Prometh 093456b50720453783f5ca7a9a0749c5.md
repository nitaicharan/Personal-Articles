# Monitor Azure Kubernetes Service(AKS) with Prometheus and Grafana

Created: September 27, 2022 8:21 PM
Finished: No
Source: https://shailender-choudhary.medium.com/monitor-azure-kubernetes-service-aks-with-prometheus-and-grafana-8e2fe64d1314

![Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1m3cQWKux9t64cCbgZioUqA.png](Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1m3cQWKux9t64cCbgZioUqA.png)

Prometheus is an open-source systems monitoring and alerting toolkit. Prometheus collects and stores its metrics as time series data, i.e. metrics information is stored with the timestamp at which it was recorded, alongside optional key-value pairs called labels. Grafana is an open-source platform for monitoring and observability. It allows you to query, visualize, alert on and understand your metrics no matter where they are stored.

We will use Helm to install and manage Prometheus and Grafana on AKS cluster.

# Steps to be followed

1. Install Prometheus and Grafana using Helm
2. Setup Port-forwarding for both Prometheus and Grafana
3. Create a Service Principal and add roles to AKS cluster RG
4. Create Data Source in Grafana
5. Import Azure Monitor for Containers in Grafana
6. View the metrics in Grafana Dashboard

# Install Prometheus and Grafana

```
# Define public Kubernetes chart repository in the Helm configuration
helm repo add prometheus-communityhttps://prometheus-community.github.io/helm-charts# Update local repositories
helm repo update# Search for newly installed repositories
helm repo list# Create a namespace for Prometheus and Grafana resources
kubectl create ns prometheus# Install Prometheus using HELM
helm install prometheus prometheus-community/kube-prometheus-stack -n prometheus# Check all resources in Prometheus Namespace
kubectl get all -n prometheus
```

![Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/14lYwmoqlvJ-AuR5aMg-fwQ.png](Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/14lYwmoqlvJ-AuR5aMg-fwQ.png)

# Configure Prometheus and Grafana

```
# Port forward the Prometheus service
kubectl port-forward -n prometheus prometheus-prometheus-kube-prometheus-prometheus-0 9090

```

Open localhost:9090 in browser.

Prometheus uses Prometheus Query Language(PQL) to query Kubernetes metrics which is not easy to visualise. Search for API Server requests in Prometheus Dashboard.

![Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1F1CSFuql6dwOEdnwPZFWEA.png](Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1F1CSFuql6dwOEdnwPZFWEA.png)

In order to login to the Grafana dashboard, username and password are required which can be retrieved by using the below command:

```
# Port forward the Prometheus service
kubectl port-forward -n prometheus prometheus-prometheus-kube-prometheus-prometheus-0 9090# Get the Username
kubectl get secret -n prometheus prometheus-grafana -o=jsonpath='{.data.admin-user}' |base64 -d# Get the Password
kubectl get secret -n prometheus prometheus-grafana -o=jsonpath='{.data.admin-password}' |base64 -d# Port forward the Grafana service
kubectl port-forward -n prometheus prometheus-grafana-5449b6ccc9-b4dv4 3000
```

Open localhost:3000 in browser and login using the username and password.

# Create a Service Principal and Assign Role

Create SPN and assign Monitoring Reader Role on the AKS Cluster ResourceGroup.

```
az ad sp create-for-rbac --role="Monitoring Reader" --scopes="/subscriptions/xxxxxx-xxxx-xxxx-xxxxx/resourceGroups/aksdemocluster-rg"
```

You will get the Output like this.

![Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1vV3qlkqGWi7uxwjV6Pa0vQ.png](Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1vV3qlkqGWi7uxwjV6Pa0vQ.png)

Save this output as we will use the app ID, tenant ID, and password later on.

# Create Data Source in Grafana

Go to configuration and click Data Sources.

Click on Add Data Source and search for Azure Monitor.

Provide the required SPN values and save.

![Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1bGQUEsCswqIXbJFCj1NZWQ.png](Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1bGQUEsCswqIXbJFCj1NZWQ.png)

# Import Azure Monitor for Containers in Grafana

Go to Create and click import.

Import Dashboard ID 10956 and load and finally import the “Azure Monitor for Containers — Metrics” Dashboard.

![Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1EbhsC7max_vhHd_Vc4KNEQ.png](Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1EbhsC7max_vhHd_Vc4KNEQ.png)

# View the metrics in Grafana Dashboard

Go to Dashboards and check multiple dashboards to get detailed mertics.

![Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1xgCaopxIE93V9aQ5vNyQmA.png](Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1xgCaopxIE93V9aQ5vNyQmA.png)

![Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1ZmqhUbeBUVYAT53acHoXYQ.png](Monitor%20Azure%20Kubernetes%20Service(AKS)%20with%20Prometh%20093456b50720453783f5ca7a9a0749c5/1ZmqhUbeBUVYAT53acHoXYQ.png)

Dashboards give us a visual representation of the AKS cluster’s health, resource utilisation trends of specific application pods, network traffic flow across the cluster, and much more. Prometheus and Grafana are powerful monitoring tools for Kubernetes clusters, and Helm makes it simple to set up and get up and running in minutes.

For more AKS information go to my YouTube channel:[https://www.youtube.com/channel/UCkJRkUw2hYSlleTUuHY43lQ](https://www.youtube.com/channel/UCkJRkUw2hYSlleTUuHY43lQ)