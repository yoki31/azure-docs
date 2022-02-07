---
title: Integrate your app with an Azure virtual network
description: Integrate your app in Azure App Service with Azure virtual networks.
author: madsd
ms.topic: conceptual
ms.date: 01/26/2022
ms.author: madsd

---
# Integrate your app with an Azure virtual network

This article describes the Azure App Service VNet integration feature and how to set it up with apps in [App Service](./overview.md). With [Azure virtual networks](../virtual-network/virtual-networks-overview.md) (VNets), you can place many of your Azure resources in a non-internet-routable network. The App Service VNet integration feature enables your apps to access resources in or through a virtual network. Virtual network integration doesn't enable your apps to be accessed privately.

App Service has two variations:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Learn [how to enable virtual network integration](./configure-vnet-integration-enable.md).

## Regional virtual network integration

Regional virtual network integration supports connecting to a virtual network in the same region and doesn't require a gateway. Using regional virtual network integration enables your app to access:

* Resources in the virtual network you're integrated with.
* Resources in virtual networks peered to the virtual network your app is integrated with including global peering connections.
* Resources across Azure ExpressRoute connections.
* Service endpoint-secured services.
* Private endpoint-enabled services.

When you use regional virtual network integration, you can use the following Azure networking features:

* **Network security groups (NSGs)**: You can block outbound traffic with an NSG that's placed on your integration subnet. The inbound rules don't apply because you can't use virtual network integration to provide inbound access to your app.
* **Route tables (UDRs)**: You can place a route table on the integration subnet to send outbound traffic where you want.

### How regional virtual network integration works

Apps in App Service are hosted on worker roles. Regional virtual network integration works by mounting virtual interfaces to the worker roles with addresses in the delegated subnet. Because the from address is in your virtual network, it can access most things in or through your virtual network like a VM in your virtual network would. The networking implementation is different than running a VM in your virtual network. That's why some networking features aren't yet available for this feature.

:::image type="content" source="./media/overview-vnet-integration/vnetint-how-regional-works.png" alt-text="Diagram that shows how regional virtual network integration works.":::

When regional virtual network integration is enabled, your app makes outbound calls through your virtual network. The outbound addresses that are listed in the app properties portal are the addresses still used by your app. However, if your outbound call is to a virtual machine or private endpoint in the integration virtual network or peered virtual network, the outbound address will be an address from the integration subnet. The private IP assigned to an instance is exposed via the environment variable, WEBSITE_PRIVATE_IP.

When all traffic routing is enabled, all outbound traffic is sent into your virtual network. If all traffic routing isn't enabled, only private traffic (RFC1918) and service endpoints configured on the integration subnet will be sent into the virtual network and outbound traffic to the internet will go through the same channels as normal.

The feature supports only one virtual interface per worker. One virtual interface per worker means one regional virtual network integration per App Service plan. All the apps in the same App Service plan can only use the same virtual network integration to a specific subnet. If you need an app to connect to another virtual network or another subnet in the same virtual network, you need to create another App Service plan. The virtual interface used isn't a resource that customers have direct access to.

Because of the nature of how this technology operates, the traffic that's used with virtual network integration doesn't show up in Azure Network Watcher or NSG flow logs.

### Subnet requirements

Virtual network integration depends on a dedicated subnet. When you create a subnet, the Azure subnet loses five IPs from the start. One address is used from the integration subnet for each plan instance. If you scale your app to four instances, then four addresses are used.

When you scale up or down in size, the required address space is doubled for a short period of time. This change affects the real, available supported instances for a given subnet size. The following table shows both the maximum available addresses per CIDR block and the effect this has on horizontal scale.

| CIDR block size | Maximum available addresses | Maximum horizontal scale (instances)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Assumes that you'll need to scale up or down in either size or SKU at some point.

Because subnet size can't be changed after assignment, use a subnet that's large enough to accommodate whatever scale your app might reach. To avoid any issues with subnet capacity, use a `/26` with 64 addresses. When creating subnets in Azure portal as part of integrating with the virtual network, a minimum size of /27 is required.

When you want your apps in your plan to reach a virtual network that's already connected to by apps in another plan, select a different subnet than the one being used by the pre-existing virtual network integration.

You must have at least the following Role-based access control permissions on the subnet or at a higher level to configure regional virtual network integration through Azure portal, CLI or when setting the `virtualNetworkSubnetId` site property directly:

