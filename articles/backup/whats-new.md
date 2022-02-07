---
title: What's new in Azure Backup
description: Learn about new features in Azure Backup.
ms.topic: conceptual
ms.date: 01/27/2022
author: v-amallick
ms.service: backup
ms.author: v-amallick
---

# What's new in Azure Backup

Azure Backup is constantly improving and releasing new features that enhance the protection of your data in Azure. These new features expand your data protection to new workload types, enhance security, and improve the availability of your backup data. They also add new management, monitoring, and automation capabilities.

You can learn more about the new releases by bookmarking this page or by [subscribing to updates here](https://azure.microsoft.com/updates/?query=backup).

## Updates summary

- January 2022
  - [Back up Azure Database for PostgreSQL is now generally available](#back-up-azure-database-for-postgresql-is-now-generally-available)
- October 2021
  - [Archive Tier support for SQL Server/ SAP HANA in Azure VM from Azure portal](#archive-tier-support-for-sql-server-sap-hana-in-azure-vm-from-azure-portal)
  - [Multi-user authorization using Resource Guard (in preview)](#multi-user-authorization-using-resource-guard-in-preview)
  - [Multiple backups per day for Azure Files (in preview)](#multiple-backups-per-day-for-azure-files-in-preview)
  - [Azure Backup Metrics and Metrics Alerts (in preview)](#azure-backup-metrics-and-metrics-alerts-in-preview)
- July 2021
  - [Archive Tier support for SQL Server in Azure VM for Azure Backup is now generally available](#archive-tier-support-for-sql-server-in-azure-vm-for-azure-backup-is-now-generally-available)
- May 2021
  - [Backup for Azure Blobs is now generally available](#backup-for-azure-blobs-is-now-generally-available)
- April 2021
  - [Enhancements to encryption using customer-managed keys for Azure Backup (in preview)](#enhancements-to-encryption-using-customer-managed-keys-for-azure-backup-in-preview)
- March 2021
  - [Azure Disk Backup is now generally available](#azure-disk-backup-is-now-generally-available)
  - [Backup center is now generally available](#backup-center-is-now-generally-available)
  - [Archive Tier support for Azure Backup (in preview)](#archive-tier-support-for-azure-backup-in-preview)
- February 2021
  - [Backup for Azure Blobs (in preview)](#backup-for-azure-blobs-in-preview)

## Back up Azure Database for PostgreSQL is now generally available

Azure Backup and Azure Database services together help you to build an enterprise-class backup solution for Azure PostgreSQL (is now generally available). You can meet your data protection and compliance needs with a customer-controlled backup policy that enables retention of backups for up to 10 years.

With this, you've granular control to manage the backup and restore operations at the individual database level. Likewise, you can restore across PostgreSQL versions or to blob storage with ease. Besides using the Azure portal to perform the PostgreSQL database protection operations, you can also use the PowerShell, CLI, and REST API clients.

For more information, see [Azure Database for PostgreSQL backup](backup-azure-database-postgresql-overview.md).

## Archive Tier support for SQL Server/ SAP HANA in Azure VM from Azure portal

Azure Backup now supports the movement of recovery points to the Vault-archive tier for SQL Server and SAP HANA in Azure Virtual Machines from the Azure portal. This allows you to move the archivable recovery points corresponding to a particular database to the Vault-archive tier at one go.

Also, the support is extended via Azure CLI for the above workloads, along with Azure Virtual Machines (in preview).

For more information, see [Archive Tier support in Azure Backup](archive-tier-support.md).

## Multi-user authorization using Resource Guard (in preview)

Azure Backup now supports multi-user authorization (MUA) that allows you to add an additional layer of protection to critical operations on your Recovery Services vaults. For MUA, Azure Backup uses the Azure resource, Resource Guard, to ensure critical operations are performed only with applicable authorization.

For more information, see [how to protect Recovery Services vault and manage critical operations with MUA](./multi-user-authorization.md).

## Multiple backups per day for Azure Files (in preview)

Low RPO (Recovery Point Objective) is a key requirement for Azure Files that contains the frequently updated, business-critical data. To ensure minimal data loss in the event of a disaster or unwanted changes to file share content, you may prefer to take backups more frequently than once a day.

Using Azure Backup you can now  create a backup policy or modify an existing backup policy to take multiple snapshots in a  day. With this capability, you can also define the duration in which your backup jobs would trigger. This capability empowers you to align your backup schedule with the working hours when there are frequent updates to Azure Files content.

For more information, see [how to configure multiple backups per day via backup policy](./manage-afs-backup.md#create-a-new-policy).

## Azure Backup metrics and metrics alerts (in preview)

Azure Backup now provides a set of built-in metrics via [Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md) that allows you to monitor the health of your backups. You can also configure alert rules that trigger alerts when metrics exceed the defined thresholds.

Azure Backup offers the following key capabilities:
 
- Ability to view out-of-the-box metrics related to the backup and restore health of your backup items along with associated trends.
- Ability to write custom alert rules on these metrics to efficiently monitor the health of your backup items.
- Ability to route fired metric alerts to various notification channels that Azure Monitor supports, such as email, ITSM, webhook, logic apps, and so on.
 
Currently, Azure Backup supports built-in metrics for the following workload types:

- Azure VM
- SQL databases in Azure VM
- SAP HANA databases in Azure VM
- Azure Files.

For more information, see [Monitor the health of your backups using Azure Backup Metrics (preview)](metrics-overview.md).

## Archive Tier support for SQL Server in Azure VM for Azure Backup is now generally available

Azure Backup allows you to move your long-term retention points for Azure Virtual Machines and SQL Server in Azure Virtual Machines to the low-cost Archive Tier. You can also restore from the recovery points in the Vault-archive tier.

In addition to the capability to move the recovery points:

- Azure Backup provides recommendations to move a specific set of recovery points for Azure Virtual Machine backups that'll ensure cost savings.
- You have the capability to move all their recovery points for a particular backup item at one go using sample scripts.
- You can view Archive storage usage on the Vault dashboard.

For more information, see [Archive Tier support](./archive-tier-support.md).

## Backup for Azure Blobs is now generally available

Operational backup for Azure Blobs is a managed-data protection solution that lets you protect your block blob data from various data loss scenarios, such as blob corruptions, blob deletions, and accidental deletion of storage accounts.

Being an operational backup solution, the backup data is stored locally in the source storage account, and can be recovered from a selected point-in-time, giving you a simple and cost-effective means to protect your blob data. To do this, the solution uses the blob point-in-time restore capability available from blob storage.

Operational backup for blobs integrates with the Azure Backup management tools, including Backup Center, to help you manage the protection of your blob data effectively and at-scale. In addition to previously available capabilities, you can now configure and manage operational backup for blobs using the **Data protection** view of the storage accounts, also  [through PowerShell](backup-blobs-storage-account-ps.md). Also, Backup now gives you an enhanced experience for managing role assignments required for configuring operational backup.

For more information, see [Overview of operational backup for Azure Blobs](blob-backup-overview.md).

## Enhancements to encryption using customer-managed keys for Azure Backup (in preview)

Azure Backup now provides enhanced capabilities (in preview) to manage encryption with customer-managed keys. Azure Backup allows you to bring in your own keys to encrypt the backup data in the Recovery Services vaults, thus providing you a better control.

- Supports user-assigned managed identities to grant permissions to the keys to manage data encryption in the Recovery Services vault.
- Enables encryption with customer-managed keys while creating a Recovery Services vault.
  >[!NOTE]
  >This feature is currently in limited preview. To sign up, fill [this form](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u), and write to us at [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).
- Allows you to use Azure Policies to audit and enforce encryption using customer-managed keys.
>[!NOTE]
>- The above capabilities are supported through the Azure portal only, PowerShell is currently not supported.<br>If you are using PowerShell for managing encryption keys for Backup, we do not recommend to update the keys from the portal.<br>If you update the key from the portal, you can’t use PowerShell to update the encryption key further, till a PowerShell update to support the new model is available. However, you can continue updating the key from the Azure portal.
>- You can use the audit policy for auditing vaults with encryption using customer-managed keys that are enabled after 04/01/2021.  
>- For vaults with the CMK encryption enabled before this date, the policy might fail to apply, or might show false negative results (that is, these vaults may be reported as non-compliant, despite having CMK encryption enabled). [Learn more](encryption-at-rest-with-cmk.md#use-azure-policies-to-audit-and-enforce-encryption-with-customer-managed-keys-in-preview).

For more information, see [Encryption for Azure Backup using customer-managed keys](encryption-at-rest-with-cmk.md). 

## Azure Disk Backup is now generally available

Azure Backup offers snapshot lifecycle management to Azure Managed Disks by automating periodic creation of snapshots and retaining these for configured durations using Backup policy.

For more information, see [Overview of Azure Disk Backup](disk-backup-overview.md).

## Backup center is now generally available

Backup center simplifies data protection management at-scale by enabling you to discover, govern, monitor, operate, and optimize backup management from one single central console.

For more information, see [Overview of Backup Center](backup-center-overview.md).

## Archive Tier support for Azure Backup (in preview)

Azure Backup now allows you to reduce the cost of long-term retention backups with the availability of Archive Tier for Azure virtual machines and SQL Server in Azure virtual machines.

For more information, see [Archive Tier support (Preview)](archive-tier-support.md).

## Backup for Azure Blobs (in preview)

Operational backup for Blobs is a managed, local data protection solution that lets you protect your block blobs from various data loss scenarios like corruptions, blob deletions, and accidental storage account deletion. The data is stored locally within the source storage account itself and can be recovered to a selected point in time whenever needed. So it provides a simple, secure, and cost-effective means to protect your blobs.

Operational backup for Blobs integrates with Backup Center, among other Backup management capabilities, to provide a single pane of glass that can help you govern, monitor, operate, and analyze backups at scale.

For more information, see [Overview of operational backup for Azure Blobs (in preview)](blob-backup-overview.md).

## Next steps

- [Azure Backup guidance and best practices](guidance-best-practices.md)
- [Archived release notes](backup-release-notes-archived.md)