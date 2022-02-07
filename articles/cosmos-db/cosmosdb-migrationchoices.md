---
title: Cosmos DB Migration options
description: This doc describes the various options to migrate your on-premises or cloud data to Azure Cosmos DB
author: rothja
ms.author: jroth
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/03/2021

---
# Options to migrate your on-premises or cloud data to Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

You can load data from various data sources to Azure Cosmos DB. Since Azure Cosmos DB supports multiple APIs, the targets can be any of the existing APIs. The following are some scenarios where you migrate data to Azure Cosmos DB:

* Move data from one Azure Cosmos container to another container in the same database or a different databases.
* Moving data between dedicated containers to shared database containers.
* Move data from an Azure Cosmos account located in region1 to another Azure Cosmos account in the same or a different region.
* Move data from a source such as Azure blob storage, a JSON file, Oracle database, Couchbase, DynamoDB to Azure Cosmos DB.

In order to support migration paths from the various sources to the different Azure Cosmos DB APIs, there are multiple solutions that provide specialized handling for each migration path. This document lists the available solutions and describes their advantages and limitations.

## Factors affecting the choice of migration tool

The following factors determine the choice of the migration tool:

* **Online vs offline migration**: Many migration tools provide a path to do a one-time migration only. This means that the applications accessing the database might experience a period of downtime. Some migration solutions provide a way to do a live migration where there is a replication pipeline set up between the source and the target.

* **Data source**: The existing data can be in various data sources like Oracle DB2, Datastax Cassanda, Azure SQL Database, PostgreSQL, etc. The data can also be in an existing Azure Cosmos DB account and the intent of migration can be to change the data model or repartition the data in a container with a different partition key.

* **Azure Cosmos DB API**: For the SQL API in Azure Cosmos DB, there are a variety of tools developed by the Azure Cosmos DB team which aid in the different migration scenarios. All of the other APIs have their own specialized set of tools developed and maintained by the community. Since Azure Cosmos DB supports these APIs at a wire protocol level, these tools should work as-is while migrating data into Azure Cosmos DB too. However, they might require custom handling for throttles as this concept is specific to Azure Cosmos DB.

* **Size of data**: Most migration tools work very well for smaller datasets. When the data set exceeds a few hundred gigabytes, the choices of migration tools are limited. 

* **Expected migration duration**: Migrations can be configured to take place at a slow, incremental pace that consumes less throughput or can consume the entire throughput provisioned on the target Azure Cosmos DB container and complete the migration in less time.

## Azure Cosmos DB SQL API

If you need help with capacity planning, consider reading our [guide to estimating RU/s using Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md). 
* If you are migrating from a vCores- or server-based platform and you need guidance on estimating request units, consider reading our [guide to estimating RU/s based on vCores](estimate-ru-with-capacity-planner.md).

