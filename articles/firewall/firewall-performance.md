---
title: Azure Firewall performance 
description: Compare Azure Firewall performance for Azure Firewall Standard and Premium
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/24/2022
ms.author: victorh
---

# Azure Firewall performance

Reliable firewall performance is essential to operate and protect your virtual networks in Azure. More advanced features (like those found in Azure Firewall Premium) require more processing complexity. This will affect firewall performance and impact the overall network performance.

Azure Firewall has two versions: Standard and Premium.

- Azure Firewall Standard

   Azure Firewall Standard has been generally available since September 2018. It is cloud native, highly available, with built-in auto scaling firewall-as-a-service. You can centrally govern and log all your traffic flows using a DevOps approach. The service supports both application and network level-filtering rules, and is integrated with the Microsoft Threat Intelligence feed for filtering known malicious IP addresses and domains. 
- Azure Firewall Premium

   Azure Firewall Premium is a next generation firewall. It has capabilities that are required for highly sensitive and regulated environments. The features that might affect the performance of the Firewall are TLS (Transport Layer Security) inspection and IDPS (Intrusion Detection and Prevention).

For more information about Azure Firewall, see [What is Azure Firewall?](overview.md)

## Performance testing

Before deploying Azure Firewall, the performance needs to be tested and evaluated to ensure it meets your expectations. Not only should Azure Firewall handle the current traffic on a network, but it should also be ready for potential traffic growth. It is recommended to evaluate on a test network and not in a production environment. The testing should attempt to replicate the production environment as close as possible. This includes the network topology, and emulating the actual characteristics of the expected traffic through the firewall.

## Performance data

The following set of performance results demonstrates the maximal Azure Firewall throughput in various use cases. All use cases were measured while Threat intelligence mode was set to alert/deny.


|Firewall type and use case  |TCP/UDP bandwidth (Gbps)  |HTTP/S bandwidth (Gbps)  |
|---------|---------|---------|
|Standard     |30|30|
|Premium (no TLS/IDPS)     |30|30|
|Premium with TLS     |-|30|
|Premium with IDS     |30|30|
|Premium with IPS      |10|10|

> [!NOTE]
> IPS (Intrusion Prevention System) takes place when one or more signatures are configured to *Alert and Deny* mode.

Azure Firewall Premium’s new performance boost functionality is now in public preview and provides you with the following enhancements to the overall firewall performance:


|Firewall use case  |Without performance boost (Gbps)  |With performance boost (Gbps)  |
|---------|---------|---------|
|Standard<br>Max bandwidth for single TCP connection     |1.3|-|
|Premium<br>Max bandwidth for single TCP connection     |2.6|9.5|
|Premium max bandwidth with TLS/IDS|30|100|

Performance values are calculated with Azure Firewall at full scale and with Premium performance boost enabled. Actual performance may vary depending on your rule complexity and network configuration. These metrics are updated periodically as performance continuously evolves with each release.

To enable the Azure Firewall Premium performance boost, see [Azure Firewall preview features](firewall-preview.md#azure-firewall-premium-performance-boost-preview).


## Next steps

- Learn how to [deploy and configure an Azure Firewall](tutorial-firewall-deploy-portal.md).