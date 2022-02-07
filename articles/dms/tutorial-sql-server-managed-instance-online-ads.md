---
title: "Tutorial: Migrate SQL Server to Azure SQL Managed Instance online using Azure Data Studio"
titleSuffix: Azure Database Migration Service(DMS)
description: Migrate SQL Server to an Azure SQL Managed Instance online using Azure Data Studio with Azure Database Migration Service
services: dms
author: mokabiru
ms.author: mokabiru
manager: 
ms.reviewer: cawrites
ms.service: dms
ms.workload: data-services
ms.custom: "seo-lt-2019"
ms.topic: tutorial
ms.date: 10/05/2021
---

# Tutorial: Migrate SQL Server to an Azure SQL Managed Instance online using Azure Data Studio with DMS (preview)

Use the Azure SQL Migration extension in Azure Data Studio to migrate database(s) from a SQL Server instance to an [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) with minimal downtime. For methods that may require some manual effort, see the article [SQL Server instance migration to Azure SQL Managed Instance](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).

In this tutorial, you migrate the **Adventureworks** database from an on-premises instance of SQL Server to Azure SQL Managed Instance with minimal downtime by using Azure Data Studio with Azure Database Migration Service (DMS).

In this tutorial, you learn how to:
> [!div class="checklist"]
>
> * Launch the *Migrate to Azure SQL* wizard in Azure Data Studio.
> * Run an assessment of your source SQL Server database(s)
> * Specify details of your source SQL Server, backup location and your target Azure SQL Managed Instance
> * Create a new Azure Database Migration Service and install the self-hosted integration runtime to access source server and backups.
> * Start and monitor the progress for your migration.
> * Perform the migration cutover when you are ready.

> [!IMPORTANT]
> Prepare for migration and reduce the duration of the online migration process as much as possible to minimize the risk of interruption caused by instance reconfiguration or planned maintenance. In case of such an event, migration process will start from the beginning. In case of planned maintenance, there is a grace period of 36 hours where the target Azure SQL Managed Instance configuration or maintenance will be held before migration process is restarted.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

This article describes an online database migration from SQL Server to Azure SQL Managed Instance. For an offline database migration, see [Migrate SQL Server to a SQL Managed Instance offline using Azure Data Studio with DMS](tutorial-sql-server-managed-instance-offline-ads.md).

## Prerequisites

To complete this tutorial, you need to:

* [Download and install Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Install the Azure SQL Migration extension](/sql/azure-data-studio/extensions/azure-sql-migration-extension) from the Azure Data Studio marketplace
* Have an Azure account that is assigned to one of the built-in roles listed below:
    - Contributor for the target Azure SQL Managed Instance (and Storage Account to upload your database backup files from SMB network share).
    - Owner or Contributor role for the Azure Resource Groups containing the target Azure SQL Managed Instance or the Azure storage account.
    - Owner or Contributor role for the Azure subscription (required if creating a new DMS service).
* Create a target [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md).
* Ensure that the logins used to connect the source SQL Server are members of the *sysadmin* server role or have `CONTROL SERVER` permission. 
* Use one of the following storage options for the full database and transaction log backup files: 
    - SMB network share 
    - Azure storage account file share or blob container 

    > [!IMPORTANT]
    > - If your database backup files are provided in an SMB network share, [Create an Azure storage account](../storage/common/storage-account-create.md) that allows the DMS service to upload the database backup files.  Make sure to create the Azure Storage Account in the same region as the Azure Database Migration Service instance is created.
    > - You can't use an Azure Storage account that has a private endpoint with Azure Database Migration Service.
    > - Azure Database Migration Service does not initiate any backups, and instead uses existing backups, which you may already have as part of your disaster recovery plan, for the migration.
    > - You should take [backups using the `WITH CHECKSUM` option](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server). 
    > - Each backup can be written to either a separate backup file or multiple backup files. However, appending multiple backups (i.e. full and t-log) into a single backup media is not supported. 
    > - Use compressed backups to reduce the likelihood of experiencing potential issues associated with migrating large backups.
