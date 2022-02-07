---
title: VNet configuration settings | Azure API Management
description: Reference for network configuration settings when deploying Azure API Management to a virtual network
services: api-management
author: dlepow

ms.service: api-management
ms.topic: reference
ms.date: 12/16/2021
ms.author: danlep
ms.custom: references_regions
---
# Virtual network configuration reference: API Management

This reference provides detailed network configuration settings for an API Management instance deployed in an Azure virtual network in the [external](api-management-using-with-vnet.md) or [internal](api-management-using-with-internal-vnet.md) mode.

For VNet connectivity options, requirements, and considerations, see [Using a virtual network with Azure API Management](virtual-network-concepts.md).

## Required ports  

Control inbound and outbound traffic into the subnet in which API Management is deployed by using [network security group][NetworkSecurityGroups] rules. If certain ports are unavailable, API Management may not operate properly and may become inaccessible. 

When an API Management service instance is hosted in a VNet, the ports in the following table are used. Some requirements differ depending on the version (`stv2` or `stv1`) of the [compute platform](compute-infrastructure.md) hosting your API Management instance.

>[!IMPORTANT]
> * **Bold** items in the *Purpose* column indicate port configurations required for successful deployment and operation of the API Management service. Configurations labeled "optional" enable specific features, as noted. They are not required for the overall health of the service. 
>
> * We recommend using [service tags](../virtual-network/service-tags-overview.md) instead of IP addresses in NSG rules to specify network sources and destinations. Service tags prevent downtime when infrastructure improvements necessitate IP address changes.      


### [stv2](#tab/stv2)

