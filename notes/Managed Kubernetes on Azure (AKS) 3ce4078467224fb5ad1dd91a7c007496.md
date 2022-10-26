# Managed Kubernetes on Azure (AKS)

Created: September 28, 2022 9:19 AM
Finished: No
Source: https://koukia.ca/managed-kubernetes-on-azure-aks-8514ac0cd8aa

![Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/15X_qkpTFBxYIZrlbi7Z55g.png](Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/15X_qkpTFBxYIZrlbi7Z55g.png)

In [a previous post](https://koukia.ca/create-a-kubernetes-cluster-in-azure-container-service-28e281896), I showed you how to create a Kubernetes Cluster, on Azure Container Service (Considered an IaaS offering, since you manage all the resources) and as you could see , it was pretty straightforward.

Today I will walk you through a new service on Azure called Managed Kubernetes or AKS, and this is even easier.

# K8s on ACS vs AKS

So the question is, what’s the difference between Kubernetes on Azure Container Service and Manage Kubernetes or AKS?

As the following images sums it up, in AKS, the Control Plane nodes are managed for you, where in K8s on Azure Container Service, you are responsible for managing the control plane VMs.

![Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/1H1DqxPqWGfGgBuZZLBD9oQ.png](Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/1H1DqxPqWGfGgBuZZLBD9oQ.png)

![Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/1PFIvn5HjItsRc1SDPdOp6w.png](Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/1PFIvn5HjItsRc1SDPdOp6w.png)

# Creating a Resource Group

So the first step to create a Kubernetes Cluster in AKS is to create a resource group. Note that, AKS is currently only available in Central US, East US and West Europe, so make sure you create your resource group in one of these regions.

I tend to like the cloud console on Azure portal. So you can open the cloud console and use the following command to create the Resource Group.

```
az group create -l eastus -n aks-cluster-rg
```

# Creating the Kubernetes Cluster

Now that your Resource Groups is created, you can use the following command to create the Kubernetes Cluster:

```
az aks create -n aks-cluster -g aks-cluster-rg — generate-ssh-key
```

Note: If you have tried to do this through the UI, you realize tat getting an “ssh key” is a painful process on its own, so I would stick to the cloud console!

So at this point your console should look like the following picture:

![Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/1Pz-rgNqe1MUDnOo2F2AybQ.png](Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/1Pz-rgNqe1MUDnOo2F2AybQ.png)

After a few minutes (this creates 3 VMs and a lot of other storage and resources, so expect it to take a little while), you should see the following success message:

![Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/16g70NK19qsqFhhn5Y6MpnQ.png](Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/16g70NK19qsqFhhn5Y6MpnQ.png)

Now, if you navigate to your resources, you should see your Container Service (Managed) resource in the portal.

![Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/17jI5ShyOZrMl0kb5Mite6g.png](Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/17jI5ShyOZrMl0kb5Mite6g.png)

The weird thing I noticed is, the “Container Cluster” resource is created in the Resource Group we specified in our command, but all other resources related to the Cluster were created in a different resource group, as you can see in the following picture.

![Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/14z5zNqQjzP37ETGcWtgRmg.png](Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/14z5zNqQjzP37ETGcWtgRmg.png)

# Upgrade the Kubernetes Cluster

If you would like to upgrade the Kubernetes Cluster version, (currently it installs version 1.7.1) you can use the following command:

```
az aks upgrade -g aks-cluster-rg -n aks-cluster -k 1.8.1
```

“-g” represents the Resource Group name, and “-n” is the name of the Kubernetes Cluster.

At this point you should see the following in your cloud console:

![Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/17teQYTnL15TK8_mxHdFXeA.png](Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/17teQYTnL15TK8_mxHdFXeA.png)

# Accessing Kubernetes Dashboard

To access the Kubenetes dashboard you can try the following command:

```
az aks browse -g aks-cluster-rg -n aks-cluster
```

And you should see the following message in the console:

![Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/1T70WfOr4rZCmStw3IS5PyQ.png](Managed%20Kubernetes%20on%20Azure%20(AKS)%203ce4078467224fb5ad1dd91a7c007496/1T70WfOr4rZCmStw3IS5PyQ.png)

Unfortunately, as you see above, looks like browsing the dashboard is also not ready in the AKS at the moment as this is still in preview.

Welcome to the bleeding edge of technology!

# Some notes on current Mnaged Kubernetes (AKS) limitations

- Windows containers are not supported yet in AKS, but it is in their road-map.
- Managed Kubernetes currently is only available in East US, Central US and West Europe regions.
- Browsing Kubernetes dashboard is not available at the moment.

Cheers!