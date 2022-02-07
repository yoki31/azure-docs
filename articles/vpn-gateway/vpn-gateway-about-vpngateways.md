---
title: 'About Azure VPN Gateway'
description: Learn what VPN Gateway is, and how to use a VPN gateway to connect to IPsec IKE Site-to-Site, VNet-to-VNet, and Point-to-Site VPN virtual networks.
services: vpn-gateway
author: cherylmc
# Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 01/12/2022
ms.author: cherylmc
ms.custom: contperf-fy21q1, e2e-hybrid
---

# What is VPN Gateway?

A VPN gateway is a specific type of virtual network gateway that is used to send encrypted traffic between an Azure virtual network and an on-premises location over the public Internet. You can also use a VPN gateway to send encrypted traffic between Azure virtual networks over the Microsoft network. Each virtual network can have only one VPN gateway. However, you can create multiple connections to the same VPN gateway. When you create multiple connections to the same VPN gateway, all VPN tunnels share the available gateway bandwidth.

## <a name="whatis"></a>What is a virtual network gateway?

A virtual network gateway is composed of two or more VMs that are automatically configured and deployed to a specific subnet you create called the *gateway subnet*. The gateway VMs contain routing tables and run specific gateway services. You can't directly configure the VMs that are part of the virtual network gateway, although the settings that you select when configuring your gateway impact the gateway VMs that are created.

### <a name="vpn"></a>What is a VPN gateway?

When you configure a virtual network gateway, you configure a setting that specifies the gateway type. The gateway type determines how the virtual network gateway will be used and the actions that the gateway takes. The gateway type 'Vpn' specifies that the type of virtual network gateway created is a 'VPN gateway'. This distinguishes it from an ExpressRoute gateway, which uses a different gateway type. A virtual network can have two virtual network gateways; one VPN gateway and one ExpressRoute gateway. For more information, see [Gateway types](vpn-gateway-about-vpn-gateway-settings.md#gwtype).

When you create a VPN gateway, gateway VMs are deployed to the gateway subnet and configured with the settings that you specified. This process can take 45 minutes or more to complete, depending on the gateway SKU that you selected. After you create a VPN gateway, you can create an IPsec/IKE VPN tunnel connection between that VPN gateway and another VPN gateway (VNet-to-VNet), or create a cross-premises IPsec/IKE VPN tunnel connection between the VPN gateway and an on-premises VPN device (Site-to-Site). You can also create a Point-to-Site VPN connection (VPN over OpenVPN, IKEv2, or SSTP), which lets you connect to your virtual network from a remote location, such as from a conference or from home.

## <a name="configuring"></a>Configuring a VPN Gateway

A VPN gateway connection relies on multiple resources that are configured with specific settings. Most of the resources can be configured separately, although some resources must be configured in a certain order.

### <a name="connectivity"></a> Connectivity

Because you can create multiple connection configurations using VPN Gateway, you need to determine which configuration best fits your needs. Point-to-Site, Site-to-Site, and coexisting ExpressRoute/Site-to-Site connections all have different instructions and configuration requirements. For connection diagrams and corresponding links to configuration steps, see [VPN Gateway design](design.md).

* [Site-to-Site VPN connections](design.md#s2smulti)
* [Point-to-Site VPN connections](design.md#P2S)
* [VNet-to-VNet VPN connections](design.md#V2V)

### <a name="planningtable"></a>Planning table

The following table can help you decide the best connectivity option for your solution. Note that ExpressRoute is not a part of VPN Gateway, but is included in the table.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a>Settings

The settings that you chose for each resource are critical to creating a successful connection. For information about individual resources and settings for VPN Gateway, see [About VPN Gateway settings](vpn-gateway-about-vpn-gateway-settings.md). The article contains information to help you understand gateway types, gateway SKUs, VPN types, connection types, gateway subnets, local network gateways, and various other resource settings that you may want to consider.

### <a name="tools"></a>Deployment tools

You can start out creating and configuring resources using one configuration tool, such as the Azure portal. You can later decide to switch to another tool, such as PowerShell, to configure additional resources, or modify existing resources when applicable. Currently, you can't configure every resource and resource setting in the Azure portal. The instructions in the articles for each connection topology specify when a specific configuration tool is needed.

## <a name="gwsku"></a>Gateway SKUs

When you create a virtual network gateway, you specify the gateway SKU that you want to use. Select the SKU that satisfies your requirements based on the types of workloads, throughputs, features, and SLAs.

* For more information about gateway SKUs, including supported features, production and dev-test, and configuration steps, see the [VPN Gateway Settings - Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) article.
* For Legacy SKU information, see [Working with Legacy SKUs](vpn-gateway-about-skus-legacy.md).

### <a name="benchmark"></a>Gateway SKUs by tunnel, connection, and throughput

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability"></a>Availability Zones

VPN gateways can be deployed in Azure Availability Zones. This brings resiliency, scalability, and higher availability to virtual network gateways. Deploying gateways in Azure Availability Zones physically and logically separates gateways within a region, while protecting your on-premises network connectivity to Azure from zone-level failures. See [About zone-redundant virtual network gateways in Azure Availability Zones](about-zone-redundant-vnet-gateways.md).

## <a name="pricing"></a>Pricing

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

For more information about gateway SKUs for VPN Gateway, see [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a>FAQ

For frequently asked questions about VPN gateway, see the [VPN Gateway FAQ](vpn-gateway-vpn-faq.md).

## <a name="new"></a>What's new?

Subscribe to the RSS feed and view the latest VPN Gateway feature updates on the [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway) page.

## Next steps

- [Tutorial: Create and manage a VPN Gateway](tutorial-create-gateway-portal.md).
- [Learn module: Connect your on-premises network to Azure with VPN Gateway](/learn/modules/connect-on-premises-network-with-vpn-gateway/).
- [Subscription and service limits](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