| Source / Destination Port(s) | Direction          | Transport protocol |   Service tags <br> Source / Destination   | Purpose                                            | VNet type |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Inbound            | TCP                | Internet / VirtualNetwork           | **Client communication to API Management**                     | External only            |
| * / 3443                     | Inbound            | TCP                | ApiManagement / VirtualNetwork       | **Management endpoint for Azure portal and PowerShell**         | External & Internal  |
| * / 443                  | Outbound           | TCP                | VirtualNetwork / Storage             | **Dependency on Azure Storage**                             | External & Internal  |
| * / 443                  | Outbound           | TCP                | VirtualNetwork / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) and Azure Key Vault dependency (optional)              | External & Internal  |
| * / 1433                     | Outbound           | TCP                | VirtualNetwork / SQL                 | **Access to Azure SQL endpoints**                           | External & Internal  |
| * / 443                     | Outbound           | TCP                | VirtualNetwork / AzureKeyVault                | **Access to Azure Key Vault**                         | External & Internal  |
| * / 5671, 5672, 443          | Outbound           | TCP                | VirtualNetwork / Azure Event Hubs            | Dependency for [Log to Azure Event Hubs policy](api-management-howto-log-event-hubs.md) and monitoring agent (optional) | External & Internal  |
| * / 445                      | Outbound           | TCP                | VirtualNetwork / Storage             | Dependency on Azure File Share for [GIT](api-management-configuration-repository-git.md) (optional)                   | External & Internal  |
| * / 443, 12000                     | Outbound           | TCP                | VirtualNetwork / AzureCloud            | Health and Monitoring Extension (optional)        | External & Internal  |
| * / 1886, 443                     | Outbound           | TCP                | VirtualNetwork / AzureMonitor         | Publish [Diagnostics Logs and Metrics](api-management-howto-use-azure-monitor.md), [Resource Health](../service-health/resource-health-overview.md), and [Application Insights](api-management-howto-app-insights.md) (optional)                  | External & Internal  |
| * / 25, 587, 25028                       | Outbound           | TCP                | VirtualNetwork / Internet            | Connect to SMTP Relay for sending e-mail (optional)                   | External & Internal  |
| * / 6381 - 6383              | Inbound & Outbound | TCP                | VirtualNetwork / VirtualNetwork     | Access Redis Service for [Cache](api-management-caching-policies.md) policies between machines (optional)        | External & Internal  |
| * / 4290              | Inbound & Outbound | UDP                | VirtualNetwork / VirtualNetwork     | Sync Counters for [Rate Limit](api-management-access-restriction-policies.md#LimitCallRateByKey) policies between machines (optional)        | External & Internal  |
| * / 6390                       | Inbound            | TCP                | AzureLoadBalancer / VirtualNetwork | **Azure Infrastructure Load Balancer**                          | External & Internal  |

### [stv1](#tab/stv1)

| Source / Destination Port(s) | Direction          | Transport protocol |   [Service Tags](../virtual-network/network-security-groups-overview.md#service-tags) <br> Source / Destination   | Purpose                                                  | VNet type |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Inbound            | TCP                | Internet / VirtualNetwork            | **Client communication to API Management**                     | External only          |
| * / 3443                     | Inbound            | TCP                | ApiManagement / VirtualNetwork       | **Management endpoint for Azure portal and PowerShell**       | External & Internal  |
| * / 443                  | Outbound           | TCP                | VirtualNetwork / Storage             | **Dependency on Azure Storage**                             | External & Internal  |
| * / 443                  | Outbound           | TCP                | VirtualNetwork / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) dependency  (optional)                | External & Internal  |
| * / 1433                     | Outbound           | TCP                | VirtualNetwork / SQL                 | **Access to Azure SQL endpoints**                           | External & Internal  |
| * / 5671, 5672, 443          | Outbound           | TCP                | VirtualNetwork / Azure Event Hubs            | Dependency for [Log to Azure Event Hubs policy](api-management-howto-log-event-hubs.md) and monitoring agent (optional)| External & Internal  |
| * / 445                      | Outbound           | TCP                | VirtualNetwork / Storage             | Dependency on Azure File Share for [GIT](api-management-configuration-repository-git.md) (optional)                     | External & Internal  |
| * / 443, 12000                     | Outbound           | TCP                | VirtualNetwork / AzureCloud            | Health and Monitoring Extension & Dependency on Event Grid (if events notification activated) (optional)       | External & Internal  |
| * / 1886, 443                     | Outbound           | TCP                | VirtualNetwork / AzureMonitor         | Publish [Diagnostics Logs and Metrics](api-management-howto-use-azure-monitor.md), [Resource Health](../service-health/resource-health-overview.md), and [Application Insights](api-management-howto-app-insights.md) (optional)                  | External & Internal  |
| * / 25, 587, 25028                       | Outbound           | TCP                | VirtualNetwork / Internet            | Connect to SMTP Relay for sending e-mail (optional)                    | External & Internal  |
| * / 6381 - 6383              | Inbound & Outbound | TCP                | VirtualNetwork / VirtualNetwork     | Access Redis Service for [Cache](api-management-caching-policies.md) policies between machines (optional)        | External & Internal  |
| * / 4290              | Inbound & Outbound | UDP                | VirtualNetwork / VirtualNetwork     | Sync Counters for [Rate Limit](api-management-access-restriction-policies.md#LimitCallRateByKey) policies between machines (optional)        | External & Internal  |
| * / *                         | Inbound            | TCP                | AzureLoadBalancer / VirtualNetwork | **Azure Infrastructure Load Balancer** (required for Premium SKU, optional for other SKUs)                        | External & Internal  |

---

## Regional service tags

NSG rules allowing outbound connectivity to Storage, SQL, and Azure Event Hubs service tags may use the regional versions of those tags corresponding to the region containing the API Management instance (for example, **Storage.WestUS** for an API Management instance in the West US region). In multi-region deployments, the NSG in each region should allow traffic to the service tags for that region and the primary region.

## TLS functionality  
  To enable TLS/SSL certificate chain building and validation, the API Management service needs outbound network connectivity to `ocsp.msocsp.com`, `mscrl.microsoft.com`, and `crl.microsoft.com`. This dependency is not required if any certificate you upload to API Management contains the full chain to the CA root.

## DNS access
  Outbound access on port `53` is required for communication with DNS servers. If a custom DNS server exists on the other end of a VPN gateway, the DNS server must be reachable from the subnet hosting API Management.

## Metrics and health monitoring 

Outbound network connectivity to Azure Monitoring endpoints, which resolve under the following domains, are represented under the **AzureMonitor** service tag for use with Network Security Groups.

### Metrics and health monitoring 

Outbound network connectivity to Azure Monitoring endpoints, which resolve under the following domains, are represented under the AzureMonitor service tag for use with Network Security Groups.

|     Azure Environment | Endpoints                                                                                                                                                                                                                                                                                                                                           |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
| Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
| Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                        

## SMTP relay  
  
Allow outbound network connectivity for the SMTP relay, which resolves under the host `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com`, and `ies.global.microsoft.com`

> [!NOTE]
> Only the SMTP relay provided in API Management may be used to send email from your instance.

## Developer portal CAPTCHA 
Allow outbound network connectivity for the developer portal's CAPTCHA, which resolves under the hosts `client.hip.live.com` and `partner.hip.live.com`.

## Publishing the developer portal

Enable publishing the [developer portal](api-management-howto-developer-portal.md) for an API Management instance in a VNet by allowing outbound connectivity to blob storage in the West US region. For example, use the **Storage.WestUS** service tag in an NSG rule. Currently, connectivity to blob storage in the West US region is required to publish the developer portal for any API Management instance.

## Azure portal diagnostics  
  When using the API Management diagnostics extension from inside a VNet, outbound access to `dc.services.visualstudio.com` on port `443` is required to enable the flow of diagnostic logs from Azure portal. This access helps in troubleshooting issues you might face when using the extension.

## Azure load balancer  
  You're not required to allow inbound requests from service tag `AzureLoadBalancer` for the Developer SKU, since only one compute unit is deployed behind it. However, inbound connectivity from `AzureLoadBalancer` becomes **critical** when scaling to a higher SKU, such as Premium, because failure of the health probe from load balancer then blocks all inbound access to the control plane and data plane.

## Application Insights  
  If you enabled [Azure Application Insights](api-management-howto-app-insights.md) monitoring on API Management, allow outbound connectivity to the [telemetry endpoint](../azure-monitor/app/ip-addresses.md#outgoing-ports) from the VNet.

## KMS endpoint

When adding virtual machines running Windows to the VNet, allow outbound connectivity on port `1688` to the [KMS endpoint](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation#solution) in your cloud. This configuration routes Windows VM traffic to the Azure Key Management Services (KMS) server to complete Windows activation.

## Force tunneling traffic to on-premises firewall Using ExpressRoute or Network Virtual Appliance  
  Commonly, you configure and define your own default route (0.0.0.0/0), forcing all traffic from the API Management subnet to flow through an on-premises firewall or to a network virtual appliance. This traffic flow breaks connectivity with Azure API Management, since outbound traffic is either blocked on-premises, or NAT'd to an unrecognizable set of addresses no longer working with various Azure endpoints. You can solve this issue via one of the following methods: 

  * Enable [service endpoints][ServiceEndpoints] on the subnet in which the API Management service is deployed for:
      * Azure SQL (required only in the primary region if the API Management service is deployed to [multiple regions](api-management-howto-deploy-multi-region.md))
      * Azure Storage
      * Azure Event Hubs
      * Azure Key Vault (required when API Management is deployed on the v2 platform) 
  
     By enabling endpoints directly from the API Management subnet to these services, you can use the Microsoft Azure backbone network, providing optimal routing for service traffic. If you use service endpoints with a force tunneled API Management, the above Azure services traffic isn't force tunneled. The other API Management service dependency traffic is force tunneled and can't be lost. If lost, the API Management service would not function properly.

  * All the control plane traffic from the internet to the management endpoint of your API Management service is routed through a specific set of inbound IPs, hosted by API Management. When the traffic is force tunneled, the responses will not symmetrically map back to these inbound source IPs. To overcome the limitation, set the destination of the following user-defined routes ([UDRs][UDRs]) to the "Internet", to steer traffic back to Azure. Find the set of inbound IPs for control plane traffic documented in [Control plane IP addresses](#control-plane-ip-addresses).

  * For other force tunneled API Management service dependencies, resolve the hostname and reach out to the endpoint. These include:
      - Metrics and Health Monitoring
      - Azure portal diagnostics
      - SMTP relay
      - Developer portal CAPTCHA
      - Azure KMS server

## Control plane IP addresses

The following IP addresses are divided by **Azure Environment**. When allowing inbound requests, IP addresses marked with **Global** must be permitted, along with the **Region**-specific IP address. In some cases, two IP addresses are listed. Permit both IP addresses.

> [!IMPORTANT]
> Control plane IP addresses should be configured for network access rules only when needed in certain networking scenarios. We recommend using the **ApiManagement** [service tag](../virtual-network/service-tags-overview.md) instead of control plane IP addresses to prevent downtime when infrastructure improvements necessitate IP address changes.   

| **Azure Environment**|   **Region**|  **IP address**|
|-----------------|-------------------------|---------------|
| Azure Public| South Central US (Global)| 104.214.19.224|
| Azure Public| North Central US (Global)| 52.162.110.80|
| Azure Public| Australia Central| 20.37.52.67|
| Azure Public| Australia Central 2| 20.39.99.81|
| Azure Public| Australia East| 20.40.125.155|
| Azure Public| Australia Southeast| 20.40.160.107|
| Azure Public| Brazil South| 191.233.24.179, 191.238.73.14|
| Azure Public| Brazil Southeast| 191.232.18.181|
| Azure Public| Canada Central| 52.139.20.34, 20.48.201.76|
| Azure Public| Canada East| 52.139.80.117|
| Azure Public| Central India| 13.71.49.1, 20.192.45.112|
| Azure Public| Central US| 13.86.102.66|
| Azure Public| Central US EUAP| 52.253.159.160|
| Azure Public| East Asia| 52.139.152.27|
| Azure Public| East US| 52.224.186.99|
| Azure Public| East US 2| 20.44.72.3|
| Azure Public| East US 2 EUAP| 52.253.229.253|
| Azure Public| France Central| 40.66.60.111|
| Azure Public| France South| 20.39.80.2|
| Azure Public| Germany North| 51.116.0.0|
| Azure Public| Germany West Central| 51.116.96.0, 20.52.94.112|
| Azure Public| Japan East| 52.140.238.179|
| Azure Public| Japan West| 40.81.185.8|
| Azure Public| India Central| 20.192.234.160|
| Azure Public| India West| 20.193.202.160|
| Azure Public| Korea Central| 40.82.157.167, 20.194.74.240|
| Azure Public| Korea South| 40.80.232.185|
| Azure Public| North Central US| 40.81.47.216|
| Azure Public| North Europe| 52.142.95.35|
| Azure Public| Norway East| 51.120.2.185|
| Azure Public| Norway West| 51.120.130.134|
| Azure Public| South Africa North| 102.133.130.197, 102.37.166.220|
| Azure Public| South Africa West| 102.133.0.79|
| Azure Public| South Central US| 20.188.77.119, 20.97.32.190|
| Azure Public| South India| 20.44.33.246|
| Azure Public| Southeast Asia| 40.90.185.46|
| Azure Public| Switzerland North| 51.107.0.91|
| Azure Public| Switzerland West| 51.107.96.8|
| Azure Public| UAE Central| 20.37.81.41|
| Azure Public| UAE North| 20.46.144.85|
| Azure Public| UK South| 51.145.56.125|
| Azure Public| UK West| 51.137.136.0|
| Azure Public| West Central US| 52.253.135.58|
| Azure Public| West Europe| 51.145.179.78|
| Azure Public| West India| 40.81.89.24|
| Azure Public| West US| 13.64.39.16|
| Azure Public| West US 2| 51.143.127.203|
| Azure Public| West US 3| 20.150.167.160|
| Azure China 21Vianet| China North (Global)| 139.217.51.16|
| Azure China 21Vianet| China East (Global)| 139.217.171.176|
| Azure China 21Vianet| China North| 40.125.137.220|
| Azure China 21Vianet| China East| 40.126.120.30|
| Azure China 21Vianet| China North 2| 40.73.41.178|
| Azure China 21Vianet| China East 2| 40.73.104.4|
| Azure Government| USGov Virginia (Global)| 52.127.42.160|
| Azure Government| USGov Texas (Global)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD Central| 52.182.32.132|
| Azure Government| USDoD East| 52.181.32.192|


## Next steps

Learn more about:

* [Connecting a virtual network to backend using VPN Gateway](../vpn-gateway/design.md#s2smulti)
* [Connecting a virtual network from different deployment models](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Debug your APIs using request tracing](api-management-howto-api-inspector.md)
* [Virtual Network frequently asked questions](../virtual-network/virtual-networks-faq.md)
* [Service tags](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[NetworkSecurityGroups]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
