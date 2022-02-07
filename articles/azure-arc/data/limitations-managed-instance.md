---
title: Limitations of Azure Arc-enabled SQL Managed Instance
description: Limitations of Azure Arc-enabled SQL Managed Instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 09/07/2021
ms.topic: conceptual
---

# Limitations of Azure Arc-enabled SQL Managed Instance

This article describes limitations of Azure Arc-enabled SQL Managed Instance. 

At this time, the business critical service tier is public preview. The general purpose service tier is generally available.

## Backup and restore

### Automated backups 

-  User databases with SIMPLE recovery model are not backed up.
-  System database `model` is not backed up in order to prevent interference with creation/deletion of database. The database gets locked when admin operations are performed.

### Point-in-time restore (PITR)

-  Doesn't support restore from one Azure Arc-enabled SQL Managed Instance to another Azure Arc-enabled SQL Managed Instance.  The database can only be restored to the same Arc-enabled SQL Managed Instance where the backups were created.
-  Renaming of a databases is currently not supported, for point in time restore purposes.
-  No support for restoring a TDE enabled database currently.
-  A deleted database cannot be restored currently.

## Other limitations 

-  Transactional replication is currently not supported.
-  Log shipping is currently blocked.

## Roles and responsibilities

The roles and responsibilities between Microsoft and its customers differ between Azure PaaS services (Platform As A Service) and Azure hybrid (like Azure Arc-enabled SQL Managed Instance). 

### Frequently asked questions

The table below summarizes answers to frequently asked questions regarding support roles and responsibilities.

| Question                          | Azure Platform As A Service (PaaS) | Azure Arc hybrid services |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Who provides the infrastructure?  | Microsoft                          | Customer                  |
| Who provides the software?*       | Microsoft                          | Microsoft                 |
| Who does the operations?          | Microsoft                          | Customer                  |
| Does Microsoft provide SLAs?      | Yes                                | No                        |
| Who’s in charge of SLAs?          | Microsoft                          | Customer                  |

\* Azure services

__Why doesn't Microsoft provide SLAs on Azure Arc hybrid services?__ Because Microsoft does not own the infrastructure and does not operate it. Customers do.

## Next steps

- **Try it out.** Get started quickly with [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) on Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) or in an Azure VM. 

- **Create your own.** Follow these steps to create on your own Kubernetes cluster: 
   1. [Install the client tools](install-client-tools.md)
   2. [Plan an Azure Arc-enabled data services deployment](plan-azure-arc-data-services.md)
   3. [Create an Azure Arc-enabled SQL Managed Instance](create-sql-managed-instance.md) 

- **Learn**
   - [Read more about Azure Arc-enabled data services](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Read about Azure Arc](https://aka.ms/azurearc)
