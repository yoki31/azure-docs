---
title: Azure DDoS Protection reference architectures
description: Learn Azure DDoS protection reference architectures.
services: ddos-protection
author: aletheatoh
ms.service: ddos-protection
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/19/2022
ms.author: yitoh
ms.custom: fasttrack-edit
---

# DDoS Protection reference architectures

DDoS Protection Standard is designed [for services that are deployed in a virtual network](../virtual-network/virtual-network-for-azure-services.md). The following reference architectures are arranged by scenarios, with architecture patterns grouped together.

> [!NOTE]
> Protected resources include public IPs attached to an IaaS VM, Load Balancer (Classic & Standard Load Balancers), Application Gateway (including WAF) cluster, Firewall, Bastion, VPN Gateway, Service Fabric or an IaaS based Network Virtual Appliance (NVA). PaaS services (multitenant) are not supported at present. This includes Azure App Service Environment for Power Apps or API management in a virtual network with a public IP.

## Virtual machine (Windows/Linux) workloads

### Application running on load-balanced VMs

This reference architecture shows a set of proven practices for running multiple Windows VMs in a scale set behind a load balancer, to improve availability and scalability. This architecture can be used for any stateless workload, such as a web server.

![Diagram of the reference architecture for an application running on load-balanced VMs](./media/ddos-best-practices/image-9.png)

In this architecture, a workload is distributed across multiple VM instances. There is a single public IP address, and internet traffic is distributed to the VMs through a load balancer. DDoS Protection Standard is enabled on the virtual network of the Azure (internet) load balancer that has the public IP associated with it.

The load balancer distributes incoming internet requests to the VM instances. Virtual machine scale sets allow the number of VMs to be scaled in or out manually, or automatically based on predefined rules. This is important if the resource is under DDoS attack. For more information on this reference architecture, see
[this article](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

### Application running on Windows N-tier

There are many ways to implement an N-tier architecture. The following diagram shows a typical three-tier web application. This architecture builds on the article [Run load-balanced VMs for scalability and availability](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). The web and business tiers use load-balanced VMs.

![Diagram of the reference architecture for an application running on Windows N-tier](./media/ddos-best-practices/image-10.png)

In this architecture, DDoS Protection Standard is enabled on the virtual network. All public IPs in the virtual network get DDoS protection for Layer 3 and 4. For Layer 7 protection, deploy Application Gateway in the WAF SKU. For more information on this reference architecture, see 
[this article](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

> [!NOTE]
> Scenarios in which a single VM is running behind a public IP are not supported.

### PaaS web application

This reference architecture shows running an Azure App Service application in a single region. This architecture shows a set of proven practices for a web application that uses [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) and [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
A standby region is set up for failover scenarios.

![Diagram of the reference architecture for a PaaS web application](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager routes incoming requests to Application Gateway in one of the regions. During normal operations, it routes requests to Application Gateway in the active region. If that region becomes unavailable, Traffic Manager fails over to Application Gateway in the standby region.

All traffic from the internet destined to the web application is routed to the [Application Gateway public IP address](../application-gateway/application-gateway-web-app-overview.md) via Traffic Manager. In this scenario, the app service (web app) itself is not directly externally facing and is protected by Application Gateway. 

We recommend that you configure the Application Gateway WAF SKU (prevent mode) to help protect against Layer 7 (HTTP/HTTPS/WebSocket) attacks. Additionally, web apps are configured to [accept only traffic from the Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP address.

For more information about this reference architecture, see [this article](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## Mitigation for non-web PaaS services

### HDInsight on Azure

This reference architecture shows configuring DDoS Protection Standard for an [Azure HDInsight cluster](../hdinsight/index.yml). Make sure that the HDInsight cluster is linked to a virtual network and that DDoS Protection is enabled on the virtual network.

!["HDInsight" and "Advanced settings" panes, with virtual network settings](./media/ddos-best-practices/image-12.png)

![Selection for enabling DDoS Protection](./media/ddos-best-practices/image-13.png)

In this architecture, traffic destined to the HDInsight cluster from the internet is routed to the public IP associated with the HDInsight gateway load balancer. The gateway load balancer then sends the traffic to the head nodes or the worker nodes directly. Because DDoS Protection Standard is enabled on the HDInsight virtual network, all public IPs in the virtual network get DDoS protection for Layer 3 and 4. This reference architecture can be combined with the N-Tier and multi-region reference architectures.

For more information on this reference architecture, see the [Extend Azure HDInsight using an Azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
documentation.


> [!NOTE]
> Azure App Service Environment for Power Apps or API management in a virtual network with a public IP are both not natively supported.

## Hub-and-spoke network topology with Azure Firewall and Azure Bastion

This reference architecture details a hub-and-spoke topology with Azure Firewall inside the hub as a DMZ for scenarios that require central control over security aspects. Azure Firewall is a managed firewall as a service and is placed in its own subnet. Azure Bastion is deployed and placed in its own subnet.

There are two spokes that are connected to the hub using VNet peering and there is no spoke-to-spoke connectivity. If you require spoke-to-spoke connectivity, then you need to create routes to forward traffic from one spoke to the firewall, which can then route it to the other spoke.

:::image type="content" source="./media/ddos-best-practices/image-14.png" alt-text="Screenshot showing Hub-and-spoke architecture with firewall, bastion, and DDoS Protection Standard" lightbox="./media/ddos-best-practices/image-14.png":::

Azure DDoS Protection Standard is enabled on the hub virtual network. Therefore, all the Public IPs that are inside the hub are protected by the DDoS Standard plan. In this scenario, the firewall in the hub helps control the ingress traffic from the internet, while the firewall's public IP is being protected. Azure DDoS Protection Standard also protects the public IP of the bastion.

DDoS Protection Standard is designed for services that are deployed in a virtual network. For more information, see [Deploy dedicated Azure service into virtual networks](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

> [!NOTE]
> DDoS Protection Standard protects the Public IPs of Azure resource. DDoS Protection Basic, which requires no configuration and is enabled by default, only protects the Azure underlying platform infrastructure (e.g. Azure DNS). For more information, see [Azure DDoS Protection Standard overview](ddos-protection-overview.md).
For more information about hub-and-spoke topology, see [Hub-spoke network topology](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?tabs=cli).

## Next steps

- Learn how to [create a DDoS protection plan](manage-ddos-protection.md).
