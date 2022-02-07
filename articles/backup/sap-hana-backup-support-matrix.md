---
title: SAP HANA Backup support matrix
description: In this article, learn about the supported scenarios and limitations when you use Azure Backup to back up SAP HANA databases on Azure VMs.
ms.topic: conceptual
ms.date: 01/13/2022
ms.custom: references_regions 
author: v-amallick
ms.service: backup
ms.author: v-amallick
---

# Support matrix for backup of SAP HANA databases on Azure VMs

Azure Backup supports the backup of SAP HANA databases to Azure. This article summarizes the scenarios supported and limitations present when you use Azure Backup to back up SAP HANA databases on Azure VMs.

> [!NOTE]
> The frequency of log backup can now be set to a minimum of 15 minutes. Log backups only begin to flow after a successful full backup for the database has completed.

## Scenario support

| **Scenario**               | **Supported  configurations**                                | **Unsupported  configurations**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topology**               | SAP HANA running in Azure Linux  VMs only                    | HANA Large Instances (HLI)                                   |
| **Regions**                   | **GA:**<br> **Americas** – Central US, East US 2, East US, North Central US, South Central US, West US 2, West US 3, West Central US, West US, Canada Central, Canada East, Brazil South <br> **Asia Pacific** – Australia Central, Australia Central 2, Australia East, Australia Southeast, Japan East, Japan West, Korea Central, Korea South, East Asia, Southeast Asia, Central India, South India, West India, China East, China North, China East2, China North 2 <br> **Europe** – West Europe, North Europe, France Central, UK South, UK West, Germany North, Germany West Central, Switzerland North, Switzerland West, Central Switzerland North, Norway East, Norway West <br> **Africa / ME** - South Africa North, South Africa West, UAE North, UAE Central  <BR>  **Azure Government regions** | France South, Germany Central, Germany Northeast, US Gov IOWA |
| **OS versions**            | SLES 12 with SP2, SP3, SP4 and SP5; SLES 15 with SP0, SP1, SP2 and SP3 <br><br>  RHEL 7.4, 7.6, 7.7, 7.9, 8.1, 8.2 and 8.4                |                                             |
| **HANA versions**          | SDC on HANA 1.x, MDC on HANA 2.x SPS04, SPS05 Rev <= 56, SPS 06 (validated for encryption enabled scenarios as well)      |                                                            |
| **Encryption** | SSLEnforce, HANA data encryption |            |
| **HANA deployments**       | SAP HANA on a single Azure VM -  Scale up only. <br><br> For high availability deployments, both the nodes on the two different machines are treated as individual nodes with separate data chains.               | Scale-out <br><br> In high availability deployments, backup doesn’t failover to the secondary node automatically. Configuring backup should be done separately for each node.                                           |
| **HANA Instances**         | A single SAP HANA instance on a  single Azure VM – scale up only | Multiple SAP HANA instances on a  single VM. You can protect only one of these multiple instances at a time.                  |
| **HANA database types**    | Single Database Container (SDC)  ON 1.x, Multi-Database Container (MDC) on 2.x | MDC in HANA 1.x                                              |
| **HANA database size**     | HANA databases of size <= 8 TB  (this isn't the memory size of the HANA system)               |                                                              |
| **Backup types**           | Full, Differential, Incremental and Log backups                          |  Snapshots                                       |
| **Restore types**          | Refer to the SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) to learn about the supported restore types |                                                              |
| **Backup limits**          | Up to 8 TB of full backup size per SAP HANA instance (soft limit)         |                                                              |
| **Number of full backups per day**     |   One scheduled backup.  <br><br>   Three on-demand backups. <br><br> We recommend not to trigger more than three backups per day. However, to allow user retries in case of failed attempts, hard limit for on-demand backups is set to nine attempts.  |
| **Special configurations** |                                                              | SAP HANA + Dynamic Tiering <br>  Cloning through LaMa        |

------

>[!NOTE]
>Azure Backup doesn’t automatically adjust for daylight saving time changes when backing up a SAP HANA database running in an Azure VM.
>
>Modify the policy manually as needed.

> [!NOTE]
> You can now [monitor the backup and restore](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) jobs (to the same machine) triggered from HANA native clients (SAP HANA Studio/ Cockpit/ DBA Cockpit) in the Azure portal.

## Next steps

* Learn how to [backup SAP HANA databases running on Azure VMs](./backup-azure-sap-hana-database.md)
* Learn how to [restore SAP HANA databases running on Azure VMs](./sap-hana-db-restore.md)
* Learn how to [manage SAP HANA databases that are backed up using Azure Backup](sap-hana-db-manage.md)
* Learn how to [troubleshoot common issues when backing up SAP HANA databases](./backup-azure-sap-hana-database-troubleshoot.md)
