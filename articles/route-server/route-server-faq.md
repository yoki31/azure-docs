---
title: Frequently asked questions about Azure Route Server
description: Find answers to frequently asked questions about Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 01/26/2022
ms.author: duau
---

# Azure Route Server FAQ

## What is Azure Route Server?

Azure Route Server is a fully managed service that allows you to easily manage routing between your network virtual appliance (NVA) and your virtual network.

### Is Azure Route Server just a VM?

No. Azure Route Server is a service designed with high availability. If it's deployed in an Azure region that supports [Availability Zones](../availability-zones/az-overview.md), it will have zone-level redundancy.

### How many route servers can I create in a virtual network?

You can create only one route server in a virtual network. It must be deployed in a dedicated subnet called *RouteServerSubnet*.

### Does Azure Route Server support virtual network peering?

Yes, if you peer a virtual network hosting the Azure Route Server to another virtual network and you enable Use Remote Gateway on the second virtual network, Azure Route Server will learn the address spaces of that virtual network and send them to all the peered NVAs. It will also program the routes from the NVAs into the routing table of the VMs in the peered virtual network. 


### <a name = "protocol"></a>What routing protocols does Azure Route Server support?

Azure Route Server supports Border Gateway Protocol (BGP) only. Your NVA needs to support multi-hop external BGP because you’ll need to deploy Azure Route Server in a dedicated subnet in your virtual network. The [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) you choose must be different from the one Azure Route Server uses when you configure the BGP on your NVA.

### Does Azure Route Server route data traffic between my NVA and my VMs?

No. Azure Route Server only exchanges BGP routes with your NVA. The data traffic goes directly from the NVA to the destination VM and directly from the VM to the NVA.

### Does Azure Route Server store customer data?
No. Azure Route Server only exchanges BGP routes with your NVA and then propagates them to your virtual network.

### Why does Azure Route Server require a public IP address?

Azure Router Server needs to ensure connectivity to the backend service that manages the Route Server configuration, as such a public IP address is required. This public IP address doesn't constitute a security exposure of your virtual network.

### Does Azure Route Server support IPv6?

No. We'll add IPv6 support in the future. 

### If Azure Route Server receives the same route from more than one NVA, how does it handle them?

If the route has the same AS path length, Azure Route Server will program multiple copies of the route, each with a different next hop, to the VMs in the virtual network. When the VMs send traffic to the destination of this route, the VM hosts will do Equal-Cost Multi-Path (ECMP) routing. However, if one NVA sends the route with a shorter AS path length than other NVAs, Azure Route Server will only program the route that has the next hop set to this NVA to the VMs in the virtual network.

### Does Azure Route Server preserve the BGP AS Path of the route it receives?

Yes, Azure Route Server propagates the route with the BGP AS Path intact.

### Does Azure Route Server preserve the BGP communities of the route it receives?

Yes, Azure Route Server propagates the route with the BGP communities as is.

### What is the BGP timer setting of Azure Route Server?
The Keep-alive timer is set to 60 seconds and the Hold-down timer 180 seconds.

### What Autonomous System Numbers (ASNs) can I use?

You can use your own public ASNs or private ASNs in your network virtual appliance. You can't use the ranges reserved by Azure or IANA.
The following ASNs are reserved by Azure or IANA:

* ASNs reserved by Azure:
    * Public ASNs: 8074, 8075, 12076
    * Private ASNs: 65515, 65517, 65518, 65519, 65520
* ASNs [reserved by IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### Can I use 32-bit (4 byte) ASNs?

No, Azure Route Server supports only 16-bit (2 bytes) ASNs.

### Can I associate a User Defined Route (UDR) to the RouteServerSubnet?

No, Azure Route Server doesn't support configuring a UDR on the RouteServerSubnet.

### Can I associate a Network Security group (NSG) to the RouteServerSubnet?

No, Azure Route Server doesn't support NSG association to the RouteServerSubnet.

### Can I peer two route servers in two peered virtual networks and enable the NVAs connected to the route servers to talk to each other? 

***Topology: NVA1 -> RouteServer1 -> (via VNet Peering) -> RouteServer2 -> NVA2***

No, Azure Route Server doesn't forward data traffic. To enable transit connectivity through the NVA, set up a direct connection (for example, an IPsec tunnel) between the NVAs and use the route servers for dynamic route propagation. 

### Can I use Azure Route Server to direct traffic between subnets in the same virtual network to flow inter-subnet traffic through the NVA?

No. System routes for traffic related to virtual network, virtual network peerings, or virtual network service endpoints, are preferred routes, even if BGP routes are more specific. As Route Server uses BGP to advertise routes, currently this is not supported by design. You must continue to use UDRs to force override the routes, and you can't utilize BGP to quickly failover these routes. You must continue to use a third party solution to update the UDRs via the API in a failover situation, or use an Azure Load Balancer with HA ports mode to direct traffic.

You can still use Route Server to direct traffic between subnets in different virtual networks to flow using the NVA. The only possible design that may work is one subnet per "spoke" virtual network and all virtual networks are peered to a "hub" virtual network, but this is very limiting and needs to take into scaling considerations and Azure's maximum limits on virtual networks vs subnets.

## <a name = "limitations"></a>Route Server Limits

Azure Route Server has the following limits (per deployment).

| Resource | Limit |
|----------|-------|
| Number of BGP peers supported | 8 |
| Number of routes each BGP peer can advertise to Azure Route Server | 1000 |
| Number of routes that Azure Route Server can advertise to ExpressRoute or VPN gateway | 200 |
| Number of VMs in the virtual network (including peered virtual networks) that Azure Route Server can support | 2000 |

The number of VMs that Azure Route Server can support is not a hard limit. This depends on how the Route Server infrastructure is deployed within an Azure Region.

If your NVA advertises more routes than the limit, the BGP session will get dropped. In the event BGP session is dropped between the gateway and Azure Route Server, you'll lose connectivity from your on-premises network to Azure. For more information, see [Diagnose an Azure virtual machine routing problem](../virtual-network/diagnose-network-routing-problem.md).


## Next steps

Learn how to [configure Azure Route Server](quickstart-configure-route-server-powershell.md).
