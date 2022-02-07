---
title: Create a private Azure Kubernetes Service cluster
description: Learn how to create a private Azure Kubernetes Service (AKS) cluster
services: container-service
ms.topic: article
ms.date: 01/12/2022

---

# Create a private Azure Kubernetes Service cluster

In a private cluster, the control plane or API server has internal IP addresses that are defined in the [RFC1918 - Address Allocation for Private Internet](https://tools.ietf.org/html/rfc1918) document. By using a private cluster, you can ensure network traffic between your API server and your node pools remains on the private network only.

The control plane or API server is in an Azure Kubernetes Service (AKS)-managed Azure subscription. A customer's cluster or node pool is in the customer's subscription. The server and the cluster or node pool can communicate with each other through the [Azure Private Link service][private-link-service] in the API server virtual network and a private endpoint that's exposed in the subnet of the customer's AKS cluster.

When you provision a private AKS cluster, AKS by default creates a private FQDN with a private DNS zone and an additional public FQDN with a corresponding A record in Azure public DNS. The agent nodes still use the A record in the private DNS zone to resolve the private IP address of the private endpoint for communication to the API server.  

## Region availability

Private cluster is available in public regions, Azure Government, and Azure China 21Vianet regions where [AKS is supported](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Azure Government sites are supported, however US Gov Texas isn't currently supported because of missing Private Link support.

## Prerequisites

* Azure CLI >= 2.28.0 or Azure CLI with aks-preview extension 0.5.29 or later.
* If using ARM or the rest API, the AKS API version must be 2021-05-01 or later.
* The Private Link service is supported on Standard Azure Load Balancer only. Basic Azure Load Balancer isn't supported.  
* To use a custom DNS server, add the Azure DNS IP 168.63.129.16 as the upstream DNS server in the custom DNS server.

## Create a private AKS cluster

### Create a resource group

Create a resource group or use an existing resource group for your AKS cluster.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### Default basic networking 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```

Where `--enable-private-cluster` is a mandatory flag for a private cluster. 

### Advanced networking  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Where `--enable-private-cluster` is a mandatory flag for a private cluster. 

> [!NOTE]
> If the Docker bridge address CIDR (172.17.0.1/16) clashes with the subnet CIDR, change the Docker bridge address appropriately.

## Disable Public FQDN

The following parameters can be leveraged to disable Public FQDN.

### Disable Public FQDN on a new AKS cluster

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <private-dns-zone-mode> --disable-public-fqdn
```

### Disable Public FQDN on an existing cluster

```azurecli-interactive
az aks update -n <private-cluster-name> -g <private-cluster-resource-group> --disable-public-fqdn
```

## Configure Private DNS Zone 

The following parameters can be leveraged to configure Private DNS Zone.

- "system", which is also the default value. If the --private-dns-zone argument is omitted, AKS will create a Private DNS Zone in the Node Resource Group.
- "none", defaults to public DNS which means AKS will not create a Private DNS Zone.  
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID", which requires you to create a Private DNS Zone in this format for Azure global cloud: `privatelink.<region>.azmk8s.io` or `<subzone>.privatelink.<region>.azmk8s.io`. You will need the Resource ID of that Private DNS Zone going forward.  Additionally, you will need a user assigned identity or service principal with at least the `private dns zone contributor`  and `vnet contributor` roles.
  - If the Private DNS Zone is in a different subscription than the AKS cluster, you need to register Microsoft.ContainerServices in both the subscriptions.
  - "fqdn-subdomain" can be utilized with "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" only to provide subdomain capabilities to `privatelink.<region>.azmk8s.io`

### Create a private AKS cluster with Private DNS Zone

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```
### Create a private AKS cluster with a BYO Private DNS SubZone

Prerequisites:

* Azure CLI >= 2.32.0 or later.

### Create a private AKS cluster with Custom Private DNS Zone or Private DNS SubZone

```azurecli-interactive
# Custom Private DNS Zone name should be in format "<subzone>.privatelink.<region>.azmk8s.io"
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone or custom private dns subzone ResourceId>
```

### Create a private AKS cluster with Custom Private DNS Zone and Custom Subdomain

```azurecli-interactive
# Custom Private DNS Zone name could be in formats "privatelink.<region>.azmk8s.io" or "<subzone>.privatelink.<region>.azmk8s.io"
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain>
```

## Options for connecting to the private cluster

The API server endpoint has no public IP address. To manage the API server, you'll need to use a VM that has access to the AKS cluster's Azure Virtual Network (VNet). There are several options for establishing network connectivity to the private cluster.

* Create a VM in the same Azure Virtual Network (VNet) as the AKS cluster.
* Use a VM in a separate network and set up [Virtual network peering][virtual-network-peering].  See the section below for more information on this option.
* Use an [Express Route or VPN][express-route-or-VPN] connection.
* Use the [AKS `command invoke` feature][command-invoke].
* Use a [private endpoint][private-endpoint-service] connection.

Creating a VM in the same VNET as the AKS cluster is the easiest option. Express Route and VPNs add costs and require additional networking complexity. Virtual network peering requires you to plan your network CIDR ranges to ensure there are no overlapping ranges.

## Virtual network peering

As mentioned, virtual network peering is one way to access your private cluster. To use virtual network peering, you need to set up a link between virtual network and the private DNS zone.
    
1. Go to the node resource group in the Azure portal.  
2. Select the private DNS zone.   
3. In the left pane, select the **Virtual network** link.  
4. Create a new link to add the virtual network of the VM to the private DNS zone. It takes a few minutes for the DNS zone link to become available.  
5. In the Azure portal, navigate to the resource group that contains your cluster's virtual network.  
6. In the right pane, select the virtual network. The virtual network name is in the form *aks-vnet-\**.  
7. In the left pane, select **Peerings**.  
8. Select **Add**, add the virtual network of the VM, and then create the peering.  
9. Go to the virtual network where you have the VM, select **Peerings**, select the AKS virtual network, and then create the peering. If the address ranges on the AKS virtual network and the VM's virtual network clash, peering fails. For more information, see  [Virtual network peering][virtual-network-peering].

## Hub and spoke with custom DNS

[Hub and spoke architectures](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) are commonly used to deploy networks in Azure. In many of these deployments, DNS settings in the spoke VNets are configured to reference a central DNS forwarder to allow for on-premises and Azure-based DNS resolution. When deploying an AKS cluster into such a networking environment, there are some special considerations that must be taken into account.

![Private cluster hub and spoke](media/private-clusters/aks-private-hub-spoke.png)

1. By default, when a private cluster is provisioned, a private endpoint (1) and a private DNS zone (2) are created in the cluster-managed resource group. The cluster uses an A record in the private zone to resolve the IP of the private endpoint for communication to the API server.

2. The private DNS zone is linked only to the VNet that the cluster nodes are attached to (3). This means that the private endpoint can only be resolved by hosts in that linked VNet. In scenarios where no custom DNS is configured on the VNet (default), this works without issue as hosts point at 168.63.129.16 for DNS that can resolve records in the private DNS zone because of the link.

3. In scenarios where the VNet containing your cluster has custom DNS settings (4), cluster deployment fails unless the private DNS zone is linked to the VNet that contains the custom DNS resolvers (5). This link can be created manually after the private zone is created during cluster provisioning or via automation upon detection of creation of the zone using event-based deployment mechanisms (for example, Azure Event Grid and Azure Functions).

> [!NOTE]
> If you are using [Bring Your Own Route Table with kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) and Bring Your Own DNS with Private Cluster, the cluster creation will fail. You will need to associate the [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) in the node resource group to the subnet after the cluster creation failed, in order to make the creation successful.

## Use a private endpoint connection

A private endpoint can be set up so that an Azure Virtual Network doesn't need to be peered to communicate to the private cluster. To use a private endpoint, create a new private endpoint in your virtual network then create a link between your virtual network and a new private DNS zone.

> [!IMPORTANT]
> If the virtual network is configured with custom DNS servers, private DNS will need to be set up appropriately for the environment. See the [virtual networks name resolution documentation][virtual-networks-name-resolution] for more details.

1. On the Azure portal menu or from the Home page, select **Create a resource**.
2. Search for **Private Endpoint** and select **Create > Private Endpoint**.
3. Select **Create**.
4. On the **Basics** tab, set up the following options:
    * **Project details**:
      * Select an Azure **Subscription**.
      * Select the Azure **Resource group** where your virtual network is located.
    * **Instance details**:
      * Enter a **Name** for the private endpoint, such as *myPrivateEndpoint*.
      * Select a **Region** for the private endpoint.
  
  > [!IMPORTANT]
  > Check that the region selected is the same as the virtual network where you want to connect from, otherwise you won't see your virtual network in the **Configuration** tab.

5. Select **Next: Resource** when complete.
6. On the **Resource** tab, set up the following options:
    * **Connection method**: *Connect to an Azure resource in my directory*
    * **Subscription**: Select your Azure Subscription where the private cluster is located
    * **Resource type**: *Microsoft.ContainerService/managedClusters*
    * **Resource**: *myPrivateAKSCluster*
    * **Target sub-resource**: *management*
7. Select **Next: Configuration** when complete.
8. On the **Configuration** tab, set up the following options:
    * **Networking**:
      * **Virtual network**: *myVirtualNetwork*
      * **Subnet**: *mySubnet*
9.  Select **Next: Tags** when complete.
10. (Optional) On the **Tags** tab, set up key-values as needed.
11. Select **Next: Review + create**, and then select **Create** when validation completes. 

Record the private IP address of the private endpoint. This private IP address is used in a later step.

After the private endpoint has been created, create a new private DNS zone with the same name as the private DNS zone that was created by the private cluster. 

1. Go to the node resource group in the Azure portal.  
2. Select the private DNS zone and record:
   * the name of the private DNS zone, which follows the pattern `*.privatelink.<region>.azmk8s.io`
   * the name of the A record (excluding the private DNS name)
   * the time-to-live (TTL)
3. On the Azure portal menu or from the Home page, select **Create a resource**.
4. Search for **Private DNS zone** and select **Create > Private DNS Zone**.
5. On the **Basics** tab, set up the following options:
     * **Project details**:
       * Select an Azure **Subscription**
       * Select the Azure **Resource group** where the private endpoint was created
     * **Instance details**:
       * Enter the **Name** of the DNS zone retrieved from previous steps
       * **Region** defaults to the Azure Resource group location
6. Select **Review + create** when complete and select **Create** when validation completes.

After the private DNS zone is created, create an A record. This record associates the private endpoint to the private cluster.

1. Go to the private DNS zone created in previous steps.
2. On the **Overview** page, select **+ Record set**.
3. On the **Add record set** tab, set up the following options:
   * **Name**: Input the name retrieved from the A record in the private cluster's DNS zone
   * **Type**: *A - Alias record to IPv4 address*
   * **TTL**: Input the number to match the record from the A record private cluster's DNS zone
   * **TTL Unit**: Change the dropdown value to match the A record from the private cluster's DNS zone
   * **IP address**: Input the IP address of the private endpoint that was created previously

> [!IMPORTANT]
> When creating the A record, use only the name, and not the fully qualified domain name (FQDN).

Once the A record is created, link the private DNS zone to the virtual network that will access the private cluster.

1. Go to the private DNS zone created in previous steps.  
2. In the left pane, select **Virtual network links**.  
3. Create a new link to add the virtual network to the private DNS zone. It takes a few minutes for the DNS zone link to become available.

> [!WARNING]
> If the private cluster is stopped and restarted, the private cluster's original private link service is removed and re-created, which breaks the connection between your private endpoint and the private cluster. To resolve this issue, delete and re-create any user created private endpoints linked to the private cluster. DNS records will also need to be updated if the re-created private endpoints have new IP addresses.

## Limitations 
* IP authorized ranges can't be applied to the private API server endpoint, they only apply to the public API server
* [Azure Private Link service limitations][private-link-service] apply to private clusters.
* No support for Azure DevOps Microsoft-hosted Agents with private clusters. Consider using [Self-hosted Agents](/azure/devops/pipelines/agents/agents?tabs=browser). 
* If you need to enable Azure Container Registry to work with a private AKS cluster, [set up a private link for the container registry in the cluster virtual network][container-registry-private-link] or set up peering between the Container Registry virtual network and the private cluster's virtual network.
* No support for converting existing AKS clusters into private clusters
* Deleting or modifying the private endpoint in the customer subnet will cause the cluster to stop functioning. 

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[private-endpoint-service]: ../private-link/private-endpoint-overview.md
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
[command-invoke]: command-invoke.md
[container-registry-private-link]: ../container-registry/container-registry-private-link.md
[virtual-networks-name-resolution]: ../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server