| Action | Description |
|-|-|
| Microsoft.Network/virtualNetworks/read | Read the virtual network definition |
| Microsoft.Network/virtualNetworks/subnets/read | Read a virtual network subnet definition |
| Microsoft.Network/virtualNetworks/subnets/join/action | Joins a virtual network |

If the virtual network is in a different subscription than the app, you must ensure that the subscription with the virtual network is registered for the Microsoft.Web resource provider. You can explicitly register the provider [by following this documentation](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider), but it will also automatically be registered when creating the first web app in a subscription.

### Routes

There are two types of routing to consider when you configure regional virtual network integration. Application routing defines what traffic is routed from your application and into the virtual network. Network routing is the ability to control how traffic is routed from your virtual network and out.

#### Application routing

When you configure application routing, you can either route all traffic or only private traffic (also known as [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) traffic) into your virtual network. You configure this behavior through the **Route All** setting. If **Route All** is disabled, your app only routes private traffic into your virtual network. If you want to route all your outbound traffic into your virtual network, make sure that **Route All** is enabled.

> [!NOTE]
> * When **Route All** is enabled, all traffic is subject to the NSGs and UDRs that are applied to your integration subnet. When all traffic routing is enabled, outbound traffic is still sent from the addresses that are listed in your app properties, unless you provide routes that direct the traffic elsewhere.
> * Windows containers don't support routing App Service Key Vault references or pulling custom container images over virtual network integration.
> * Regional virtual network integration can't use port 25.

Learn [how to configure application routing](./configure-vnet-integration-routing.md).

We recommend that you use the **Route All** configuration setting to enable routing of all traffic. Using the configuration setting allows you to audit the behavior with [a built-in policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33228571-70a4-4fa1-8ca1-26d0aba8d6ef). The existing WEBSITE_VNET_ROUTE_ALL app setting can still be used, and you can enable all traffic routing with either setting.

#### Network routing