* Ensure that the service account running the source SQL Server instance has read and write permissions on the SMB network share that contains database backup files.
* The source SQL Server instance certificate from a database protected by Transparent Data Encryption (TDE) needs to be migrated to the target Azure SQL Managed Instance or SQL Server on Azure Virtual Machine before migrating data. To learn more, see [Migrate a certificate of a TDE-protected database to Azure SQL Managed Instance](../azure-sql/managed-instance/tde-certificate-migrate.md) and [Move a TDE Protected Database to Another SQL Server](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
    > [!TIP]
    > If your database contains sensitive data that is protected by [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio), migration process using Azure Data Studio with DMS will automatically migrate your Always Encrypted keys to your target Azure SQL Managed Instance or SQL Server on Azure Virtual Machine.

* If your database backups are in a network file share, provide a machine to install [self-hosted integration runtime](../data-factory/create-self-hosted-integration-runtime.md) to access and migrate database backups. The migration wizard provides the download link and authentication keys to download and install your self-hosted integration runtime. In preparation for the migration, ensure that the machine where you plan to install the self-hosted integration runtime has the following outbound firewall rules and domain names enabled:

    | Domain names                                          | Outbound ports | Description                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | Public Cloud: `{datafactory}.{region}.datafactory.azure.net`<br> or `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> China: `{datafactory}.{region}.datafactory.azure.cn` | 443            | Required by the self-hosted integration runtime to connect to the Data Migration service. <br>For new created Data Factory in public cloud, locate the FQDN from your Self-hosted Integration Runtime key, which is in format `{datafactory}.{region}.datafactory.azure.net`. For old Data factory, if you don't see the FQDN in your Self-hosted Integration key, use *.frontend.clouddatahub.net instead. |
    | `download.microsoft.com`    | 443            | Required by the self-hosted integration runtime for downloading the updates. If you have disabled auto-update, you can skip configuring this domain. |
    | `*.core.windows.net`          | 443            | Used by the self-hosted integration runtime that connects to the Azure storage account for uploading database backups from your network share |

    > [!TIP]
    > If your database backup files are already provided in an Azure storage account, self-hosted integration runtime is not required during the migration process.

* When using self-hosted integration runtime, make sure that the machine where the runtime is installed can connect to the source SQL Server instance and the network file share where backup files are located. Outbound port 445 should be enabled to allow access to the network file share. Also see [recommendations for using self-hosted integration runtime](migration-using-azure-data-studio.md#recommendations-for-using-self-hosted-integration-runtime-for-database-migrations)
* If you're using the Azure Database Migration Service for the first time, ensure that Microsoft.DataMigration resource provider is registered in your subscription. You can follow the steps to [register the resource provider](quickstart-create-data-migration-service-portal.md#register-the-resource-provider)

## Launch the Migrate to Azure SQL wizard in Azure Data Studio

1. Open Azure Data Studio and select the server icon to connect to your on-premises SQL Server (or SQL Server on Azure Virtual Machine).
1. On the server connection, right-click and select **Manage**.
1. On the server's home page, Select **Azure SQL Migration** extension.
1. On the Azure SQL Migration dashboard, select **Migrate to Azure SQL** to launch the migration wizard.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/launch-migrate-to-azure-sql-wizard.png" alt-text="Launch Migrate to Azure SQL wizard":::
1. In the first step of the migration wizard, link your existing or new Azure account to Azure Data Studio.

## Run database assessment and select target

1. Select the database(s) to run assessment and select **Next**.
1. Select Azure SQL Managed Instance as the target.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/assessment-complete-target-selection.png" alt-text="Assessment confirmation":::
1. Select on the **View/Select** button to view details of the assessment results for your database(s), select the database(s) to migrate, and select **OK**. If any issues are displayed in the assessment results, they need to be remediated before proceeding with the next steps.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/assessment-issues-details.png" alt-text="Database assessment details":::
1. Specify your **target Azure SQL Managed Instance** by selecting your subscription, location, resource group from the corresponding drop-down lists and select **Next**.

## Configure migration settings

1. Select **Online migration** as the migration mode.
    > [!NOTE]
    > In the online migration mode, the source SQL Server database is available for read and write activity while database backups are continuously restored on target Azure SQL Managed Instance. Application downtime is limited to duration for the cutover at the end of migration.
1. Select the location of your database backups. Your database backups can either be located on an on-premises network share or in an Azure storage blob container.
    > [!NOTE]
    > If your database backups are provided in an on-premises network share, DMS will require you to setup self-hosted integration runtime in the next step of the wizard. Self-hosted integration runtime is required to access your source database backups, check the validity of the backup set and upload them to Azure storage account.<br/> If your database backups are already on an Azure storage blob container, you do not need to setup self-hosted integration runtime.
1. After selecting the backup location, provide details of your source SQL Server and source backup location.

    |Field    |Description  |
    |---------|-------------|
    |**Source Credentials - Username**    |The credential (Windows / SQL authentication) to connect to the source SQL Server instance and validate the backup files.         |
    |**Source Credentials - Password**    |The credential (Windows / SQL authentication) to connect to the source SQL Server instance and validate the backup files.         |
    |**Network share location that contains backups**     |The network share location that contains the full and transaction log backup files. Any invalid files or backups files in the network share that don't belong to the valid backup set will be automatically ignored during the migration process.        |
    |**Windows user account with read access to the network share location**     |The Windows credential (username) that has read access to the network share to retrieve the backup files.       |
    |**Password**     |The Windows credential (password) that has read access to the network share to retrieve the backup files.         |
    |**Target database name** |The target database name can be modified if you wish to change the database name on the target during the migration process.            |

1. Specify the **Azure storage account** by selecting the **Subscription**, **Location**, and **Resource Group** from the corresponding drop-down lists. This Azure storage account will be used by DMS to upload the database backups from network share. You don't need to create a container as DMS will automatically create a blob container in the specified storage account during the upload process.
    > [!IMPORTANT]
    > If loopback check functionality is enabled and the source SQL Server and file share are on the same computer, then source won't be able to access the files hare using FQDN. To fix this issue, disable loopback check functionality using the instructions [here](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)

## Create Azure Database Migration Service

1. Create a new Azure Database Migration Service or reuse an existing Service that you previously created.
    > [!NOTE]
    > If you had previously created DMS using the Azure Portal, you cannot reuse it in the migration wizard in Azure Data Studio. Only DMS created previously using Azure Data Studio can be reused.
1. Select the **Resource group** where you have an existing DMS or need to create a new one. The **Azure Database Migration Service** dropdown will list any existing DMS in the selected resource group.
1. To reuse an existing DMS, select it from the dropdown list and the status of the self-hosted integration runtime will be displayed at the bottom of the page.
1. To create a new DMS, select **Create new**. On the **Create Azure Database Migration Service**, screen provide the name for your DMS and select **Create**.
1. After successful creation of DMS, you'll be provided with details to set up **integration runtime**.
1. Select on **Download and install integration runtime** to open the download link in a web browser. Complete the download. Install the integration runtime on a machine that meets the pre-requisites of connecting to source SQL Server and the location containing the source backup.
1. After the installation is complete, the **Microsoft Integration Runtime Configuration Manager** will automatically launch to begin the registration process.
1. Copy and paste one of the authentication keys provided in the wizard screen in Azure Data Studio. If the authentication key is valid, a green check icon is displayed in the Integration Runtime Configuration Manager indicating that you can continue to **Register**.
1. After successfully completing the registration of self-hosted integration runtime, close the **Microsoft Integration Runtime Configuration Manager** and switch back to the migration wizard in Azure Data Studio.
1. Select **Test connection** in the **Create Azure Database Migration Service** screen in Azure Data Studio to validate that the newly created DMS is connected to the newly registered self-hosted integration runtime.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/test-connection-integration-runtime-complete.png" alt-text="Test connection integration runtime":::
1. Review the migration summary and select **Done** to start the database migration.

## Monitor your migration

1. On the **Database Migration Status**, you can track the migrations in progress, migrations completed, and migrations failed (if any).

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/monitor-migration-dashboard.png" alt-text="monitor migration dashboard":::
1. Select **Database migrations in progress** to view ongoing migrations and get further details by selecting the database name.
1. The migration details page displays the backup files and the corresponding status:

    | Status | Description |
    |--------|-------------|
    | Arrived | Backup file arrived in the source backup location and validated |
    | Uploading | Integration runtime is currently uploading the backup file to Azure storage|
    | Uploaded | Backup file is uploaded to Azure storage |
    | Restoring | Azure Database Migration Service is currently restoring the backup file to Azure SQL Managed Instance|
    | Restored | Backup file is successfully restored on Azure SQL Managed Instance |
    | Canceled | Migration process was canceled |
    | Ignored | Backup file was ignored as it doesn't belong to a valid database backup chain |

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/online-to-mi-migration-details-all-backups-restored.png" alt-text="backup restore details":::

## Complete migration cutover

The final step of the tutorial is to complete the migration cutover to ensure the migrated database in Azure SQL Managed Instance is ready for use. This is the only part in the process that requires downtime for applications that connect to the database and hence the timing of the cutover needs to be carefully planned with business or application stakeholders.

To complete the cutover,

1. Stop all incoming transactions to the source database and prepare to make any application configuration changes to point to the target database in Azure SQL Managed Instance.
2. take any tail log backups for the source database in the backup location specified
3. ensure all database backups have the status *Restored* in the monitoring details page
4. select *Complete cutover* in the monitoring details page

During the cutover process, the migration status changes from *in progress* to *completing*. When the cutover process is completed, the migration status changes to *succeeded* to indicate that the database migration is successful and that the migrated database is ready for use.

> [!IMPORTANT]
> After the cutover, availability of SQL Managed Instance with Business Critical service tier only can take significantly longer than General Purpose as three secondary replicas have to be seeded for AlwaysOn High Availability group. This operation duration depends on the size of data, for more information, see [Management operations duration](../azure-sql/managed-instance/management-operations-overview.md#duration).

## Next steps

* For a tutorial showing you how to migrate a database to SQL Managed Instance using the T-SQL RESTORE command, see [Restore a backup to SQL Managed Instance using the restore command](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
* For information about SQL Managed Instance, see [What is SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* For information about connecting apps to SQL Managed Instance, see [Connect applications](../azure-sql/managed-instance/connect-application-instance.md).
