---
 title: include file
 description: include file
 services: vpn-gateway
 author: cherylmc
 ms.service: vpn-gateway
 ms.topic: include
 ms.date: 02/02/2022
 ms.author: cherylmc
 ms.custom: include file
---

1. In **Search resources, services, and docs (G+/)** type **virtual network gateway**. Locate **Virtual network gateway** in the Marketplace search results and select it to open the **Create virtual network gateway** page.

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/search.png" alt-text="Screenshot of Search field." lightbox="./media/vpn-gateway-add-gw-portal/search-expand.png":::

1. On the **Basics** tab, fill in the values for **Project details** and **Instance details**.

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/instance-details.png" alt-text="Screenshot of Instance fields." lightbox="./media/vpn-gateway-add-gw-portal/instance-details.png":::

   * **Subscription**: Select the subscription you want to use from the dropdown.
   * **Resource Group**: This setting is autofilled when you select your virtual network on this page.
   * **Name**: Name your gateway. Naming your gateway not the same as naming a gateway subnet. It's the name of the gateway object you're creating.
   * **Region**: Select the region in which you want to create this resource. The region for the gateway must be the same as the virtual network.
   * **Gateway type**: Select **VPN**. VPN gateways use the virtual network gateway type **VPN**.
   * **VPN type**: Select the VPN type that is specified for your configuration. Most configurations require a Route-based VPN type.
   * **SKU**: Select the gateway SKU  you want to use from the dropdown. The SKUs listed in the dropdown depend on the VPN type you select. Make sure to select a SKU that supports the features you want to use. For more information about gateway SKUs, see [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Generation**: Select the generation you want to use. For more information, see [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   * **Virtual network**: From the dropdown, select the virtual network to which you want to add this gateway. If you can't see the VNet for which you want to create a gateway, make sure you selected the correct subscription and region in the previous settings.
   * **Gateway subnet address range**: This field only appears if your VNet doesn't have a gateway subnet. It's best to specify /27 or larger (/26,/25 etc.). This allows enough IP addresses for future changes, such as adding an ExpressRoute gateway. We don't recommend creating a range any smaller than /28. If you already have a gateway subnet, you can view GatewaySubnet details by navigating to your virtual network. Select **Subnets** to view the range. If you want to change the range, you can delete and recreate the GatewaySubnet.