You can use route tables to route outbound traffic from your app to wherever you want. Route tables affect your destination traffic. When **Route All** is disabled in [application routing](#application-routing), only private traffic (RFC1918) is affected by your route tables. Common destinations can include firewall devices or gateways. Routes that are set on your integration subnet won't affect replies to inbound app requests.

When you want to route all outbound traffic on-premises, you can use a route table to send all outbound traffic to your Azure ExpressRoute gateway. If you do route traffic to a gateway, set routes in the external network to send any replies back.

Border Gateway Protocol (BGP) routes also affect your app traffic. If you have BGP routes from something like an ExpressRoute gateway, your app outbound traffic is affected. Similar to user-defined routes, BGP routes affect traffic according to your routing scope setting.

### Network security groups

An app that uses regional virtual network integration can use a [network security group](../virtual-network/network-security-groups-overview.md) to block outbound traffic to resources in your virtual network or the internet. To block traffic to public addresses, enable [Route All](#application-routing) to the virtual network. When **Route All** isn't enabled, NSGs are only applied to RFC1918 traffic.

An NSG that's applied to your integration subnet is in effect regardless of any route tables applied to your integration subnet.

The inbound rules in an NSG don't apply to your app because virtual network integration affects only outbound traffic from your app. To control inbound traffic to your app, use the Access Restrictions feature.

### Service endpoints

Regional virtual network integration enables you to reach Azure services that are secured with service endpoints. To access a service endpoint-secured service, follow these steps:

1. Configure regional virtual network integration with your web app to connect to a specific subnet for integration.
1. Go to the destination service and configure service endpoints against the integration subnet.

### Private endpoints

If you want to make calls to [private endpoints](./networking/private-endpoint.md), make sure that your DNS lookups resolve to the private endpoint. You can enforce this behavior in one of the following ways:

* Integrate with Azure DNS private zones. When your virtual network doesn't have a custom DNS server, the integration is done automatically when the zones are linked to the virtual network.
* Manage the private endpoint in the DNS server used by your app. To manage the configuration, you must know the private endpoint IP address. Then point the endpoint you're trying to reach to that address by using an A record.
* Configure your own DNS server to forward to Azure DNS private zones.

### Azure DNS private zones

After your app integrates with your virtual network, it uses the same DNS server that your virtual network is configured with. If no custom DNS is specified, it uses Azure default DNS and any private zones linked to the virtual network.

### Limitations

There are some limitations with using regional virtual network integration:

* The feature is available from all App Service scale units in Premium v2 and Premium v3. It's also available in Standard but only from newer App Service scale units. If you're on an older scale unit, you can only use the feature from a Premium v2 App Service plan. If you want to make sure you can use the feature in a Standard App Service plan, create your app in a Premium v3 App Service plan. Those plans are only supported on our newest scale units. You can scale down if you want after the plan is created.
* The feature can't be used by Isolated plan apps that are in an App Service Environment.
* You can't reach resources across peering connections with classic virtual networks.
* The feature requires an unused subnet that's an IPv4 `/28` block or larger in an Azure Resource Manager virtual network.
* The app and the virtual network must be in the same region.
* The integration virtual network can't have IPv6 address spaces defined.
* The integration subnet can't have [service endpoint policies](../virtual-network/virtual-network-service-endpoint-policies-overview.md) enabled.
* The integration subnet can be used by only one App Service plan.
* You can't delete a virtual network with an integrated app. Remove the integration before you delete the virtual network.
* You can have only one regional virtual network integration per App Service plan. Multiple apps in the same App Service plan can use the same virtual network.
* You can't change the subscription of an app or a plan while there's an app that's using regional virtual network integration.

## Gateway-required virtual network integration

Gateway-required virtual network integration supports connecting to a virtual network in another region or to a classic virtual network. Gateway-required virtual network integration:

* Enables an app to connect to only one virtual network at a time.
* Enables up to five virtual networks to be integrated within an App Service plan.
* Allows the same virtual network to be used by multiple apps in an App Service plan without affecting the total number that can be used by an App Service plan. If you have six apps using the same virtual network in the same App Service plan, that counts as one virtual network being used.
* SLA on the gateway can impact the overall [SLA](https://azure.microsoft.com/support/legal/sla/).
* Enables your apps to use the DNS that the virtual network is configured with.
* Requires a virtual network route-based gateway configured with an SSTP point-to-site VPN before it can be connected to an app.

You can't use gateway-required virtual network integration:

* With a virtual network connected with ExpressRoute.
* From a Linux app.
* From a [Windows container](./quickstart-custom-container.md).
* To access service endpoint-secured resources.
* With a coexistence gateway that supports both ExpressRoute and point-to-site or site-to-site VPNs.

### Set up a gateway in your Azure virtual network

To create a gateway:

1. [Create the VPN gateway and subnet](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw). Select a route-based VPN type.

1. [Set the point-to-site addresses](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool). If the gateway isn't in the basic SKU, then IKEV2 must be disabled in the point-to-site configuration and SSTP must be selected. The point-to-site address space must be in the RFC 1918 address blocks 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16.

If you create the gateway for use with gateway-required virtual network integration, you don't need to upload a certificate. Creating the gateway can take 30 minutes. You won't be able to integrate your app with your virtual network until the gateway is created.

### How gateway-required virtual network integration works

Gateway-required virtual network integration is built on top of point-to-site VPN technology. Point-to-site VPNs limit network access to the virtual machine that hosts the app. Apps are restricted to send traffic out to the internet only through hybrid connections or through virtual network integration. When your app is configured with the portal to use gateway-required virtual network integration, a complex negotiation is managed on your behalf to create and assign certificates on the gateway and the application side. The result is that the workers used to host your apps can directly connect to the virtual network gateway in the selected virtual network.

:::image type="content" source="./media/overview-vnet-integration/vnetint-how-gateway-works.png" alt-text="Diagram that shows how gateway-required virtual network integration works.":::

### Access on-premises resources

Apps can access on-premises resources by integrating with virtual networks that have site-to-site connections. If you use gateway-required virtual network integration, update your on-premises VPN gateway routes with your point-to-site address blocks. When the site-to-site VPN is first set up, the scripts used to configure it should set up routes properly. If you add the point-to-site addresses after you create your site-to-site VPN, you need to update the routes manually. Details on how to do that vary per gateway and aren't described here. You can't have BGP configured with a site-to-site VPN connection.

No extra configuration is required for the regional virtual network integration feature to reach through your virtual network to on-premises resources. You simply need to connect your virtual network to on-premises resources by using ExpressRoute or a site-to-site VPN.

> [!NOTE]
> The gateway-required virtual network integration feature doesn't integrate an app with a virtual network that has an ExpressRoute gateway. Even if the ExpressRoute gateway is configured in [coexistence mode](../expressroute/expressroute-howto-coexist-resource-manager.md), the virtual network integration doesn't work. If you need to access resources through an ExpressRoute connection, use the regional virtual network integration feature or an [App Service Environment](./environment/intro.md), which runs in your virtual network.

### Peering

If you use peering with regional virtual network integration, you don't need to do any more configuration.

If you use gateway-required virtual network integration with peering, you need to configure a few more items. To configure peering to work with your app:

1. Add a peering connection on the virtual network your app connects to. When you add the peering connection, enable **Allow virtual network access** and select **Allow forwarded traffic** and **Allow gateway transit**.
1. Add a peering connection on the virtual network that's being peered to the virtual network you're connected to. When you add the peering connection on the destination virtual network, enable **Allow virtual network access** and select **Allow forwarded traffic** and **Allow remote gateways**.
1. Go to **App Service plan** > **Networking** > **VNet integration** in the portal. Select the virtual network your app connects to. Under the routing section, add the address range of the virtual network that's peered with the virtual network your app is connected to.

## Manage virtual network integration

Connecting and disconnecting with a virtual network is at an app level. Operations that can affect virtual network integration across multiple apps are at the App Service plan level. From the app > **Networking** > **VNet integration** portal, you can get details on your virtual network. You can see similar information at the App Service plan level in the **App Service plan** > **Networking** > **VNet integration** portal.

The only operation you can take in the app view of your virtual network integration instance is to disconnect your app from the virtual network it's currently connected to. To disconnect your app from a virtual network, select **Disconnect**. Your app is restarted when you disconnect from a virtual network. Disconnecting doesn't change your virtual network. The subnet or gateway isn't removed. If you then want to delete your virtual network, first disconnect your app from the virtual network and delete the resources in it, such as gateways.

The App Service plan virtual network integration UI shows you all the virtual network integrations used by the apps in your App Service plan. To see details on each virtual network, select the virtual network you're interested in. There are two actions you can perform here for gateway-required virtual network integration:

* **Sync network**: The sync network operation is used only for the gateway-required virtual network integration feature. Performing a sync network operation ensures that your certificates and network information are in sync. If you add or change the DNS of your virtual network, perform a sync network operation. This operation restarts any apps that use this virtual network. This operation won't work if you're using an app and a virtual network belonging to different subscriptions.
* **Add routes**: Adding routes drives outbound traffic into your virtual network.

The private IP assigned to the instance is exposed via the environment variable WEBSITE_PRIVATE_IP. Kudu console UI also shows the list of environment variables available to the web app. This IP is assigned from the address range of the integrated subnet. For regional virtual network integration, the value of WEBSITE_PRIVATE_IP is an IP from the address range of the delegated subnet. For gateway-required virtual network integration, the value is an IP from the address range of the point-to-site address pool configured on the virtual network gateway. This IP will be used by the web app to connect to the resources through the Azure virtual network.

> [!NOTE]
> The value of WEBSITE_PRIVATE_IP is bound to change. However, it will be an IP within the address range of the integration subnet or the point-to-site address range, so you'll need to allow access from the entire address range.
>

### Gateway-required virtual network integration routing

The routes that are defined in your virtual network are used to direct traffic into your virtual network from your app. To send more outbound traffic into the virtual network, add those address blocks here. This capability only works with gateway-required virtual network integration. Route tables don't affect your app traffic when you use gateway-required virtual network integration the way that they do with regional virtual network integration.

### Gateway-required virtual network integration certificates

When gateway-required virtual network integration is enabled, there's a required exchange of certificates to ensure the security of the connection. Along with the certificates are the DNS configuration, routes, and other similar things that describe the network.

If certificates or network information is changed, select **Sync Network**. When you select **Sync Network**, you cause a brief outage in connectivity between your app and your virtual network. Your app isn't restarted, but the loss of connectivity could cause your site to not function properly.

## Pricing details

The regional virtual network integration feature has no extra charge for use beyond the App Service plan pricing tier charges.

Three charges are related to the use of the gateway-required virtual network integration feature:

* **App Service plan pricing tier charges**: Your apps need to be in a Standard, Premium, Premium v2, or Premium v3 App Service plan. For more information on those costs, see [App Service pricing](https://azure.microsoft.com/pricing/details/app-service/).
* **Data transfer costs**: There's a charge for data egress, even if the virtual network is in the same datacenter. Those charges are described in [Data transfer pricing details](https://azure.microsoft.com/pricing/details/data-transfers/).
* **VPN gateway costs**: There's a cost to the virtual network gateway that's required for the point-to-site VPN. For more information, see [VPN gateway pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/).

## Troubleshooting

> [!NOTE]
> Virtual network integration isn't supported for Docker Compose scenarios in App Service.
> Access restrictions are ignored if a private endpoint is present.

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]
