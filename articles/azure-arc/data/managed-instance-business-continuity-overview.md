---
title: Business continuity overview - Azure Arc-enabled SQL Managed Instance
description: Overview business continuity for Azure Arc-enabled SQL Managed Instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 01/27/2022
ms.topic: conceptual
---

# Overview: Azure Arc-enabled SQL Managed Instance business continuity (preview)

Business continuity is a combination of people, processes, and technology that enables businesses to recover and continue operating in the event of disruptions. In hybrid scenarios there is a joint responsibility between Microsoft and customer, such that  customer owns and manages the on-premises infrastructure while the software is provided by Microsoft. 

Business continuity for Azure Arc-enabled SQL Managed Instance is available as preview.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## Features

This overview describes the set of capabilities that come built-in with Azure Arc-enabled SQL Managed Instance and how you can leverage them to recover from disruptions. 

| Feature         | Use case     | Service Tier      | 
|--------------|-----------|---------------|
| Point in time restore | Use the built-in point in time restore (PITR) feature to recover from situations such as data corruptions caused by human errors. Learn more about [Point in time restore](.\point-in-time-restore.md) | Available in both General Purpose and Business Critical service tiers|
| High availability | Deploy the Azure Arc enabled SQL Managed Instance in high availability mode to achieve local high availability. This mode automatically recovers from scenarios such as hardware failures, pod/node failures, and etc. The built-in listener service automatically redirects new connections to another replica while Kubernetes attempts to rebuild the failed replica. Learn more about  [high-availability in Azure Arc-enabled SQL Managed Instance](.\managed-instance-high-availability.md) |This feature is only available in the Business Critical service tier. <br> For General Purpose service tier, Kubernetes provides basic recoverability from scenarios such as node/pod crashes. |
|Disaster recovery| Configure disaster recovery by setting up another Azure Arc-enabled SQL Managed Instance in a geographically separate data center to synchronize data from the primary data center. This scenario is useful for recovering from events when an entire data center is down due to disruptions such as power outages or other events. |  Available in both General Purpose and Business Critical service tiers| 
|

## Next steps

[Learn more about configuring point in time restore](.\point-in-time-restore.md)

[Learn more about configuring high availability in Azure Arc-enabled SQL Managed Instance](.\managed-instance-high-availability.md)

[Learn more about setting up and configuring disaster recovery in Azure Arc-enabled SQL Managed Instance](.\managed-instance-disaster-recovery.md)
