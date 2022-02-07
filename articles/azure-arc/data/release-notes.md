---
title: Azure Arc-enabled data services - Release notes
description: Latest release notes
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 01/27/2022
ms.topic: conceptual
ms.custom: references_regions
# Customer intent: As a data professional, I want to understand why my solutions would benefit from running with Azure Arc-enabled data services so that I can leverage the capability of the feature.
---

# Release notes - Azure Arc-enabled data services

This article highlights capabilities, features, and enhancements recently released or improved for Azure Arc-enabled data services.

## January 2022

This release is published January 27, 2022.

### Data controller

- Initiate an upgrade of the data controller from the portal in the direct connected mode
- Removed block on data controller upgrade if there are Azure Arc-enabled SQL Managed Instance business critical instances that exist
- Better handling of delete user experiences in Azure portal

### SQL Managed Instance

- Azure Arc-enabled SQL Managed Instance business critical instances can be upgraded from the January release and going forward (preview)
- Business critical distributed availability group failover can now be done through a Kubernetes-native experience or the Azure CLI (indirect mode only) (preview)
- Added support for `LicenseType: DisasterRecovery` which will ensure that instances which are used for business critical distributed availability group secondary replicas:
    - Are not billed for
    - Automatically seed the system databases from the primary replica when the distributed availability group is created. (preview)
- New option added to `desiredVersion` called `auto` - automatically upgrades a given SQL instance when there is a new upgrade available (preview)
- Update the configuration of SQL instances using Azure CLI in the direct connected mode

## December 2021

This release is published December 16, 2021.

### Data controller

- Secret rotation for metrics and logs dashboards using Azure CLI or Kubernetes .yaml file
- Ability to provide custom SSL certificates for metrics and logs dashboards using Azure CLI or Kubernetes yaml file
- Direct mode upgrade of data controller via Azure CLI

### SQL Managed Instance

- Active Directory authentication in preview for SQL Managed Instance
- Direct mode upgrade of SQL Managed Instance via Azure CLI
- Edit memory and CPU configuration in Azure portal in directly connected mode
- Ability to specify a single replica for a business critical instance using Azure CLI or Kubernetes yaml file
- Updated SQL binaries to latest Azure PaaS-compatible binary version
- Resolved issue where the point in time restore did not respect the configured time zone

## November 2021

This release is published November 3, 2021

### Tools

#### Azure Data Studio

Install or update to the latest version of [Arc extension for Azure Data Studio](/sql/azure-data-studio/extensions/azure-arc-extension).

#### Azure (`az`) CLI

Install or update `arcdata` extension for `az` CLI to support directly connected deployment.

The following `sql` commands now support directly connected mode:

   ```console
   az arcdata dc create
   az arcdata dc delete
   az sql mi-arc create
   az sql mi-arc delete
   ```
 
### Data controller

- Directly connected mode generally available
- Directly connected Azure Arc Data controller extensions on Azure Arc enabled Kubernetes clusters now use system generated managed identities instead of service principal name. The managed identity is automatically created when a new Azure Arc data controller extension is created. You still need to grant appropriate permissions to upload usage and metrics.
- Metrics upload leverages the system generated managed identity with a directly connected Azure Arc data controller. 
- Create directly connected mode Azure Arc data controller from Azure CLI (`az`).
- Automatically upload metrics to Azure Monitor
- Automatically upload logs to Azure Log Analytics
- Enable or disable automatic upload of Metrics and/or logs to Azure after deployment of Azure Arc data controller.
- Upgrade from July 2021 release in-place (only for generally available services such as Azure Arc data controller and general purpose SQL Managed Instance) using Azure CLI.
- Set the metrics and logs dashboards usernames and passwords separately at DC deployment time using the new environment variables:

   ```console
   AZDATA_LOGSUI_USERNAME
   AZDATA_LOGSUI_PASSWORD
   AZDATA_METRICSUI_USERNAME
   AZDATA_METRICSUI_PASSWORD
   ```
- New command - `az arcdata dc list-upgrades` shows the list of available upgrades from the currently deployed data controller.


You can continue to use `AZDATA_USERNAME` and `AZDATA_PASSWORD` environment variables as before. If you only provide `AZDATA_USERNAME` and `AZDATA_PASSWORD` then the deployment uses them for both the logs and metrics dashboards.

