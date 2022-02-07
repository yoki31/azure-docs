---
title: Back up an SAP HANA database to Azure with Azure Backup 
description: In this article, learn how to back up an SAP HANA database to Azure virtual machines with the Azure Backup service.
ms.topic: conceptual
ms.date: 02/03/2022
author: v-amallick
ms.service: backup
ms.author: v-amallick
---

# Back up SAP HANA databases in Azure VMs

SAP HANA databases are critical workloads that require a low recovery-point objective (RPO) and long-term retention. You can back up SAP HANA databases running on Azure virtual machines (VMs) by using [Azure Backup](backup-overview.md).

This article shows how to back up SAP HANA databases that are running on Azure VMs to an Azure Backup Recovery Services vault.

In this article, you'll learn how to:
> [!div class="checklist"]
>
> * Create and configure a vault
> * Discover databases
> * Configure backups
> * Run an on-demand backup job

>[!NOTE]
Refer to the [SAP HANA backup support matrix](sap-hana-backup-support-matrix.md) to know more about the supported configurations and scenarios.

## Prerequisites

Refer to the [prerequisites](tutorial-backup-sap-hana-db.md#prerequisites) and the [What the pre-registration script does](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) sections to set up the database for backup.

### Establish network connectivity

For all operations, an SAP HANA database running on an Azure VM requires connectivity to the Azure Backup service, Azure Storage, and Azure Active Directory. This can be achieved by using private endpoints or by allowing access to the required public IP addresses or FQDNs. Not allowing proper connectivity to the required Azure services may lead to failure in operations like database discovery, configuring backup, performing backups, and restoring data.

The following table lists the various alternatives you can use for establishing connectivity:

| **Option**                        | **Advantages**                                               | **Disadvantages**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Private endpoints                 | Allow backups over private IPs inside the virtual network  <br><br>   Provide granular control on the network and vault side | Incurs standard private endpoint [costs](https://azure.microsoft.com/pricing/details/private-link/) |
| NSG service tags                  | Easier to manage as range changes are automatically merged   <br><br>   No additional costs | Can be used with NSGs only  <br><br>    Provides access to the entire service |
| Azure Firewall FQDN tags          | Easier to manage since the required FQDNs are automatically managed | Can be used with Azure Firewall only                         |
| Allow access to service FQDNs/IPs | No additional costs   <br><br>  Works with all network security appliances and firewalls | A broad set of IPs or FQDNs may be required to be accessed   |
| Use an HTTP proxy                 | Single point of internet access to VMs                       | Additional costs to run a VM with the proxy software         |
| [Virtual Network Service Endpoint](../virtual-network/virtual-network-service-endpoints-overview.md)    |     Can be used for Azure Storage (= Recovery Services vault).     <br><br>     Provides large benefit to optimize performance of data plane traffic.          |         Can’t be used for Azure AD, Azure Backup service.    |
| Network Virtual Appliance      |      Can be used for Azure Storage, Azure AD, Azure Backup service. <br><br> **Data plane**   <ul><li>      Azure Storage: `*.blob.core.windows.net`, `*.queue.core.windows.net`, `*.blob.storage.azure.net`  </li></ul>   <br><br>     **Management plane**  <ul><li>  Azure AD: Allow access to FQDNs mentioned in sections 56 and 59 of [Microsoft 365 Common and Office Online](/microsoft-365/enterprise/urls-and-ip-address-ranges?view=o365-worldwide&preserve-view=true#microsoft-365-common-and-office-online). </li><li>   Azure Backup service: `.backup.windowsazure.com` </li></ul> <br>Learn more about [Azure Firewall service tags](../firewall/fqdn-tags.md).       |  Adds overhead to data plane traffic and decrease throughput/performance.  |

More details around using these options are shared below:

#### Private endpoints

Private endpoints allow you to connect securely from servers inside a virtual network to your Recovery Services vault. The private endpoint uses an IP from the VNET address space for your vault. The network traffic between your resources inside the virtual network and the vault travels over your virtual network and a private link on the Microsoft backbone network. This eliminates exposure from the public internet. Read more on private endpoints for Azure Backup [here](./private-endpoints.md).

#### NSG tags

If you use Network Security Groups (NSG), use the *AzureBackup* service tag to allow outbound access to Azure Backup. In addition to the Azure Backup tag, you also need to allow connectivity for authentication and data transfer by creating similar [NSG rules](../virtual-network/network-security-groups-overview.md#service-tags) for Azure AD (*AzureActiveDirectory*) and Azure Storage(*Storage*).  The following steps describe the process to create a rule for the Azure Backup tag:

1. In **All Services**, go to **Network security groups** and select the network security group.

1. Select **Outbound security rules** under **Settings**.

1. Select **Add**. Enter all the required details for creating a new rule as described in [security rule settings](../virtual-network/manage-network-security-group.md#security-rule-settings). Ensure the option **Destination** is set to *Service Tag* and **Destination service tag** is set to *AzureBackup*.

1. Select **Add**  to save the newly created outbound security rule.

You can similarly create NSG outbound security rules for Azure Storage and Azure AD. For more information on service tags, see [this article](../virtual-network/service-tags-overview.md).

#### Azure Firewall tags

If you're using Azure Firewall, create an application rule by using the *AzureBackup* [Azure Firewall FQDN tag](../firewall/fqdn-tags.md). This allows all outbound access to Azure Backup.

#### Allow access to service IP ranges

If you choose to allow access service IPs, refer to the IP ranges in the JSON file available [here](https://www.microsoft.com/download/confirmation.aspx?id=56519). You'll need to allow access to IPs corresponding to Azure Backup, Azure Storage, and Azure Active Directory.

#### Allow access to service FQDNs

You can also use the following FQDNs to allow access to the required services from your servers:

| Service    | Domain  names to be accessed                             |   Ports        |
| -------------- | ------------------------------------------------------------ | ---------------------- |
| Azure  Backup  | `*.backup.windowsazure.com`                             |  443       |
| Azure  Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` <br><br> `*.blob.storage.azure.net` |   443    |
| Azure  AD      | Allow  access to FQDNs under sections 56 and 59 according to [this article](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |    As applicable      |

#### Use an HTTP proxy server to route traffic

> [!NOTE]
> Currently, there is no proxy support for SAP HANA. Please consider other options such as private end points if you wish to remove outbound connectivity requirements for database backups via Azure backup in HANA VMs.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## Enable Cross Region Restore

At the Recovery Services vault, you can enable Cross Region Restore. You must turn on Cross Region Restore before you configure and protect backups on your HANA databases. Learn about [how to turn on Cross Region Restore](./backup-create-rs-vault.md#set-cross-region-restore).

[Learn more](./backup-azure-recovery-services-vault-overview.md) about Cross Region Restore.

## Discover the databases

1. In the Azure portal, go to **Backup center** and click **+Backup**.

   :::image type="content" source="./media/backup-azure-sap-hana-database/backup-center-configure-inline.png" alt-text="Screenshot showing to start checking for SAP HANA databases." lightbox="./media/backup-azure-sap-hana-database/backup-center-configure-expanded.png":::

1. Select **SAP HANA in Azure VM** as the datasource type, select a Recovery Services vault to use for backup, and then click **Continue**.

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-select-vault.png" alt-text="Screenshot showing to select an SAP HANA database in Azure VM.":::

1. Select **Start Discovery**. This initiates discovery of unprotected Linux VMs in the vault region.

   * After discovery, unprotected VMs appear in the portal, listed by name and resource group.
   * If a VM isn't listed as expected, check whether it's already backed up in a vault.
   * Multiple VMs can have the same name but they belong to different resource groups.

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-discover-databases.png" alt-text="Screenshot showing to select Start Discovery.":::

1. In **Select Virtual Machines**, select the link to download the script that provides permissions for the Azure Backup service to access the SAP HANA VMs for database discovery.
1. Run the script on each VM hosting SAP HANA databases that you want to back up.
1. After running the script on the VMs, in **Select Virtual Machines**, select the VMs. Then select **Discover DBs**.
1. Azure Backup discovers all SAP HANA databases on the VM. During discovery, Azure Backup registers the VM with the vault, and installs an extension on the VM. No agent is installed on the database.

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-select-virtual-machines-inline.png" alt-text="Screenshot showing the discovered SAP HANA databases." lightbox="./media/backup-azure-sap-hana-database/hana-select-virtual-machines-expanded.png":::

## Configure backup  

Now enable backup.

1. In Step 2, select **Configure Backup**.

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-configure-backups.png" alt-text="Screenshot showing to configure Backup.":::

2. In **Select items to back up**, select all the databases you want to protect > **OK**.

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-select-databases-inline.png" alt-text="Screenshot showing to select databases to back up." lightbox="./media/backup-azure-sap-hana-database/hana-select-databases-expanded.png":::

3. In **Backup Policy** > **Choose backup policy**, create a new backup policy for the databases, in accordance with the instructions below.

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-policy-summary.png" alt-text="Screenshot showing to choose backup policy.":::

4. After creating the policy, on the **Backup** menu, select **Enable backup**.

    ![Enable backup](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Track the backup configuration progress in the **Notifications** area of the portal.

### Create a backup policy

A backup policy defines when backups are taken, and how long they're retained.

* A policy is created at the vault level.
* Multiple vaults can use the same backup policy, but you must apply the backup policy to each vault.

>[!NOTE]
>Azure Backup doesn’t automatically adjust for daylight saving time changes when backing up an SAP HANA database running in an Azure VM.
>
>Modify the policy manually as needed.

Specify the policy settings as follows:

1. In **Policy name**, enter a name for the new policy.

   ![Enter policy name](./media/backup-azure-sap-hana-database/policy-name.png)
1. In **Full Backup policy**, select a **Backup Frequency**, choose **Daily** or **Weekly**.
   * **Daily**: Select the hour and time zone in which the backup job begins.
       * You must run a full backup. You can't turn off this option.
       * Select **Full Backup** to view the policy.
       * You can't create differential backups for daily full backups.
   * **Weekly**: Select the day of the week, hour, and time zone in which the backup job runs.

   ![Select backup frequency](./media/backup-azure-sap-hana-database/backup-frequency.png)

1. In **Retention Range**, configure retention settings for the full backup.
    * By default all options are selected. Clear any retention range limits you don't want to use, and set those that you do.
    * The minimum retention period for any type of backup (full/differential/log) is seven days.
    * Recovery points are tagged for retention based on their retention range. For example, if you select a daily full backup, only one full backup is triggered each day.
    * The backup for a specific day is tagged and retained based on the weekly retention range and setting.
    * The monthly and yearly retention ranges behave in a similar way.

1. In the **Full Backup policy** menu, select **OK** to accept the settings.
1. Select **Differential Backup** to add a differential policy.
1. In **Differential Backup policy**, select **Enable** to open the frequency and retention controls.
    * At most, you can trigger one differential backup per day.
    * Differential backups can be retained for a maximum of 180 days. If you need longer retention, you must use full backups.

    ![Differential backup policy](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > You can choose either a differential or an incremental as a daily backup but not both.
1. In **Incremental Backup policy**, select **Enable** to open the frequency and retention controls.
    * At most, you can trigger one incremental backup per day.
    * Incremental backups can be retained for a maximum of 180 days. If you need longer retention, you must use full backups.

    ![Incremental backup policy](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

1. Select **OK** to save the policy and return to the main **Backup policy** menu.
1. Select **Log Backup** to add a transactional log backup policy,
    * In **Log Backup**, select **Enable**.  This can't be disabled, since SAP HANA manages all log backups.
    * Set the frequency and retention controls.

    > [!NOTE]
    > Log backups only begin to flow after a successful full backup is completed.

1. Select **OK** to save the policy and return to the main **Backup policy** menu.
1. After you finish defining the backup policy, select **OK**.

> [!NOTE]
> Each log backup is chained to the previous full backup to form a recovery chain. This full backup will be retained until the retention of the last log backup has expired. This might mean that the full backup is retained for an extra period to make sure all the logs can be recovered. Let's assume a user has a weekly full backup, daily differential and 2 hour logs. All of them are retained for 30 days. But, the weekly full can be really cleaned up/deleted only after the next full backup is available, that is, after 30 + 7 days. For example, a weekly full backup happens on Nov 16th. According to the retention policy, it should be retained until Dec 16th. The last log backup for this full happens before the next scheduled full, on Nov 22nd. Until this log is available until Dec 22nd, the Nov 16th full can't be deleted. So, the Nov 16th full is retained until Dec 22nd.

## Run an on-demand backup

Backups run in accordance with the policy schedule. You can run a backup on-demand as follows:

1. In the vault menu, select **Backup items**.
2. In **Backup Items**,  select the VM running the SAP HANA database, and then select **Backup now**.
3. In **Backup Now**, choose the type of backup you want to perform. Then select **OK**. This backup will be retained for 45 days.
4. Monitor the portal notifications. You can monitor the job progress in the vault dashboard > **Backup Jobs** > **In progress**. Depending on the size of your database, creating the initial backup may take a while.

By default, the retention of on-demand backups is 45 days.

## Run SAP HANA Studio backup on a database with Azure Backup enabled

If you want to take a local backup (using HANA Studio) of a database that's being backed up with Azure Backup, do the following:

1. Wait for any full or log backups for the database to finish. Check the status in SAP HANA Studio / Cockpit.
1. Disable log backups, and set the backup catalog to the file system for relevant database.
1. To do this, double-click **systemdb** > **Configuration** > **Select Database** > **Filter (Log)**.
1. Set **enable_auto_log_backup** to **No**.
1. Set **log_backup_using_backint** to **False**.
1. Set **catalog_backup_using_backint** to **False**.
1. Take an on-demand full backup of the database.
1. Wait for the full backup and catalog backup to finish.
1. Revert the previous settings back to those for Azure:
    * Set **enable_auto_log_backup** to **Yes**.
    * Set **log_backup_using_backint** to **True**.
    * Set **catalog_backup_using_backint** to **True**.

## Next steps

* Learn how to [restore SAP HANA databases running on Azure VMs](./sap-hana-db-restore.md)
* Learn how to [manage SAP HANA databases that are backed up using Azure Backup](./sap-hana-db-manage.md)
