---
title: Azure Database for PostgreSQL - Flexible Server Release notes
description: Release notes of Azure Database for PostgreSQL - Flexible Server.
author: sr-msft
ms.author: srranga
ms.custom: references_regions
ms.service: postgresql
ms.topic: overview
ms.date: 11/30/2021
---

# Release notes - Azure Database for PostgreSQL - Flexible Server

This page provides latest news and updates regarding feature additions, engine versions support, extensions, and any other announcements relevant for Flexible Server - PostgreSQL.



## Release: November 2021

* Azure Database for PostgreSQL is [**Generally Available**](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/azure-database-for-postgresql-flexible-server-is-now-ga/ba-p/2987030)!!
* Support for [latest PostgreSQL minors](./concepts-supported-versions.md) 13.4, 12.8 and 11.13 with new server creates<sup>$</sup>.
* Support for [Geo-redundant backup and restore](concepts-backup-restore.md) feature in preview in selected paired regions - East US 2, Central US, North Europe, West Europe, Japan East, and Japan West.
*  Support for [new regions](overview.md#azure-regions) North Central US, Sweden Central, and West US 3. 
*  Support for [Azure Stream Analytics (ASA) connector in Preview](https://techcommunity.microsoft.com/t5/analytics-on-azure/stream-analytics-updates-ignite-fall-2021-new-outputs-new/ba-p/2919170) to ingest high throughput streaming data to existing table.
*  Several bug fixes, stability, and performance improvements<sup>$</sup>.

<sup>**$**</sup> New servers get these features automatically. In your existing servers, these features are enabled during your server's future maintenance window.

## Release: October 2021

*   Support for [Ddsv4 and Edsv4 SKUs](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/flexible-server-now-supports-v4-compute-series-in-postgresql-on/ba-p/2815092). 
*   Ability to choose local disk for temporary tablespace using `azure.enable_temp_tablespaces_on_local_ssd` server parameter.
*   Server parameters page in Azure portal shows unit of measurement and the PostgreSQL doc link for most parameters.
*   Several bug fixes, stability, and performance improvements.

## Release: September 2021

* Support for [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server).
* Support for [new regions](overview.md#azure-regions) Central India and Japan West.
* Support for non-SSL mode of connectivity using a new `require_secure_transport` server parameter.
* Support for `log_line_prefix` server parameter which adds the string at the beginning of each log line.
* Support for [Azure Resource Health](../../service-health/resource-health-overview.md) for Flexible server health diagnosis and to get support.
* Several bug fixes, stability, and performance improvements.

## Release: July 2021

* Support for [new regions](overview.md#azure-regions) East Asia, Germany West Central, Korea South, South Central US, UK West.
* Support for [pglogical extension](concepts-logical.md) v2.3.2 with PostgreSQL 11,12, and 13.<sup>$</sup>
* PgBouncer now includes `ignore_startup_parameters` to ignore certain client-side driver's parameters including `extra_float_digits`, and `pgbouncer.query_wait_timeout` parameters.  <sup>$</sup>.
* Support for `pg_stat_reset_shared('bgwriter');` and `pg_stat_reset_shared('archiver');` to reset the counters shown in the `pg_stat_bgwriter` and `pg_stat_archiver` views <sup>$</sup>.
* Several bug fixes, stability, and performance improvements<sup>$</sup>.

<sup>**$**</sup> New servers get these features automatically. In your existing servers, these features are enabled during your server's future maintenance window.

## Release: June 2021

* Support for [latest PostgreSQL minors](./concepts-supported-versions.md) 13.3, 12.7 and 11.12 with new server creates<sup>$</sup>.
* Support for [new regions](overview.md#azure-regions), including Australia Southeast, Brazil South, Korea Central, Norway East, South Africa North, Switzerland North, UAE North, and West US.
* Support for [on-demand failover](./concepts-high-availability.md#on-demand-failover) capabilities including forced failover and planned failover for zone redundant high availability deployments.
* Support for [SCRAM authentication](how-to-connect-scram.md) for all major versions with new server creates<sup>$</sup>.
* Support for `pg_prewarm` to be preloaded using `shared_preload_libraries` with new server creates<sup>$</sup>.
* Support for lo extension. See the [extensions page](./concepts-extensions.md) for versions supported with each major version <sup>$</sup>.
* Several bug fixes, stability, and performance improvements<sup>$</sup>.
  
<sup>**$**</sup> New servers get these features automatically. Your existing servers will be automatically upgraded to the latest supported minor version and also new features are enabled during your server's future maintenance window.

## Release: May 2021

* Support for [PostgreSQL major version 13](./concepts-supported-versions.md).
* Support for extensions including pg_partman, pg_cron, and pgaudit. See the [extensions page](./concepts-extensions.md) for versions supported with each major version.
* Several bug fixes, stability, and performance improvements.

## Release: April 2021

* Support for [latest PostgreSQL minors](./concepts-supported-versions.md) 12.6 and 11.11 with new server creates.
* Support for Virtual Network (VNET) [private DNS zone](./concepts-networking.md#private-access-vnet-integration).
* Support to choose the Availability zone during Point-in-time recovery operation.
* Support for new [regions](./overview.md#azure-regions) including Australia East, Canada Central, and France Central.
* Support for [built-in PgBouncer](./concepts-pgbouncer.md) connection pooler. 
<!--- * Support for [pglogical](https://github.com/2ndQuadrant/pglogical) extension version 2.3.2. -->
* [Intelligent performance](concepts-query-store.md) .
* Several bug fixes, stability and performance improvements.

## Release: October 2020 - March 2021

*  Improved experience to [connect](connect-azure-cli.md) to the Flexible server using Azure CLI with the `az postgres flexible- server connect` command.
*  Support for [new regions](overview.md#azure-regions).
*  Several portal improvements, including display of minor versions, summary of metrics on the overview blade.
*  Several bug fixes, stability, and performance improvements.

## Contacts

For any questions or suggestions you might have on Azure Database for PostgreSQL flexible server, send an email to the Azure Database for PostgreSQL Team ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Please note that this email address is not a technical support alias.

In addition, consider the following points of contact as appropriate:

- To contact Azure Support, [file a ticket from the Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- To fix an issue with your account, file a [support request](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) in the Azure portal.
- To provide feedback or to request new features, create an entry via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## Frequently Asked Questions

 Will Flexible Server replace Single Server or Will Single Server be retired soon?

We continue to support Single Server and encourage you to adopt Flexible Server which has richer capabilities such as zone resilient HA, predictable performance, maximum control, custom maintenance window, cost optimization controls and simplified developer experience suitable for your enterprise workloads. If we decide to retire any service, feature, API or SKU, you will receive advance notice including a migration or transition path. Learn more about Microsoft Lifecycle policies [here](/lifecycle/faq/general-lifecycle).
  

## Next steps

Now that you've read an introduction to Azure Database for PostgreSQL flexible server deployment mode, you're ready to create your first server: [Create an Azure Database for PostgreSQL - Flexible Server using Azure portal](./quickstart-create-server-portal.md)