### Region availability

This release introduces directly connected mode availability in the following Azure regions:

- North Central US
- West US
- West US 3

For complete list, see [Supported regions](overview.md#supported-regions).

### Azure Arc-enabled SQL Managed Instance

- Upgrade instances of Azure Arc-enabled SQL Managed Instance general purpose in-place
- The SQL binaries are updated to a new version
- Direct connected mode deployment of Azure Arc enabled SQL Managed Instance using Azure CLI
- Point in time restore for Azure Arc enabled SQL Managed Instance is being made generally available with this release. Currently point in time restore is only supported for the general purpose SQL Managed Instance. Point in time restore for business critical SQL Managed Instance is still under preview.
- New `--dry-run` option provided for point in time restore
- Recovery point objective is set to 5 minutes by default and is not configurable
- Backup retention period is set to 7 days by default. A new option to set the retention period to zero disables automatic backups for development and test instances that do not require backups
- Resolved issue where the point in time restore operation did not respect configured time zone 
- Restore to a point in time from Azure CLI or Azure Data Studio
 

### Known issues

#### Data controller upgrade

- At this time, upgrade of a directly connected data controller via CLI or the portal is not supported.
- You can only upgrade generally available services such as Azure Arc data controller and general purpose SQL Managed Instance at this time. If you also have business critical SQL Managed Instance and/or Azure Arc enabled PostgreSQL Hyperscale, remove them first, before proceeding to upgrade.

#### Commands

The following commands do not support directly connected mode at this time:

```console
az arcdata dc update
az arcdata sql mi-arc update
```

#### Azure Arc-enabled PostgreSQL Hyperscale

- Backup and restore of Azure Arc-enabled PostgreSQL Hyperscale server is not supported in the current preview release.

- It is not possible to enable and configure the `pg_cron` extension at the same time. You need to use two commands for this. One command to enable it and one command to configure it. For example:

   1. Enable the extension:

      ```console
      az postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. Restart the server group.

   1. Configure the extension:

      ```console
      az postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   If you execute the second command before the restart has completed it will fail. If that is the case, simply wait for a few more moments and execute the second command again.

- Passing an invalid value to the `--extensions` parameter when editing the configuration of a server group to enable additional extensions incorrectly resets the list of enabled extensions to what it was at the create time of the server group and prevents user from creating additional extensions. The only workaround available when that happens is to delete the server group and redeploy it.

#### Azure Arc-enabled SQL Managed Instance

- When a pod is re-provisioned, SQL Managed Instance starts a new set of full backups for all databases.
- If your data controller is directly connected, before you can provision a SQL Managed Instance, you must upgrade your data controller to the most recent version first. Attempting to provision a SQL Managed Instance with a data controller imageVersion of `v1.0.0_2021-07-30` will not succeed.


##### Other limitations

- Transaction replication is currently not supported.
- Log shipping is currently blocked.
- Only SQL Server Authentication is supported.

## July 2021

This release is published July 30, 2021.

This release announces general availability for Azure Arc-enabled SQL Managed Instance [general purpose service tier](service-tiers.md) in indirectly connected mode.

   > [!NOTE]
   > In addition, this release provides the following Azure Arc-enabled services in preview: 
   > - SQL Managed Instance in directly connected mode
   > - SQL Managed Instance [business critical service tier](service-tiers.md)
   > - PostgreSQL Hyperscale

### Breaking changes

#### Tools

Use the following tools:
- [Insiders build of Azure Data Studio](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-main).
- [`arcdata` extension for Azure (`az`) CLI](install-arcdata-extension.md). 


#### Data controller

- `az arcdata dc create` parameter named `--azure-subscription` has been changed to use the standard `--subscription` parameter.
- Deployment on AKS HCI requires a special storage class configuration. See details under [Configure storage (Azure Stack HCI with AKS-HCI)](create-data-controller-indirect-cli.md#configure-storage-azure-stack-hci-with-aks-hci).
- There is a new requirement to allow non-SSL connections when exporting data. Set an environment variable to suppress the interactive prompt.

### What's new

#### Data controller

- Directly connected mode is in preview. 

- Directly connected mode (preview) is only available in the following Azure regions for this release:

   - North Central US *
   - Central US
   - East US
   - East US 2
   - West US *
   - West US 2
   - West US 3 *
   - UK South
   - West Europe
   - North Europe
   - Australia East
   - Southeast Asia
   - Korea Central
   - France Central
    \* Newly added for November, 2021.

- Currently, additional basic authentication users can be added to Grafana using the Grafana administrative experience. Customizing Grafana by modifying the Grafana .ini files is not supported.

- Currently, modifying the configuration of ElasticSearch and Kibana is not supported beyond what is available through the Kibana administrative experience. Only basic authentication with a single user is supported.
	
- Custom metrics in Azure portal is in preview.

- Exporting usage/billing information, metrics, and logs using the command `az arcdata dc export` requires bypassing SSL verification for now.  You will be prompted to bypass SSL verification or you can set the `AZDATA_VERIFY_SSL=no` environment variable to avoid prompting.  There is no way to configure an SSL certificate for the data controller export API currently.

#### Azure Arc-enabled SQL Managed Instance

- Automated backup and point-in-time restore is in preview.
- Supports point-in-time restore from an existing database in an Azure Arc-enabled SQL Managed Instance to a new database within the same instance.
- If the current datetime is given as point-in-time in UTC format, it resolves to the latest valid restore time and restores the given database until last valid transaction.
- A database can be restored to any point-in-time where the transactions took place.
- To set a specific recovery point objective for an Azure Arc-enabled SQL Managed Instance, edit the SQL Managed Instance CRD to set the `recoveryPointObjectiveInSeconds` property. Supported values are from 300 to 600.
- To disable the automated backups, edit the SQL instance CRD and set the `recoveryPointObjectiveInSeconds` property to 0.

### Known issues

#### Platform

- You can create a data controller, SQL Managed Instance, or PostgreSQL Hyperscale server group on a directly connected mode cluster with the Azure portal. Directly connected mode deployment is not supported with other Azure Arc-enabled data services tools. Specifically, you can't deploy a data controller in directly connect mode with any of the following tools during this release.
   - Azure Data Studio
   - Kubernetes native tools (`kubectl`)
   - The `arcdata` extension for the Azure CLI (`az`)

   [Create Azure Arc data controller in Direct connectivity mode from Azure portal](create-data-controller-direct-azure-portal.md) explains how to create the data controller in the portal.

- You can still use `kubectl` to create resources directly on a Kubernetes cluster, however they will not be reflected in the Azure portal if you are using direct connected mode.

- In directly connected mode, upload of usage, metrics, and logs using `az arcdata dc upload` is blocked by design. Usage is automatically uploaded. Upload for data controller created in indirect connected mode should continue to work.
- Automatic upload of usage data in direct connectivity mode will not succeed if using proxy via `–proxy-cert <path-t-cert-file>`.
- Azure Arc-enabled SQL Managed instance and Azure Arc-enabled PostgreSQL Hyperscale are not GB18030 certified.
- Currently, only one Azure Arc data controller per Kubernetes cluster is supported.

#### Data controller

- When Azure Arc data controller is deleted from Azure portal, validation is done to block the delete if there any Azure Arc-enabled SQL Managed Instances deployed on this Arc data controller. Currently, this validation is applied only when the delete is performed from the Overview page of the Azure Arc data controller. 

#### Azure Arc-enabled PostgreSQL Hyperscale

- At this time, PosgreSQL Hyperscale can't be used on Kubernetes version 1.22 and higher. 
- Backup and restore operations no longer work in the July 30 release. This is a temporary limitation. Use the June 2021 release for now if you need to do to back up or restore. This will be fixed in a future release.

- It is not possible to enable and configure the `pg_cron` extension at the same time. You need to use two commands for this. One command to enable it and one command to configure it. For example:

   1. Enable the extension:

      ```console
      azdata postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. Restart the server group.

   1. Configure the extension:

      ```console
      azdata postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   If you execute the second command before the restart has completed it will fail. If that is the case, simply wait for a few more moments and execute the second command again.

- Passing an invalid value to the `--extensions` parameter when editing the configuration of a server group to enable additional extensions incorrectly resets the list of enabled extensions to what it was at the create time of the server group and prevents user from creating additional extensions. The only workaround available when that happens is to delete the server group and redeploy it.

- Point in time restore is not supported for now on NFS storage.

#### Azure Arc-enabled SQL Managed Instance

##### Can't see resources in portal

- Portal does not show Azure Arc-enabled SQL Managed Instance resources created in the June release. Delete the SQL Managed Instance resources from the resource group list view. You may need to delete the custom location resource first.

##### Point-in-time restore(PITR) supportability and limitations:
	
- Doesn't support restore from one Azure Arc-enabled SQL Managed Instance to another Azure Arc-enabled SQL Managed Instance.  The database can only be restored to the same Azure Arc-enabled SQL Managed Instance where the backups were created.
- Renaming of a databases is currently not supported, for point in time restore purposes.
- Currently there is no CLI command or an API to provide the allowed time window information for point-in-time restore. You can provide a time within a reasonable window, since the time the database was created, and if the timestamp is valid the restore would work. If the timestamp is not valid, the allowed time window will be provided via an error message.
- No support for restoring a TDE enabled database.
- A deleted database cannot be restored currently.

#####	Automated backups

- Renaming database will stop the automated backups for this database.
- No retention enforced. Will preserve all backups as long as there's available space. 
- User databases with SIMPLE recovery model are not backed up.
- System database `model` is not backed up in order to prevent interference with creation/deletion of database. The DB gets locked when admin operations are performed. 
- Currently only `master` and `msdb` system databases are backed up. Only full backups are performed every 12 hours.
- Only `ONLINE` user databases are backup up.
- Default recovery point objective (RPO): 5 minutes. Can not be modified in current release.
- Backups are retained indefinitely. To recover space, manually delete backups.

##### Other limitations
- Transaction replication is currently not supported.
- Log shipping is currently blocked.
- Only SQL Server Authentication is supported.

## June 2021

This preview release is published July 13, 2021.

### Breaking changes

#### New deployment templates

- Kubernetes native deployment templates have been modified for for data controller, bootstrapper, & SQL Managed Instance. Update your .yaml templates. [Sample yaml files](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml)

#### New Azure CLI extension for data controller and Azure Arc-enabled SQL Managed Instance

This release introduces the `arcdata` extension to the Azure CLI. To add the extension, run the following command:

```azurecli
az extension add --name arcdata
```

The extension supports command-line interaction with data controller and SQL Managed Instance and PostgreSQL Hyperscale resources.

To update your scripts for data controller, replace `azdata arc dc...` with `az arcdata dc...`.

To update your scripts for managed instance, replace `azdata arc sql mi...` with `az sql mi-arc...`.

For Azure Arc-enabled PostgreSQL Hyperscale, replace `azdata arc sql postgres...` with `az postgres arc-server...`.

In addition to the parameters that have historically existed on the `azdata` commands, the same commands in the `arcdata` Azure CLI extension have some new parameters such as `--k8s-namespace` and `--use-k8s` are now required. The `--use-k8s` parameter will be used to differentiate when the command should be sent to the Kubernetes API or to the ARM API. For now all Azure CLI commands for Azure Arc-enabled data services target only the Kubernetes API.

Some of the short forms of the parameter names (e.g. `--core-limit` as `-cl`) have either been removed or changed. Use the new parameter short names or the long name.

The `azdata arc dc export` command is no longer functional. Use `az arcdata dc export` instead.

#### Required property: `infrastructure`

The `infrastructure` property is a new required property when deploying a data controller. Adjust your yaml files, azdata/az scripts, and ARM templates to account for specifying this property value. Allowed values are `alibaba`, `aws`, `azure`, `gpc`, `onpremises`, `other`.

#### Kibana login

The OpenDistro security pack has been removed. Log in to Kibana is now done through a generic browser username/password prompt. More information will be provided later how to configure additional authentication/authorization options.

#### CRD version bump to `v1beta1`

All CRDs have had the version bumped from `v1alpha1` to `v1beta1` for this release. Be sure to delete all CRDs as part of the uninstall process if you have deployed a version of Azure Arc-enabled data services prior to the June 2021 release. The new CRDs deployed with the June 2021 release will have v1beta1 as the version.

#### Azure Arc-enabled SQL Managed Instance

Automated backup service is available and on by default. Keep a close watch on space availability on the backup volume.

### What's new

This release introduces `az` CLI extensions for Azure Arc-enabled data services. See information in [Breaking change](#breaking-change) above.

#### Platform

#### Data controller

- Streamlined user experience for deploying a data controller in the direct connected mode from the Azure portal. Once a Kubernetes cluster has been Azure Arc-enabled, you can deploy the data controller entirely from the portal with the Arc data controller create wizard in one motion. This deployment also creates the custom location and Azure Arc-enabled data services extension (bootstrapper). You can also pre-create the custom location and/or extension and configure the data controller deployment to use them.
- New `Infrastructure` property is a required property when you deploy an Arc data controller. This property will be required for billing purposes. More information will be provided at general availability.
- Various usability improvements in the data controller user experience in the Azure portal including the ability to better see the deployment status of resources that are in the deployment process on the Kubernetes cluster.
- Data controller automatically uploads logs (optionally) and now also metrics to Azure in direct connected mode.
- The monitoring stack (metrics and logs databases/dashboards) has now been packaged into its own custom resource definition (CRD) - `monitors.arcdata.microsoft.com`. When this custom resource is created the monitoring stack pods are created. When it is deleted the monitoring stack pods are deleted. When the data controller is created the monitor custom resource is automatically created.
- New regions supported for direct connected mode (preview): East US 2, West US 2, South Central US, UK South, France Central, Southeast Asia, Australia East.
- The custom location resource chart on the overview blade now shows Azure Arc-enabled data services resources that are deployed to it.
- Diagnostics and solutions have been added to the Azure portal for data controller.
- Added new `Observed Generation` property to all Arc related custom resources.
- Credential manager service is now included and handles the automated distribution of certificates to all services managed by the data controller.

#### Azure Arc-enabled PostgreSQL Hyperscale

- Azure Arc PostgreSQL Hyperscale now supports NFS storage.
- Azure Arc PostgreSQL Hyperscale deployments now supports Kubernetes pods to nodes assignments strategies with nodeSelector, nodeAffinity and anti-affinity.
- You can now configure compute parameters (vCore & memory) per role (Coordinator or Worker) when you deploy a PostgreSQL Hyperscale server group or after deployment from Azure Data Studio and from the Azure portal.
- From the Azure portal, you can now view the list of PostgreSQL extensions created on your PostgreSQL Hyperscale server group.
- From the Azure portal, you can delete Azure Arc-enabled PostgreSQL Hyperscale groups on a data controller that is directly connected to Azure.


#### Azure Arc-enabled SQL Managed Instance

- Automated backups are now enabled.
- You can now restore a database backup as a new database on the same SQL instance by creating a new custom resource based on the `sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com` custom resource definition (CRD). See documentation for details. There is no command-line interface (`azdata` or `az`), Azure portal, or Azure Data Studio experience for restoring a database yet.
- The version of SQL engine binaries included in this release is aligned to the latest binaries that are deployed globally in Azure SQL Managed Instance (PaaS in Azure). This alignment enables backup/restore back and forth between Azure SQL Managed Instance PaaS and Azure Arc-enabled Azure SQL Managed Instance. More details on the compatibility will be provided later.
- You can now delete Azure Arc SQL Managed Instances from the Azure portal in direct connected mode.
- You can now configure a SQL Managed Instance to have a pricing tier (`GeneralPurpose`, `BusinessCritical`), license type (`LicenseIncluded`, `BasePrice` (used for AHB pricing), and `developer`. There will be no charges incurred for using Azure Arc-enabled SQL Managed Instance until the General Availability date (publicly announced as scheduled for July 30, 2021) and until you upgrade to the General Availability version of the service.
- The `arcdata` extension for Azure Data Studio now has additional parameters that can be configured for deploying and editing SQL Managed Instances: enable/disable agent, admin login secret, annotations, labels, service annotations, service labels, SSL/TLS configuration settings, collation, language, and trace flags.
- New commands in `azdata`/custom resource tasks for setting up distributed availability groups. These commands are in early stages of preview, documentation will be provided soon.

   > [!NOTE]
   > These commands will migrate to the `az arcdata` extension.

- `azdata arc dc export` is deprecated. It is replaced by `az arcdata dc export` in the `arcdata` extension for the Azure CLI (`az`). It uses a different approach to export the data out. It does not connect directly to the data controller API anymore. Instead it creates an export task based on the `exporttasks.tasks.arcdata.microsoft.com` custom resource definition (CRD). The export task custom resource that is created drives a workflow to generate a downloadable package. The Azure CLI waits for the completion of this task and then retrieves the secure URL from the task custom resource status to download the package.
- Support for using NFS-based storage classes.
- Diagnostics and solutions have been added to the Azure portal for Arc SQL Managed Instance

## May 2021

This preview release is published on June 2, 2021.

As a preview feature, the technology presented in this article is subject to [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### Breaking change

- Kubernetes native deployment templates have been modified. Update update your .yml templates.
    - Updated templates for data controller, bootstrapper, & SQL Managed instance: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)
    - Updated templates for PostgreSQL Hyperscale: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)

### What's new

#### Platform

- Create and delete data controller, SQL Managed Instance, and PostgreSQL Hyperscale server groups from Azure portal.
- Validate portal actions when deleting Azure Arc data services. For instance, the portal alerts when you attempt to delete the data controller when there are SQL Managed Instances deployed using the data controller.
- Create custom configuration profiles to support custom settings when you deploy Azure Arc-enabled data controller using the Azure portal.
- Optionally, automatically upload your logs to Azure Log analytics workspace in the directly connected mode.

#### Azure Arc-enabled PostgreSQL Hyperscale

This release introduces the following features or capabilities:

- Delete an Azure Arc PostgreSQL Hyperscale from the Azure portal when its Data Controller was configured for Direct connectivity mode.
- Deploy Azure Arc-enabled PostgreSQL Hyperscale from the Azure database for Postgres deployment page in the Azure portal. See [Select Azure Database for PostgreSQL deployment option - Microsoft Azure](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer).
- Specify storage classes and PostgreSQL extensions when deploying Azure Arc-enabled PostgreSQL Hyperscale from the Azure portal.
- Reduce the number of worker nodes in your Azure Arc-enabled PostgreSQL Hyperscale. You can do this operation (known as scale in as opposed to scale out when you increase the number of worker nodes) from `azdata` command-line.

#### Azure Arc-enabled SQL Managed Instance

- New [Azure CLI extension](/cli/azure/azure-cli-extensions-overview) for Azure Arc-enabled SQL Managed Instance has the same commands as `az sql mi-arc <command>`. All Azure Arc-enabled SQL Managed Instance commands are located at `az sql mi-arc`. All Arc related `azdata` commands will be deprecated and moved to Azure CLI in a future release.

   To add the extension:

   ```azurecli
   az extension add --source https://azurearcdatacli.blob.core.windows.net/cli-extensions/arcdata-0.0.1-py2.py3-none-any.whl -y
   az sql mi-arc --help
   ```

- Manually trigger a failover of using Transact-SQL. Do the following commands in order:

   1. On the primary replica endpoint connection:

      ```sql
       ALTER AVAILABILITY GROUP current SET (ROLE = SECONDARY);
      ```

   1. On the secondary replica endpoint connection:

      ```sql
      ALTER AVAILABILITY GROUP current SET (ROLE = PRIMARY);
      ```

- Transact-SQL `BACKUP` command is blocked unless using `COPY_ONLY` setting. This supports point in time restore capability.

## April 2021

This preview release is published on April 29, 2021.

### What's new

This section describes the new features introduced or enabled for this release.

#### Platform

- Direct connected clusters automatically upload telemetry information automatically Azure.

#### 	Azure Arc-enabled PostgreSQL Hyperscale

- Azure Arc-enabled PostgreSQL Hyperscale is now supported in Direct connect mode. You now can deploy Azure Arc-enabled PostgreSQL Hyperscale from the Azure Market Place in the Azure portal.
- Azure Arc-enabled PostgreSQL Hyperscale ships with the Citus 10.0 extension which features columnar table storage
- Azure Arc-enabled PostgreSQL Hyperscale  now supports full user/role management.
- Azure Arc-enabled PostgreSQL Hyperscale  now supports additional extensions with `Tdigest` and  `pg_partman`.
- Azure Arc-enabled PostgreSQL Hyperscale  now supports configuring vCore and memory settings per role of the PostgreSQL instance in the server group.
- Azure Arc-enabled PostgreSQL Hyperscale  now supports configuring database engine/server settings per role of the PostgreSQL instance in the server group.

#### Azure Arc-enabled SQL Managed Instance

- Restore a database to SQL Managed Instance with three replicas and it will be automatically added to the availability group.
- Connect to a secondary read-only endpoint on SQL Managed Instances deployed with three replicas. Use `azdata arc sql endpoint list` to see the secondary read-only connection endpoint.

## March 2021

The March 2021 release was initially introduced on April 5th 2021, and the final stages of release were completed April 9th 2021.

Azure Data CLI (`azdata`) version number: 20.3.2. You can install `azdata` from [Install Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

### Data controller

- Deploy Azure Arc-enabled data services data controller in direct connect mode from the portal. Start from [Deploy data controller - direct connect mode - prerequisites](create-data-controller-direct-prerequisites.md).

### Azure Arc-enabled PostgreSQL Hyperscale

Both custom resource definitions (CRD) for PostgreSQL have been consolidated into a single CRD. See the following table.

|Release |CRD |
|-----|-----|
|February 2021 and prior| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|Beginning March 2021 | postgresqls.arcdata.microsoft.com

You will delete the previous CRDs as you cleanup past installations. See [Cleanup from past installations](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations).

### Azure Arc-enabled SQL Managed Instance

- You can now create a SQL Managed Instance from the Azure portal in the direct connected mode.

- You can now restore a database to SQL Managed Instance with three replicas and it will be automatically added to the availability group.

- You can now connect to a secondary read-only endpoint on SQL Managed Instances deployed with three replicas. Use `azdata arc sql endpoint list` to see the secondary read-only connection endpoint.

## February 2021

### New capabilities and features

Azure Data CLI (`azdata`) version number: 20.3.1. You can install `azdata` from [Install Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Additional updates include:

- Azure Arc-enabled SQL Managed Instance
   - High availability with Always On availability groups

- Azure Arc-enabled PostgreSQL Hyperscale
   Azure Data Studio:
   - The overview page shows the status of the server group itemized per node
   - A new properties page shows more details about the server group
   - Configure Postgres engine parameters from **Node Parameters** page

## January 2021

### New capabilities and features

Azure Data CLI (`azdata`) version number: 20.3.0. You can install `azdata` from [Install Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Additional updates include:
- Localized portal available for 17 new languages
- Minor changes to Kube-native .yaml files
- New versions of Grafana and Kibana
- Issues with Python environments when using azdata in notebooks in Azure Data Studio resolved
- The pg_audit extension is now available for PostgreSQL Hyperscale
- A backup ID is no longer required when doing a full restore of a PostgreSQL Hyperscale database
- The status (health state) is reported for each of the PostgreSQL instances that constitute a sever group

   In earlier releases, the status was aggregated at the server group level and not itemized at the PostgreSQL node level.

- PostgreSQL deployments honor the volume size parameters indicated in create commands
- The engine version parameters are now honored when editing a server group
- The naming convention of the pods for Azure Arc-enabled PostgreSQL Hyperscale has changed

    It is now in the form: `ServergroupName{c, w}-n`. For example, a server group with three nodes, one coordinator node and two worker nodes is represented as:
   - `Postgres01c-0` (coordinator node)
   - `Postgres01w-0` (worker node)
   - `Postgres01w-1` (worker node)

## December 2020

### New capabilities & features

Azure Data CLI (`azdata`) version number: 20.2.5. You can install `azdata` from [Install Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

View endpoints for SQL Managed Instance and PostgreSQL Hyperscale using the Azure Data CLI (`azdata`) with `azdata arc sql endpoint list` and `azdata arc postgres endpoint list` commands.

Edit SQL Managed Instance resource (CPU core and memory) requests and limits using Azure Data Studio.

Azure Arc-enabled PostgreSQL Hyperscale now supports point in time restore in addition to full backup restore for both versions 11 and 12 of PostgreSQL. The point in time restore capability allows you to indicate a specific date and time to restore to.

The naming convention of the pods for Azure Arc-enabled PostgreSQL Hyperscale has changed. It is now in the form: ServergroupName{r, s}-_n_. For example, a server group with three nodes, one coordinator node and two worker nodes is represented as:
- `postgres02r-0` (coordinator node)
- `postgres02s-0` (worker node)
- `postgres02s-1` (worker node)

### Breaking change

#### New resource provider

This release introduces an updated [resource provider](../../azure-resource-manager/management/azure-services-resource-providers.md) called `Microsoft.AzureArcData`. Before you can use this feature, you need to register this resource provider.

To register this resource provider:

1. In the Azure portal, select **Subscriptions**
2. Choose your subscription
3. Under **Settings**, select **Resource providers**
4. Search for `Microsoft.AzureArcData` and select **Register**

You can review detailed steps at [Azure resource providers and types](../../azure-resource-manager/management/resource-providers-and-types.md). This change also removes all the existing Azure resources that you have uploaded to the Azure portal. In order to use the resource provider, you need to update the data controller and use the latest `azdata` CLI.

### Platform release notes

#### Direct connectivity mode

This release introduces direct connectivity mode. Direct connectivity mode enables the data controller to automatically upload the usage information to Azure. As part of the usage upload, the Arc data controller resource is automatically created in the portal, if it is not already created via `azdata` upload.

You can specify direct connectivity when you create the data controller. The following example creates a data controller with `az arcdata dc create` named `arc` using direct connectivity mode (`connectivity-mode direct`). Before you run the example, replace `<subscription id>` with your subscription ID.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## October 2020

Azure Data CLI (`azdata`) version number: 20.2.3. You can install `azdata` from [Install Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

### Breaking changes

This release introduces the following breaking changes:

* In the PostgreSQL custom resource definition (CRD), the term `shards` is renamed to `workers`. This term (`workers`) matches the command-line parameter name.

* `azdata arc postgres server delete` prompts for confirmation before deleting a postgres instance. Use `--force` to skip prompt.

### Additional changes

* A new optional parameter was added to `azdata arc postgres server create` called `--volume-claim mounts`. The value is a comma-separated list of volume claim mounts. A volume claim mount is a pair of volume type and PVC name. The only volume type currently supported is `backup`. In PostgreSQL, when volume type is `backup`, the PVC is mounted to `/mnt/db-backups`. This enables sharing backups between PostgresSQL instances so that the backup of one PostgresSQL instance can be restored in another instance.

* New short names for PostgresSQL custom resource definitions:
  * `pg11`
  * `pg12`
* Telemetry upload provides user with either:
   * Number of points uploaded to Azure
     or
   * If no data has been loaded to Azure, a prompt to try it again.
* `az arcdata dc debug copy-logs` now also reads from `/var/opt/controller/log` folder and collects PostgreSQL engine logs on Linux.
*	Display a working indicator during creating and restoring backup with PostgreSQL Hyperscale.
* `azdata arc postrgres backup list` now includes backup size information.
* SQL Managed Instance admin name property was added to right column of overview blade in the Azure portal.
* Azure Data Studio supports configuring number of worker nodes, vCore, and memory settings for PostgreSQL Hyperscale.
* Preview supports backup/restore for Postgres version 11 and 12.

## September 2020

Azure Arc-enabled data services is released for public preview. Azure Arc-enabled data services allow you to manage data services anywhere.

- SQL Managed Instance
- PostgreSQL Hyperscale

For instructions see [What are Azure Arc-enabled data services?](overview.md)

## Next steps

> **Just want to try things out?**
> Get started quickly with [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) on AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) or in an Azure VM.

- [Install the client tools](install-client-tools.md)
- [Plan an Azure Arc-enabled data services deployment](plan-azure-arc-data-services.md) (requires installing the client tools first)
- [Create an Azure SQL Managed Instance on Azure Arc](create-sql-managed-instance.md) (requires creation of an Azure Arc data controller first)
- [Create an Azure Database for PostgreSQL Hyperscale server group on Azure Arc](create-postgresql-hyperscale-server-group.md) (requires creation of an Azure Arc data controller first)
- [Resource providers for Azure services](../../azure-resource-manager/management/azure-services-resource-providers.md)