|Migration type|Solution|Supported sources|Supported targets|Considerations|
|---------|---------|---------|---------|---------|
|Offline|[Data Migration Tool](import-data.md)| &bull;JSON/CSV Files<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;AWS DynamoDB<br/>&bull;Azure Blob Storage|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Tables API<br/>&bull;JSON Files |&bull; Easy to set up and supports multiple sources. <br/>&bull; Not suitable for large datasets.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV Files<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB API for MongoDB<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/> <br/>See the [Azure Data Factory](../data-factory/connector-overview.md) article for other supported sources.|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB API for MongoDB<br/>&bull;JSON Files <br/><br/> See the [Azure Data Factory](../data-factory/connector-overview.md) article for other supported targets. |&bull; Easy to set up and supports multiple sources.<br/>&bull; Makes use of the Azure Cosmos DB bulk executor library. <br/>&bull; Suitable for large datasets. <br/>&bull; Lack of checkpointing - It means that if an issue occurs during the course of migration, you need to restart the whole migration process.<br/>&bull; Lack of a dead letter queue - It means that a few erroneous files can stop the entire migration process.|
|Offline|[Azure Cosmos DB Spark connector](./create-sql-api-spark.md)|Azure Cosmos DB SQL API. <br/><br/>You can use other sources with additional connectors from the Spark ecosystem.| Azure Cosmos DB SQL API. <br/><br/>You can use other targets with additional connectors from the Spark ecosystem.| &bull; Makes use of the Azure Cosmos DB bulk executor library. <br/>&bull; Suitable for large datasets. <br/>&bull; Needs a custom Spark setup. <br/>&bull; Spark is sensitive to schema inconsistencies and this can be a problem during migration. |
|Offline|[Custom tool with Cosmos DB bulk executor library](migrate-cosmosdb-data.md)| The source depends on your custom code | Azure Cosmos DB SQL API| &bull; Provides checkpointing, dead-lettering capabilities which increases migration resiliency. <br/>&bull; Suitable for very large datasets (10 TB+).  <br/>&bull; Requires custom setup of this tool running as an App Service. |
|Online|[Cosmos DB Functions + ChangeFeed API](change-feed-functions.md)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; Easy to set up. <br/>&bull; Works only if the source is an Azure Cosmos DB container. <br/>&bull; Not suitable for large datasets. <br/>&bull; Does not capture deletes from the source container. |
|Online|[Custom Migration Service using ChangeFeed](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; Provides progress tracking. <br/>&bull; Works only if the source is an Azure Cosmos DB container. <br/>&bull; Works for larger datasets as well.<br/>&bull; Requires the user to set up an App Service to host the Change feed processor. <br/>&bull; Does not capture deletes from the source container.|
|Online|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> See the [Striim website](https://www.striim.com/sources-and-targets/) for other supported sources. |&bull;Azure Cosmos DB SQL API <br/>&bull; Azure Cosmos DB Cassandra API<br/><br/> See the [Striim website](https://www.striim.com/sources-and-targets/) for other supported targets. | &bull; Works with a large variety of sources like Oracle, DB2, SQL Server.<br/>&bull; Easy to build ETL pipelines and provides a dashboard for monitoring. <br/>&bull; Supports larger datasets. <br/>&bull; Since this is a third-party tool, it needs to be purchased from the marketplace and installed in the user's environment.|

## Azure Cosmos DB Mongo API

Follow the [pre-migration guide](mongodb/pre-migration-steps.md) to plan your migration. 
* If you need help with capacity planning, consider reading our [guide to estimating RU/s using Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md). 
* If you are migrating from a vCores- or server-based platform and you need guidance on estimating request units, consider reading our [guide to estimating RU/s based on vCores](convert-vcore-to-request-unit.md).

When you are ready to migrate, you can find detailed guidance on migration tools below
* [Offline migration using MongoDB native tools](mongodb/tutorial-mongotools-cosmos-db.md)
* [Offline migration using Azure database migration service (DMS)](../dms/tutorial-mongodb-cosmos-db.md)
* [Online migration using Azure database migration service (DMS)](../dms/tutorial-mongodb-cosmos-db-online.md)
* [Offline/online migration using Azure Databricks and Spark](mongodb/migrate-databricks.md)

Then, follow our [post-migration guide](mongodb/post-migration-optimization.md) to optimize your Azure Cosmos DB data estate once you have migrated.

A summary of migration pathways from your current solution to Azure Cosmos DB API for MongoDB is provided below:

|Migration type|Solution|Supported sources|Supported targets|Considerations|
|---------|---------|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|Azure Cosmos DB API for MongoDB |&bull; Makes use of the Azure Cosmos DB bulk executor library. <br/>&bull; Suitable for large datasets and takes care of replicating live changes. <br/>&bull; Works only with other MongoDB sources.|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| Azure Cosmos DB API for MongoDB| &bull; Makes use of the Azure Cosmos DB bulk executor library. <br/>&bull; Suitable for large datasets and takes care of replicating live changes. <br/>&bull; Works only with other MongoDB sources.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db-mongodb-api.md)| &bull;JSON/CSV Files<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB API for MongoDB <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/><br/> See the [Azure Data Factory](../data-factory/connector-overview.md) article for other supported sources. | &bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB API for MongoDB <br/>&bull; JSON files <br/><br/> See the [Azure Data Factory](../data-factory/connector-overview.md) article for other supported targets.| &bull; Easy to set up and supports multiple sources. <br/>&bull; Makes use of the Azure Cosmos DB bulk executor library. <br/>&bull; Suitable for large datasets. <br/>&bull; Lack of checkpointing means that any issue during the course of migration would require a restart of the whole migration process.<br/>&bull; Lack of a dead letter queue would mean that a few erroneous files could stop the entire migration process. <br/>&bull; Needs custom code to increase read throughput for certain data sources.|
|Offline|Existing Mongo Tools ([mongodump](mongodb/tutorial-mongotools-cosmos-db.md#mongodumpmongorestore), [mongorestore](mongodb/tutorial-mongotools-cosmos-db.md#mongodumpmongorestore), [Studio3T](mongodb/connect-using-mongochef.md))|MongoDB | Azure Cosmos DB API for MongoDB| &bull; Easy to set up and integration. <br/>&bull; Needs custom handling for throttles.|

## Azure Cosmos DB Cassandra API

If you need help with capacity planning, consider reading our [guide to estimating RU/s using Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md). 

|Migration type|Solution|Supported sources|Supported targets|Considerations|
|---------|---------|---------|---------|---------|
|Offline|[cqlsh COPY command](cassandra/migrate-data.md#migrate-data-by-using-the-cqlsh-copy-command)|CSV Files | Azure Cosmos DB Cassandra API| &bull; Easy to set up. <br/>&bull; Not suitable for large datasets. <br/>&bull; Works only when the source is a Cassandra table.|
|Offline|[Copy table with Spark](cassandra/migrate-data.md#migrate-data-by-using-spark) | &bull;Apache Cassandra<br/>&bull;Azure Cosmos DB Cassandra API| Azure Cosmos DB Cassandra API | &bull; Can make use of Spark capabilities to parallelize transformation and ingestion. <br/>&bull; Needs configuration with a custom retry policy to handle throttles.|
|Online|[Striim (from Oracle DB/Apache Cassandra)](cassandra/migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> See the [Striim website](https://www.striim.com/sources-and-targets/) for other supported sources.|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Cassandra API <br/><br/> See the [Striim website](https://www.striim.com/sources-and-targets/) for other supported targets.| &bull; Works with a large variety of sources like Oracle, DB2, SQL Server. <br/>&bull; Easy to build ETL pipelines and provides a dashboard for monitoring. <br/>&bull; Supports larger datasets. <br/>&bull; Since this is a third-party tool, it needs to be purchased from the marketplace and installed in the user's environment.|
|Online|[Blitzz (from Oracle DB/Apache Cassandra)](cassandra/oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>See the [Blitzz website](https://www.blitzz.io/) for other supported sources. |Azure Cosmos DB Cassandra API. <br/><br/>See the [Blitzz website](https://www.blitzz.io/) for other supported targets. | &bull; Supports larger datasets. <br/>&bull; Since this is a third-party tool, it needs to be purchased from the marketplace and installed in the user's environment.|

## Other APIs

For APIs other than the SQL API, Mongo API and the Cassandra API, there are various tools supported by each of the API's existing ecosystems. 

**Table API** 

* [Data Migration Tool](table/table-import.md#data-migration-tool)
* [AzCopy](table/table-import.md#migrate-data-by-using-azcopy)

**Gremlin API**

* [Graph bulk executor library](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## Next steps

* Trying to do capacity planning for a migration to Azure Cosmos DB?
    * If all you know is the number of vcores and servers in your existing database cluster, read about [estimating request units using vCores or vCPUs](convert-vcore-to-request-unit.md) 
    * If you know typical request rates for your current database workload, read about [estimating request units using Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md)
* Learn more by trying out the sample applications consuming the bulk executor library in [.NET](bulk-executor-dot-net.md) and [Java](bulk-executor-java.md). 
* The bulk executor library is integrated into the Cosmos DB Spark connector, to learn more, see [Azure Cosmos DB Spark connector](./create-sql-api-spark.md) article.  
* Contact the Azure Cosmos DB product team by opening  a support ticket under the "General Advisory" problem type and "Large (TB+) migrations" problem subtype for additional help with large scale migrations.