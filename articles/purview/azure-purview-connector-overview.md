---
title: Azure Purview supported data sources and file types
description: This article provides details about supported data sources, file types, and functionalities in Azure Purview.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 01/24/2022
ms.custom: ignite-fall-2021
---

# Supported data sources and file types

This article discusses currently supported data sources, file types, and scanning concepts in Azure Purview.

## Azure Purview data sources

The table below shows the supported capabilities for each data source. Select the data source, or the feature, to learn more.

|**Category**|  **Data Store**  |**Technical metadata** |**Classification** |**Lineage** | **Access Policy** |
|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| [Yes](register-scan-azure-blob-storage-source.md#register) | [Yes](register-scan-azure-blob-storage-source.md#scan)| Limited* | [Yes](how-to-access-policies-storage.md) |
||    [Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [Yes](register-scan-azure-cosmos-database.md#register) | [Yes](register-scan-azure-cosmos-database.md#scan)|No*|No|
||    [Azure Data Explorer](register-scan-azure-data-explorer.md)| [Yes](register-scan-azure-data-explorer.md#register) | [Yes](register-scan-azure-data-explorer.md#scan)| No* | No |
||    [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [Yes](register-scan-adls-gen1.md#register) | [Yes](register-scan-adls-gen1.md#scan)| Limited* | No |
||    [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [Yes](register-scan-adls-gen2.md#register) | [Yes](register-scan-adls-gen2.md#scan)| Limited* | [Yes](how-to-access-policies-storage.md) |
|| [Azure Database for MySQL](register-scan-azure-mysql-database.md) | [Yes](register-scan-azure-mysql-database.md#register) | [Yes](register-scan-azure-mysql-database.md#scan) | No* | No |
|| [Azure Database for PostgreSQL](register-scan-azure-postgresql.md) | [Yes](register-scan-azure-postgresql.md#register) | [Yes](register-scan-azure-postgresql.md#scan) | No* | No |
||    [Azure Dedicated SQL pool (formerly SQL DW)](register-scan-azure-synapse-analytics.md)| [Yes](register-scan-azure-synapse-analytics.md#register) | [Yes](register-scan-azure-synapse-analytics.md#scan)| No* | No |
||    [Azure Files](register-scan-azure-files-storage-source.md)|[Yes](register-scan-azure-files-storage-source.md#register) | [Yes](register-scan-azure-files-storage-source.md#scan) | Limited* |  No |
||    [Azure SQL Database](register-scan-azure-sql-database.md)| [Yes](register-scan-azure-sql-database.md#register) |[Yes](register-scan-azure-sql-database.md#scan)| No* | No |
||    [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md)|  [Yes](register-scan-azure-sql-database-managed-instance.md#scan) | [Yes](register-scan-azure-sql-database-managed-instance.md#scan) | No* | No |
||    [Azure Synapse Analytics (Workspace)](register-scan-synapse-workspace.md)| [Yes](register-scan-synapse-workspace.md#register) | [Yes](register-scan-synapse-workspace.md#scan)| [Yes - Synapse pipelines](how-to-lineage-azure-synapse-analytics.md)| No|
|Database| [Amazon RDS](register-scan-amazon-rds.md) | [Yes](register-scan-amazon-rds.md#register-an-amazon-rds-data-source) | [Yes](register-scan-amazon-rds.md#scan-an-amazon-rds-database) | No | No |
||    [Cassandra](register-scan-cassandra-source.md)|[Yes](register-scan-cassandra-source.md#register) | No | [Yes](register-scan-cassandra-source.md#lineage)| No|
|| [Db2](register-scan-db2.md) | [Yes](register-scan-db2.md#register) | No | [Yes](register-scan-db2.md#lineage) | No |
||    [Google BigQuery](register-scan-google-bigquery-source.md)| [Yes](register-scan-google-bigquery-source.md#register)| No | [Yes](register-scan-google-bigquery-source.md#lineage)| No|
|| [Hive Metastore Database](register-scan-hive-metastore-source.md) | [Yes](register-scan-hive-metastore-source.md#register) | No | [Yes*](register-scan-hive-metastore-source.md#lineage) | No|
|| [MySQL](register-scan-mysql.md) | [Yes](register-scan-mysql.md#register) | No | [Yes](register-scan-mysql.md#scan) | No |
|| [Oracle](register-scan-oracle-source.md) | [Yes](register-scan-oracle-source.md#register)|  No | [Yes*](register-scan-oracle-source.md#lineage) | No|
|| [PostgreSQL](register-scan-postgresql.md) | [Yes](register-scan-postgresql.md#register) | No | [Yes](register-scan-postgresql.md#lineage) | No |
|| [SAP HANA](register-scan-sap-hana.md) | [Yes](register-scan-sap-hana.md#register) | No | No | No |
|| [Snowflake](register-scan-snowflake.md) | [Yes](register-scan-snowflake.md#register) | No | [Yes](register-scan-snowflake.md#lineage) | No |
||    [SQL Server](register-scan-on-premises-sql-server.md)| [Yes](register-scan-on-premises-sql-server.md#register) |[Yes](register-scan-on-premises-sql-server.md#scan) | No* | No|
||    [Teradata](register-scan-teradata-source.md)| [Yes](register-scan-teradata-source.md#register)|  No | [Yes*](register-scan-teradata-source.md#lineage) | No|
|File|[Amazon S3](register-scan-amazon-s3.md)|[Yes](register-scan-amazon-s3.md)| [Yes](register-scan-amazon-s3.md)| Limited* | No|
|Services and apps|    [Erwin](register-scan-erwin-source.md)| [Yes](register-scan-erwin-source.md#register)| No | [Yes](register-scan-erwin-source.md#lineage)| No|
||    [Looker](register-scan-looker-source.md)| [Yes](register-scan-looker-source.md#register)| No | [Yes](register-scan-looker-source.md#lineage)| No|
||    [Power BI](register-scan-power-bi-tenant.md)| [Yes](register-scan-power-bi-tenant.md#register)| No | [Yes](how-to-lineage-powerbi.md)| No|
|| [Salesforce](register-scan-salesforce.md) | [Yes](register-scan-salesforce.md#register) | No | No | No |
||    [SAP ECC](register-scan-sapecc-source.md)| [Yes](register-scan-sapecc-source.md#register) | No | [Yes*](register-scan-sapecc-source.md#lineage) | No|
|| [SAP S/4HANA](register-scan-saps4hana-source.md) | [Yes](register-scan-saps4hana-source.md#register)| No | [Yes*](register-scan-saps4hana-source.md#lineage) | No|

\* Besides the lineage on assets within the data source, lineage is also supported if dataset is used as a source/sink in [Data Factory](how-to-link-azure-data-factory.md) or [Synapse pipeline](how-to-lineage-azure-synapse-analytics.md).

> [!NOTE]
> Currently, Azure Purview can't scan an asset that has `/`, `\`, or `#` in its name. To scope your scan and avoid scanning assets that have those characters in the asset name, use the example in [Register and scan an Azure SQL Database](register-scan-azure-sql-database.md#creating-the-scan).

## Scan regions
The following is a list of all the Azure data source (data center) regions where the Azure Purview scanner runs. If your Azure data source is in a region outside of this list, the scanner will run in the region of your Azure Purview instance.

### Azure Purview scanner regions

- Australia East
- Australia Southeast
- Brazil South
- Canada Central
- Central India
- Central US
- East Asia
- East US
- East US 2
- France Central
- Japan East
- Korea Central
- North Central US
- North Europe
- South Africa North
- South Central US
- Southeast Asia
- UAE North
- UK South
- West Central US
- West Europe
- West US
- West US 2

## File types supported for scanning

The following file types are supported for scanning, for schema extraction, and classification where applicable:

- Structured file formats supported by extension: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
 > [!Note]
 > * Azure Purview scanner only supports schema extraction for the structured file types listed above.
 > * For AVRO, ORC, and PARQUET file types, Azure Purview scanner does not support schema extraction for files that contain complex data types (for example, MAP, LIST, STRUCT). 
 > * Azure Purview scanner supports scanning snappy compressed PARQUET types for schema extraction and classification. 
 > * For GZIP file types, the GZIP must be mapped to a single csv file within. 
 > Gzip files are subject to System and Custom Classification rules. We currently don't support scanning a gzip file mapped to multiple files within, or any file type other than csv. 
 > * For delimited file types(CSV, PSV, SSV, TSV, TXT), we do not support data type detection. The data type will be listed as "string" for all columns. 
- Document file formats supported by extension: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Azure Purview also supports custom file extensions and custom parsers.

## Nested data

Currently, nested data is only supported for JSON content.

For all [system supported file types](#file-types-supported-for-scanning), if there is nested JSON content in a column, then the scanner parses the nested JSON data and surfaces it within the schema tab of the asset.

Nested data, or nested schema parsing, is not supported in SQL. A column with nested data will be reported and classified as is, and subdata will not be parsed.

## Sampling within a file

In Azure Purview terminology,
- L1 scan: Extracts basic information and meta data like file name, size and fully qualified name
- L2 scan: Extracts schema for structured file types and database tables
- L3 scan: Extracts schema where applicable and subjects the sampled file to system and custom classification rules

For all structured file formats, Azure Purview scanner samples files in the following way:

- For structured file types, it samples the top 128 rows in each column or the first 1 MB, whichever is lower.
- For document file formats, it samples the first 20 MB of each file.
    - If a document file is larger than 20 MB, then it is not subject to a deep scan (subject to classification). In that case, Azure Purview captures only basic meta data like file name and fully qualified name.
- For **tabular data sources(SQL, CosmosDB)**, it samples the top 128 rows.

## Resource set file sampling

A folder or group of partition files is detected as a *resource set* in Azure Purview, if it matches with a system resource set policy or a customer defined resource set policy. If a resource set is detected, then Azure Purview will sample each folder that it contains. Learn more about resource sets [here](concept-resource-sets.md).

File sampling for resource sets by file types:

- **Delimited files (CSV, PSV, SSV, TSV)** - 1 in 100 files are sampled (L3 scan) within a folder or group of partition files that are considered a 'Resource set'
- **Data Lake file types (Parquet, Avro, Orc)** - 1 in 18446744073709551615 (long max) files are sampled (L3 scan) within a folder or group of partition files that are considered a *resource set*
- **Other structured file types (JSON, XML, TXT)** - 1 in 100 files are sampled (L3 scan) within a folder or group of partition files that are considered a 'Resource set'
- **SQL objects and CosmosDB entities** - Each file is L3 scanned.
- **Document file types** - Each file is L3 scanned. Resource set patterns don't apply to these file types.

## Classification

All 206 system classification rules apply to structured file formats. Only the MCE classification rules apply to document file types (Not the data scan native regex patterns, bloom filter-based detection). For more information on supported classifications, see [Supported classifications in Azure Purview](supported-classifications.md).

## Next steps

- [Register and scan Azure Blob storage source](register-scan-azure-blob-storage-source.md)
- [Scans and ingestion in Azure Purview](concept-scans-and-ingestion.md)
- [Manage data sources in Azure Purview](manage-data-sources.md)
