# Fully private AKS clusters — without any public ips — finally!

Created: September 28, 2022 9:17 AM
Finished: No
Source: https://denniszielke.medium.com/fully-private-aks-clusters-without-any-public-ips-finally-7f5688411184

One year after my [last post](https://medium.com/@denniszielke/setting-up-azure-firewall-for-analysing-outgoing-traffic-in-aks-55759d188039) on leveraging an azure firewall I want to revisit the scenario since we just launched a couple of new features that finally allow you to build an AKS cluster that does not use or expose any public ips. The end result should look like this and this is a guide on how you build this in your azure environment:

![Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/10Mzu_aj37loSxitXitt-0g.png](Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/10Mzu_aj37loSxitXitt-0g.png)

A fully private AKS cluster that does not need to expose or connect to public IPs.

***Update June 22nd: There have been a couple of updates and all required functionality is now GA and fully supported!***

**Update December 31st: *This scenario is now fully compatible with [Bring your own Managed Identity](https://docs.microsoft.com/en-us/azure/aks/use-managed-identity#bring-your-own-control-plane-mi) and [Uptime-SLA](https://docs.microsoft.com/en-us/azure/aks/uptime-sla) yet.***

**Update October 22nd: There are now a couple new options to ensure [DNS resolution](https://docs.microsoft.com/en-us/azure/aks/private-clusters#create-a-private-aks-cluster-with-a-public-fqdn) for private api server endpoints.**

As you might know in AKS we maintain the connection between the master and the worker nodes through a pod called “**tunnelfront**” or “**akslink**” (depending on your cluster version) which is running on your worker nodes. The connection between them is created from akslink to the master — meaning from the inside to the outside. Up until recently it was not possible to create this connection without a public IP on the masters. This is now different with [private link](https://docs.microsoft.com/en-us/azure/aks/private-clusters). Using private link we are projecting the private IP address of the AKS master, which is running in an azure managed virtual network into an ip address that is part of your AKS subnet.

We also removed the need for your worker nodes to have a public ip [for egress](https://docs.microsoft.com/en-us/azure/aks/egress) attached to their standard load balancers. However to make sure that your worker nodes are able to sync time, pull images and authenticate to azure active directory they are still in need of an internet egress path. In our case we using the azure firewall to ensure that egress path.

To get started lets first set up some variables for the following scripts which will help you to customise your deployment:

```
VNET_GROUP="${DEPLOYMENT_NAME}_networks" # here the name of the resource group for the vnet and hub resourcesKUBE_VERSION="$(az aks get-versions -l $LOCATION --query 'orchestrators[?default == `true`].orchestratorVersion' -o tsv)"
```

Now lets create the virtual networks, subnets and peerings between them:

```
az network vnet create -g $VNET_GROUP -n $HUB_VNET_NAME --address-prefixes 10.0.0.0/22az network vnet create -g $VNET_GROUP -n $KUBE_VNET_NAME --address-prefixes 10.0.4.0/22az network vnet subnet create -g $VNET_GROUP --vnet-name $HUB_VNET_NAME -n $HUB_FW_SUBNET_NAME --address-prefix 10.0.0.0/24az network vnet subnet create -g $VNET_GROUP --vnet-name $HUB_VNET_NAME -n $HUB_JUMP_SUBNET_NAME --address-prefix 10.0.1.0/24az network vnet subnet create -g $VNET_GROUP --vnet-name $KUBE_VNET_NAME -n $KUBE_ING_SUBNET_NAME --address-prefix 10.0.4.0/24az network vnet subnet create -g $VNET_GROUP --vnet-name $KUBE_VNET_NAME -n $KUBE_AGENT_SUBNET_NAME --address-prefix 10.0.5.0/24az network vnet peering create -g $VNET_GROUP -n HubToSpoke1 --vnet-name $HUB_VNET_NAME --remote-vnet $KUBE_VNET_NAME --allow-vnet-accessaz network vnet peering create -g $VNET_GROUP -n Spoke1ToHub --vnet-name $KUBE_VNET_NAME --remote-vnet $HUB_VNET_NAME --allow-vnet-access
```

Let’s configure the azure firewall public ip, the azure firewall itself and a log analytics workspace for firewall logs. To make sure that you can see outbound network traffic I would recommend to import the diagnostic dashboard via this [file](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview) as described here: [https://docs.microsoft.com/en-us/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/en-us/azure/firewall/tutorial-diagnostics).

```
az network firewall ip-config create --firewall-name $DEPLOYMENT_NAME --name $DEPLOYMENT_NAME --public-ip-address $DEPLOYMENT_NAME --resource-group $VNET_GROUP --vnet-name $HUB_VNET_NAMEFW_PRIVATE_IP=$(az network firewall show -g $VNET_GROUP -n $DEPLOYMENT_NAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

Next we are creating a user defined route which will force all traffic from the AKS subnet to the internal ip of the azure firewall (which is an ip address that we know to be 10.0.0.4, stored in $FW_PRIVATE_IP).

```
KUBE_AGENT_SUBNET_ID="/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$VNET_GROUP/providers/Microsoft.Network/virtualNetworks/$KUBE_VNET_NAME/subnets/$KUBE_AGENT_SUBNET_NAME"az network route-table route create --resource-group $VNET_GROUP --name $DEPLOYMENT_NAME --route-table-name $DEPLOYMENT_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FW_PRIVATE_IP --subscription $SUBSCRIPTION_ID
```

The next step is to create the necessary exception rules for the AKS-required network dependencies that will ensure the worker nodes can set their system time, pull ubuntu updates and retrieve the AKS kube-system container images from the Microsoft Container Registry. The following will allow dns, time and the service tags for the azure container registry.

```
az network firewall network-rule create --firewall-name $DEPLOYMENT_NAME --collection-name "time" --destination-addresses "*"  --destination-ports 123 --name "allow network" --protocols "UDP" --resource-group $VNET_GROUP --source-addresses "*" --action "Allow" --description "aks node time sync rule" --priority 101az network firewall network-rule create --firewall-name $DEPLOYMENT_NAME --collection-name "dns" --destination-addresses "*"  --destination-ports 53 --name "allow network" --protocols "UDP" --resource-group $VNET_GROUP --source-addresses "*" --action "Allow" --description "aks node dns rule" --priority 102az network firewall network-rule create --firewall-name $DEPLOYMENT_NAME --collection-name "servicetags" --destination-addresses "AzureContainerRegistry" "MicrosoftContainerRegistry" "AzureActiveDirectory" "AzureMonitor" --destination-ports "*" --name "allow service tags" --protocols "Any" --resource-group $VNET_GROUP --source-addresses "*" --action "Allow" --description "allow service tags" --priority 110
```

Not all of the dependencies can be allowed through a network rule because they neither have a service tag or a fixed ip range. So these allow rules are defined using dns, while using the AKS FQDN tags where possible (be aware that the FQDN Tag will contain the complete for all dependencies for all possible addons including all of GitHub). The latest list of these entries can be found [here](https://docs.microsoft.com/en-us/azure/aks/limit-egress-traffic) — in case you want to define your own whitelist.

```
az network firewall application-rule create --firewall-name $DEPLOYMENT_NAME --resource-group $VNET_GROUP --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 101az network firewall application-rule create  --firewall-name $DEPLOYMENT_NAME --collection-name "osupdates" --name "allow network" --protocols http=80 https=443 --source-addresses "*" --resource-group $VNET_GROUP --action "Allow" --target-fqdns "download.opensuse.org" "security.ubuntu.com" "packages.microsoft.com" "azure.archive.ubuntu.com" "changelogs.ubuntu.com" "snapcraft.io" "api.snapcraft.io" "motd.ubuntu.com"  --priority 102
```

Now we have created the virtual networks and subnets, the peering between them and a route from the AKS subnet to the internet which is going through our azure firewall that only allows the required AKS dependencies.

The current state looks like this without Kubernetes:

![Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1JzWCzVSzIO6OVSjNE7DlCg.png](Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1JzWCzVSzIO6OVSjNE7DlCg.png)

Your empty network design before you deploy the AKS cluster

I would also recommend to create a log analytics space and configure the diagnostic logs for the azure firewall to validate the traffic flows. See [here](https://docs.microsoft.com/en-us/azure/firewall/tutorial-diagnostics) and download the dashboard [here](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview).

Now lets start to create our private link enabled cluster in the AKS subnet. If you create a normal cluster, by default it will attach a public ip to the standard load balancer. In our case we want to prevent this by setting the *outboundType* to *userDefinedRouting* in our deployment and configure private cluster with a dedicated service principal. In the following we will be using [kubenet](https://docs.microsoft.com/en-us/azure/aks/configure-kubenet) as cni plugin. You can of course achieve the same using [azure cn](https://docs.microsoft.com/en-us/azure/aks/configure-azure-cni)i, but it will consume more ip addresses, which are typically a scare resource in most enterprise environments.

If you want to use a managed identity you need to create the user managed identity for the control plane ahead of time and assign it the correct permissions on the network resources.

```
az identity create --name $KUBE_NAME --resource-group $KUBE_GROUP
MSI_RESOURCE_ID=$(az identity show -n $KUBE_NAME -g $KUBE_GROUP -o json | jq -r ".id")
MSI_CLIENT_ID=$(az identity show -n $KUBE_NAME -g $KUBE_GROUP -o json | jq -r ".clientId")az aks create --resource-group $KUBE_GROUP --name $KUBE_NAME --load-balancer-sku standard --vm-set-type VirtualMachineScaleSets --enable-private-cluster --network-plugin azure --vnet-subnet-id $KUBE_AGENT_SUBNET_ID --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24 --enable-managed-identity --assign-identity $MSI_RESOURCE_ID --kubernetes-version $KUBE_VERSION --outbound-type userDefinedRouting
```

If you want to use a service principal (Managed Identities are recommended) you can use the following:

```
az role assignment create --role "Contributor" --assignee $SERVICE_PRINCIPAL_ID -g $VNET_GROUP
az aks create --resource-group $KUBE_GROUP --name $KUBE_NAME --load-balancer-sku standard --vm-set-type VirtualMachineScaleSets --enable-private-cluster --network-plugin kubenet --vnet-subnet-id $KUBE_AGENT_SUBNET_ID --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24 --service-principal $SERVICE_PRINCIPAL_ID --client-secret $SERVICE_PRINCIPAL_SECRET --kubernetes-version $KUBE_VERSION --outbound-type userDefinedRouting
```

After the deployment went through you need to create a link in your DNS zone to the hub net to make sure that your jumpbox is correctly able to resolve the dns name of your private link enabled cluster using kubectl from the jumbox. This will ensure that your jumpbox can resolve the private ip of the api server using azure dns. In case you need to bring your own private DNS resolution implementation I encourage you to check [this](https://docs.microsoft.com/en-us/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) out.

```
az network private-dns link vnet create --name "hubnetdnsconfig" --registration-enabled false --resource-group $NODE_GROUP --virtual-network $HUB_VNET_ID --zone-name $DNS_ZONE_NAME
```

![Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1ECrgvhhj1XFi1Ee5o9LK1w.png](Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1ECrgvhhj1XFi1Ee5o9LK1w.png)

This is how your private dns zone should look like after the registration.

The final task is to deploy an Ubuntu Jumpbox into the JumboxSubnet so that you can, configure [azure cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest) and [pull](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#get-and-verify-the-configuration-information) kubeconfig for the private cluster. The end result should look like this:

![Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1O8FFHdiLeQr4_42bkJmh4w.png](Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1O8FFHdiLeQr4_42bkJmh4w.png)

A private link enabled cluster without any public ips on the worker nodes

Finally we also want to add logging to our AKS cluster by deploying a log analytics space and connect our AKS monitoring addon to use that space.

If everything worked out you can create a jumpbox in the jumpbox-subnet, install the azure cli, and kubectl, pull the kubeconfig and connect to your now fully private cluster.

![Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1KdTYFHTWlYVTYPqFKfn47w.png](Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1KdTYFHTWlYVTYPqFKfn47w.png)

Your very own fully private cluster

Same as before you can now launch a container see if its access attempts to the internet are correctly blocked. In this case we need to allow access to docker hub in our firewall.

```
az network firewall application-rule create  --firewall-name $DEPLOYMENT_NAME --collection-name "dockerhub" --name "allow network" --protocols http=80 https=443 --source-addresses "*" --resource-group $VNET_GROUP --action "Allow" --target-fqdns "auth.docker.io" "registry-1.docker.io" --priority 110cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centoss
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```

![Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1W0eUDTRTeDyxDrgmnns-oQ.png](Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1W0eUDTRTeDyxDrgmnns-oQ.png)

Azure firewall working as expected, allowing www.ubuntu.com but blocking everything else.

DNS resolution as described above will only work if you are trying to design a solution where each private cluster will never have to communicate with other private link resources in other vnets. In a lot of cases this will not be sufficient for example when you have private link enabled resources (like a container registry) in the hub vnet or your cluster want to communicate to the private api server of other clusters in other vnets. Since a vnet can only be linked to a single dns zone but a dns zone can be linked to multiple vnets you have to do some preparations.

You can avoid using a private dns zone if you allow AKS to create a public DNS entry in an Azure public DNS zone (azmk8s.io) for your private link ip. When specifying ( — private-dns-zone none) this means that no private dns zone will be created and AKS will be responsible with managing that api server dns entry. However in some cases the public announcement of private ip via public dns is been seen as a problem when trying to comply with [PCI](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/containers/aks-pci/aks-pci-network#requirement-137) rules which means you have to set enablePrivateClusterPublicFQDN to false and work out a private dns resolution for your network.

![Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1VU53cnEagv2foUC8IFP1sw.png](Fully%20private%20AKS%20clusters%20%E2%80%94%20without%20any%20public%20ip%2029dc2f5200a04ab5bcba8d829e423026/1VU53cnEagv2foUC8IFP1sw.png)

Using multiple cluster in the same dns zone requires more preparation to implement

As shown in the picture the scenario with a shared dns zone across multiple clusters will require some preparation in advance. Since the hub vnet will typically belong to a hub subscription the private dns zone needs to be created in the hub subscription. In order for any AKS cluster to reference the private dns zone the controller manager identity of the cluster needs to have contributor permissions on the dns zone before the AKS cluster can be created. This is not only required during creation but also for the lifecycle of the cluster since the private endpoint ip of an AKS cluster can change and the AKS service needs to have the ability to adjust the DNS entry accordingly to keep the cluster running.

The domain name of the dns zone needs to follow the naming convention of privatelink.region.azmk8s.io or provide a fqdn subdomain of it. You will have the ability for each cluster to have a unique subdomain to ensure predictable api server domain names.

Even though in theory you can use host files or your dns resolvers to map the dns entries to the api server endpoint this is not recommended. It is possible that the api server endpoint ip of a private cluster can change during internal failover/ maintenance operations and it can also change after a start/stop operation of the cluster. AKS will update the dns entries accordingly but your own solution will most likely not be able to do perform the updates before your cluster will stop working.

Still to be solved is how you can ensure routing from on prem network. The simple way is to use a [DNAT](https://docs.microsoft.com/en-us/azure/firewall/tutorial-firewall-dnat) rule, which unfortunately only works when you target the public ip of your azure firewall as ingress point and not the private ip of the azure firewall. The more complicated setup requires gateway transit and remote gateways as described [here](https://docs.microsoft.com/en-us/azure/firewall/tutorial-hybrid-ps).

The end ;)