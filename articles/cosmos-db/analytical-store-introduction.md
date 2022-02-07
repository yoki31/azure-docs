---
title: What is Azure Cosmos DB analytical store?
description: Learn about Azure Cosmos DB transactional (row-based) and analytical(column-based) store. Benefits of analytical store, performance impact for large-scale workloads, and auto sync of data from transactional store to analytical store  
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: "seo-nov-2020"
---

# What is Azure Cosmos DB analytical store?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB analytical store is a fully isolated column store for enabling large-scale analytics against operational data in your Azure Cosmos DB, without any impact to your transactional workloads. 

Azure Cosmos DB transactional store is schema-agnostic, and it allows you to iterate on your transactional applications without having to deal with schema or index management. In contrast to this, Azure Cosmos DB analytical store is schematized to optimize for analytical query performance. This article describes in detailed about analytical storage.

## Challenges with large-scale analytics on operational data

The multi-model operational data in an Azure Cosmos DB container is internally stored in an indexed row-based "transactional store". Row store format is designed to allow fast transactional reads and writes in the order-of-milliseconds response times, and operational queries. If your dataset grows large, complex analytical queries can be expensive in terms of provisioned throughput on the data stored in this format. High consumption of provisioned throughput in turn, impacts the performance of transactional workloads that are used by your real-time applications and services.

Traditionally, to analyze large amounts of data, operational data is extracted from Azure Cosmos DB's transactional store and stored in a separate data layer. For example, the data is stored in a data warehouse or data lake in a suitable format. This data is later used for large-scale analytics and analyzed using  compute engine such as the Apache Spark clusters. This separation of analytical storage and compute layers from operational data results in additional latency, because the ETL(Extract, Transform, Load) pipelines are run less frequently to minimize the potential impact on your transactional workloads.

The ETL pipelines also become complex when handling updates to the operational data when compared to handling only newly ingested operational data. 

## Column-oriented analytical store

Azure Cosmos DB analytical store addresses the complexity and latency challenges that occur with the traditional ETL pipelines. Azure Cosmos DB analytical store can automatically sync your operational data into a separate column store. Column store format is suitable for large-scale analytical queries to be performed in an optimized manner, resulting in improving the latency of such queries.

Using Azure Synapse Link, you can now build no-ETL HTAP solutions by directly linking to Azure Cosmos DB analytical store from Azure Synapse Analytics. It enables you to run near real-time large-scale analytics on your operational data.

## Features of analytical store 

When you enable analytical store on an Azure Cosmos DB container, a new column-store is internally created based on the operational data in your container. This column store is persisted separately from the row-oriented transactional store for that container. The inserts, updates, and deletes to your operational data are automatically synced to analytical store. You don't need the Change Feed or ETL to sync the data.

## Column store for analytical workloads on operational data

Analytical workloads typically involve aggregations and sequential scans of selected fields. By storing the data in a column-major order, the analytical store allows a group of values for each field to be serialized together. This format reduces the IOPS required to scan or compute statistics over specific fields. It dramatically improves the query response times for scans over large data sets. 

For example, if your operational tables are in the following format:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Example operational table" border="false":::

The row store persists the above data in a serialized format, per row, on the disk. This format allows for faster transactional reads, writes, and operational queries, such as, "Return information about Product1". However, as the dataset grows large and if you want to run complex analytical queries on the data it can be expensive. For example, if you want to get "the sales  trends for a product under the category named 'Equipment' across different business units and months", you need to run a complex query. Large scans on this dataset can get expensive in terms of provisioned throughput and can also impact the performance of the transactional workloads powering your real-time applications and services.

Analytical store, which is a column store, is better suited for such queries because it serializes similar fields of data together and reduces the disk IOPS.

The following image shows transactional row store vs. analytical column store in Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transactional row store Vs analytical column store in Azure Cosmos DB" border="false":::

## Decoupled performance for analytical workloads

There's no impact on the performance of your transactional workloads due to analytical queries, as the analytical store is separate from the transactional store.  Analytical store doesn't need separate request units (RUs) to be allocated.

## Auto-Sync

Auto-Sync refers to the fully managed capability of Azure Cosmos DB where the inserts, updates, deletes to operational data are automatically synced from transactional store to analytical store in near real time. Auto-sync latency is usually within 2 minutes. In cases of shared throughput database with a large number of containers, auto-sync latency of individual containers could be higher and take up to 5 minutes. We would like to learn more how this latency fits your scenarios. For that, please reach out to the [Azure Cosmos DB Team](mailto:cosmosdbsynapselink@microsoft.com).

At the end of each execution of the automatic sync process, your transactional data will be immediately available for Azure Synapse Analytics runtimes:

* Azure Synapse Analytics Spark pools can read all data, including the most recent updates, through Spark tables, which are updated automatically, or via the `spark.read` command, that always reads the last state of the data.

*  Azure Synapse Analytics SQL Serverless pools can read all data, including the most recent updates, through views, which are updated automatically, or via `SELECT` together with the` OPENROWSET` commands, which always reads the latest status of the data.

> [!NOTE]
> Your transactional data will be synchronized to analytical store even if your transactional TTL is smaller than 2 minutes. 

> [!NOTE]
> Please note that if you delete your container, analytical store is also deleted.

## Scalability & elasticity

By using horizontal partitioning, Azure Cosmos DB transactional store can elastically scale the storage and throughput without any downtime. Horizontal partitioning in the transactional store provides scalability & elasticity in auto-sync to ensure data is synced to the analytical store in near real time. The data sync happens regardless of the transactional traffic throughput, whether it's 1000 operations/sec or 1 million operations/sec, and  it doesn't impact the provisioned throughput in the transactional store. 

## <a id="analytical-schema"></a>Automatically handle schema updates

Azure Cosmos DB transactional store is schema-agnostic, and it allows you to iterate on your transactional applications without having to deal with schema or index management. In contrast to this, Azure Cosmos DB analytical store is schematized to optimize for analytical query performance. With the auto-sync capability, Azure Cosmos DB manages the schema inference over the latest updates from the transactional store. It also manages the schema representation in the analytical store out-of-the-box which, includes handling nested data types.

As your schema evolves, and new properties are added over time, the analytical store automatically presents a unionized schema across all historical schemas in the transactional store.

> [!NOTE]
> In the context of analytical store, we consider the following structures as property:
> * JSON "elements" or "string-value pairs separated by a `:` ".
> * JSON objects, delimited by `{` and `}`.
> * JSON arrays, delimited by `[` and `]`.


### Schema constraints

The following constraints are applicable on the operational data in Azure Cosmos DB when you enable analytical store to automatically infer and represent the schema correctly:

* You can have a maximum of 1000 properties across all nested levels in the document schema and a maximum nesting depth of 127.
  * Only the first 1000 properties are represented in the analytical store.
  * Only the first 127 nested levels are represented in the analytical store.
  * The first level of a JSON document is its `/` root level.
  * Properties in the first level of the document will be represented as columns.


* Sample scenarios:
  * If your document's first level has 2000 properties, only the first 1000 will be represented.
  * If your documents have five levels with 200 properties in each one, all properties will be represented.
  * If your documents have 10 levels with 400 properties in each one, only the two first levels will be fully represented in analytical store. Half of the third level will also be represented.

* The hypothetical document below contains four properties and three levels.
  * The levels are `root`, `myArray`, and the nested structure within the `myArray`.
  * The properties are `id`, `myArray`, `myArray.nested1`, and `myArray.nested2`.
  * The analytical store representation will have two columns, `id`, and `myArray`. You can use Spark or T-SQL functions to also expose the nested structures as columns.


```json
{
  "id": "1",
  "myArray": [
    "string1",
    "string2",
    {
      "nested1": "abc",
      "nested2": "cde"
    }
  ]
}
```

* While JSON documents (and Cosmos DB collections/containers) are case-sensitive from the uniqueness perspective, analytical store isn't.

  * **In the same document:** Properties names in the same level should be unique when compared case-insensitively. For example, the following JSON document has "Name" and "name" in the same level. While it's a valid JSON document, it doesn't satisfy the uniqueness constraint and hence won't be fully represented in the analytical store. In this example, "Name" and "name" are the same when compared in a case-insensitive manner. Only `"Name": "fred"` will be represented in analytical store, because it's the first occurrence. And `"name": "john"` won't be represented at all.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **In different documents:** Properties in the same level and with the same name, but in different cases, will be represented within the same column, using the name format of the first occurrence. For example, the following JSON documents have `"Name"` and `"name"` in the same level. Since the first document format is `"Name"`, this is what will be used to represent the property name in analytical store. In other words, the column name in analytical store will be `"Name"`. Both `"fred"` and `"john"` will be represented, in the `"Name"` column.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* The first document of the collection defines the initial analytical store schema.
  * Documents with more properties than the initial schema will generate new columns in analytical store.
  * Columns can't be removed.
  * The deletion of all documents in a collection doesn't reset the analytical store schema.
  * There's not schema versioning. The last version inferred from transactional store is what you'll see in analytical store.

* Currently Azure Synapse Spark can't read properties that contain some special characters in their names, listed below. Azure Synapse SQL serverless isn't affected.
  * `:` 
  * ``` 
  * `,` 
  * `;` 
  * `{}`
  * `()`
  * `\n`
  * `\t`
  * `=`
  * `"`

> [!NOTE]
> White spaces are also listed in the Spark error message returned when you reach this limitation. But we have added a special treatment for white spaces, please check out more details in the items below.
 
* If you have properties names using the characters listed above, the alternatives are:
   * Change your data model in advance to avoid these characters.
   * Since currently we don't support schema reset, you can change your application to add a redundant property with a similar name, avoiding these characters.
   * Use Change Feed to create a materialized view of your container without these characters in properties names.
   * Use the `dropColumn` Spark option to ignore the affected columns and load all other columns into a DataFrame. The syntax is:

```Python
# Removing one column:
df = spark.read\
     .format("cosmos.olap")\
     .option("spark.synapse.linkedService","<your-linked-service-name>")\
     .option("spark.synapse.container","<your-container-name>")\
     .option("spark.synapse.dropColumn","FirstName,LastName")\
     .load()
     
# Removing multiple columns:
df = spark.read\
     .format("cosmos.olap")\
     .option("spark.synapse.linkedService","<your-linked-service-name>")\
     .option("spark.synapse.container","<your-container-name>")\
     .option("spark.synapse.dropColumn","FirstName,LastName;StreetName,StreetNumber")\
     .option("spark.cosmos.dropMultiColumnSeparator", ";")\
     .load()  
```

* Azure Synapse Spark now supports properties with white spaces in their names. For that, you need to use the `allowWhiteSpaceInFieldNames` Spark option to load the affected columns into a DataFrame, keeping the original name. The syntax is:

```Python
df = spark.read\
     .format("cosmos.olap")\
     .option("spark.synapse.linkedService","<your-linked-service-name>")\
     .option("spark.synapse.container","<your-container-name>")\
     .option("spark.cosmos.allowWhiteSpaceInFieldNames", "true")\
    .load()
```

* The following BSON datatypes aren't supported and won't be represented in analytical store:
  * Decimal128
  * Regular Expression
  * DB Pointer
  * JavaScript
  * Symbol
  * MinKey/MaxKey 

* When using DateTime strings that follow the ISO 8601 UTC standard, expect the following behavior:
  * Spark pools in Azure Synapse will represent these columns as `string`.
  * SQL serverless pools in Azure Synapse will represent these columns as `varchar(8000)`.

* SQL serverless pools in Azure Synapse support result sets with up to 1000 columns, and exposing nested columns also counts towards that limit. Please consider this information when designing your data architecture and modeling your transactional data.

* If you rename a property, in one or many documents, it will be considered a new column. If you execute the same rename in all documents in the collection, all data will be migrated to the new column and the old column will be represented with `NULL` values.

### Schema representation

There are two types of schema representation in the analytical store. These types define the schema representation method for all containers in the database account and have tradeoffs between the simplicity of query experience versus the convenience of a more inclusive columnar representation for polymorphic schemas.

* Well-defined schema representation, default option for SQL (CORE) API accounts. 
* Full fidelity schema representation, default option for Azure Cosmos DB API for MongoDB accounts.

#### Full fidelity schema for SQL API accounts

It's possible to use full fidelity Schema for SQL (Core) API accounts, instead of the default option, by setting the schema type when enabling Synapse Link on a Cosmos DB account for the first time. Here are the considerations about changing the default schema representation type:

 * This option is only valid for accounts that **don't** have Synapse Link already enabled.
 * It isn't possible to reset the schema representation type, from well-defined to full fidelity or vice-versa.
 * Currently Azure Cosmos DB API for MongoDB isn't compatible with this possibility of changing the schema representation. All MongoDB accounts will always have full fidelity schema representation type.
 * Currently this change can't be made through the Azure portal. All database accounts that have Synapse Link enabled by the Azure portal will have the default schema representation type, well-defined schema.
 
The schema representation type decision must be made at the same time that Synapse Link is enabled on the account, using Azure CLI or PowerShell.
 
 With the Azure CLI:
 ```cli
 az cosmosdb create --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --subscription MySubscription --analytical-storage-schema-type "FullFidelity" --enable-analytical-storage true
 ```
 
> [!NOTE]
> In the command above, replace `create` with `update` for existing accounts.
 
  With the PowerShell:
  ```
   New-AzCosmosDBAccount -ResourceGroupName MyResourceGroup -Name MyCosmosDBDatabaseAccount  -EnableAnalyticalStorage true -AnalyticalStorageSchemaType "FullFidelity"
   ```
 
> [!NOTE]
> In the command above, replace `New-AzCosmosDBAccount` with `Update-AzCosmosDBAccount` for existing accounts.
 


#### Well-defined schema representation

The well-defined schema representation creates a simple tabular representation of the schema-agnostic data in the transactional store. The well-defined schema representation has the following considerations:

* The first document defines the base schema and property must always have the same type across all documents. The only exceptions are:
  * From `NULL` to any other data type. The first non-null occurrence defines the column data type. Any document not following the first non-null datatype won't be represented in analytical store.
  * From `float` to `integer`. All documents will be represented in analytical store.
  * From `integer` to `float`. All documents will be represented in analytical store. However, to read this data with Azure Synapse SQL serverless pools, you must use a WITH clause to convert the column to `varchar`. And after this initial conversion, it's possible to convert it again to a number. Please check the example below, where **num** initial value was an integer and the second one was a float.

```SQL
SELECT CAST (num as float) as num
FROM OPENROWSET(PROVIDER = 'CosmosDB',
                CONNECTION = '<your-connection',
                OBJECT = 'IntToFloat',
                SERVER_CREDENTIAL = 'your-credential'
) 
WITH (num varchar(100)) AS [IntToFloat]
```

  * Properties that don't follow the base schema data type won't be represented in analytical store. For example, consider the documents below: the first one defined the analytical store base schema. The second document, where `id` is `"2"`, **doesn't** have a well-defined schema since property `"code"` is a string and the first document has `"code"` as a number. In this case, the analytical store registers the data type of `"code"` as `integer` for lifetime of the container. The second document will still be included in analytical store, but its `"code"` property won't.
  
    * `{"id": "1", "code":123}` 
    * `{"id": "2", "code": "123"}`
     
 > [!NOTE]
 > The condition above doesn't apply for `NULL` properties. For example, `{"a":123} and {"a":NULL}` is still well-defined.

> [!NOTE]
 > The condition above doesn't change if you update `"code"` of document `"1"` to a string in your transactional store. In analytical store, `"code"` will be kept as `integer` since currently we don't support schema reset.

* Array types must contain a single repeated type. For example, `{"a": ["str",12]}` isn't a well-defined schema because the array contains a mix of integer and string types.

> [!NOTE]
> If the Azure Cosmos DB analytical store follows the well-defined schema representation and the specification above is violated by certain items, those items won't be included in the analytical store.

* Expect different behavior in regard to different types in well-defined schema:
  * Spark pools in Azure Synapse will represent these values as `undefined`.
  * SQL serverless pools in Azure Synapse will represent these values as `NULL`.

* Expect different behavior in regard to explicit `NULL` values:
  * Spark pools in Azure Synapse will read these values as `0` (zero). And it will change to `undefined` as soon as the column has a non-null value.
  * SQL serverless pools in Azure Synapse will read these values as `NULL`.
    
* Expect different behavior in regard to missing columns:
  * Spark pools in Azure Synapse will represent these columns as `undefined`.
  * SQL serverless pools in Azure Synapse will represent these columns as `NULL`.


#### Full fidelity schema representation

The full fidelity schema representation is designed to handle the full breadth of polymorphic schemas in the schema-agnostic operational data. In this schema representation, no items are dropped from the analytical store even if the well-defined schema constraints (that is no mixed data type fields nor mixed data type arrays) are violated.

This is achieved by translating the leaf properties of the operational data into the analytical store with distinct columns based on the data type of values in the property. The leaf property names are extended with data types as a suffix in the analytical store schema such that they can be queries without ambiguity.

In the full fidelity schema representation, each datatype of each property will generate a column for that datatype. Each of them count as one of the 1000 maximum properties.

For example, let’s take the following sample document in the transactional store:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

The leaf property `streetNo` within the nested object `address` will be represented in the analytical store schema as a column `address.object.streetNo.int32`. The datatype is added as a suffix to the column. This way, if another document is added to the transactional store where the value of leaf property `streetNo` is "123" (note it’s a string), the schema of the analytical store automatically evolves without altering the type of a previously written column. A new column added to the analytical store as `address.object.streetNo.string` where this value of "123" is stored.

**Data type to suffix map**

Here's a map of all the property data types and their suffix representations in the analytical store:

|Original data type  |Suffix  |Example  |
|---------|---------|---------|
| Double |	".float64" |	24.99|
| Array	| ".array" |	["a", "b"]|
|Binary	| ".binary"	|0|
|Boolean	| ".bool"	|True|
|Int32	| ".int32"	|123|
|Int64	| ".int64"	|255486129307|
|NULL	| ".NULL"	| NULL|
|String| 	".string" |	"ABC"|
|Timestamp |	".timestamp" |	Timestamp(0, 0)|
|DateTime	|".date"	| ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId	|".objectId"	| ObjectId("5f3f7b59330ec25c132623a2")|
|Document	|".object" |	{"a": "a"}|

* Expect different behavior in regard to explicit `NULL` values:
  * Spark pools in Azure Synapse will read these values as `0` (zero).
  * SQL serverless pools in Azure Synapse will read these values as `NULL`.
  
* Expect different behavior in regard to missing columns:
  * Spark pools in Azure Synapse will represent these columns as `undefined`.
  * SQL serverless pools in Azure Synapse will represent these columns as `NULL`.

## <a id="analytical-ttl"></a> Analytical Time-to-Live (TTL)

Analytical TTL (ATTL) indicates how long data should be retained in your analytical store, for a container.

Analytical store is enabled when ATTL is set with a value other than `NULL` and `0`. When enabled, inserts, updates, deletes to operational data are automatically synced from transactional store to analytical store, irrespective of the transactional TTL (TTTL) configuration. The retention of this transactional data in analytical store can be controlled at container level by the `AnalyticalStoreTimeToLiveInSeconds` property.

The possible ATTL configurations are:

* If the value is set to `0` or set to `NULL`: the analytical store is disabled and no data is replicated from transactional store to analytical store

* If the value is set to `-1`: the analytical store retains all historical data, irrespective of the retention of the data in the transactional store. This setting indicates that the analytical store has infinite retention of your operational data

* If the value is set to any positive integer `n` number: items will expire from the analytical store `n` seconds after their last modified time in the transactional store. This setting can be leveraged if you want to retain your operational data for a limited period of time in the analytical store, irrespective of the retention of the data in the transactional store

Some points to consider:

*	After the analytical store is enabled with an ATTL value, it can be updated to a different valid value later. 
*	While TTTL can be set at the container or item level, ATTL can only be set at the container level currently.
*	You can achieve longer retention of your operational data in the analytical store by setting ATTL >= TTTL at the container level.
*	The analytical store can be made to mirror the transactional store by setting ATTL = TTTL.
*	If you have ATTL bigger than TTTL, at some point in time you'll have data that only exists in analytical store. This data is read only.

How to enable analytical store on a container:

* From the Azure portal, the ATTL option, when turned on, is set to the default value of -1. You can change this value to 'n' seconds, by navigating to container settings under Data Explorer. 
 
* From the Azure Management SDK, Azure Cosmos DB SDKs, PowerShell, or Azure CLI, the ATTL option can be enabled by setting it to either -1 or 'n' seconds. 

To learn more, see [how to configure analytical TTL on a container](configure-synapse-link.md#create-analytical-ttl).

## Cost-effective archival of historical data

Data tiering refers to the separation of data between storage infrastructures optimized for different scenarios. Thereby improving the overall performance and cost-effectiveness of the end-to-end data stack. With analytical store, Azure Cosmos DB now supports automatic tiering of data from the transactional store to analytical store with different data layouts. With analytical store optimized in terms of storage cost compared to the transactional store, allows you to retain much longer horizons of operational data for historical analysis.

After the analytical store is enabled, based on the data retention needs of the transactional workloads, you can configure the transactional store Time-to-Live (TTTL) property to have records automatically deleted from the transactional store after a certain time period. Similarly, the  analytical store Time-to-Live (ATTL)' allows you to manage the lifecycle of data retained in the analytical store independent from the transactional store. By enabling analytical store and configuring TTL properties, you can seamlessly tier and define the data retention period for the two stores.

## Backup

Currently analytical store doesn't support backup and restore, and your backup policy can't be planned relying on that. For more information, check the limitations section of [this](synapse-link.md#limitations) document. While continuous backup mode isn't supported in database accounts with Synapse Link enabled, periodic backup mode is. 

With periodic backup mode and existing containers, you can:

 ### Fully rebuild analytical store when TTTL >= ATTL
 
 The original container is restored without analytical store. But you can enable it and it will be rebuild with all data that existing in the container.
 
 ### Partially rebuild analytical store when TTTL < ATTL
 
The data that was only in analytical store isn't restored, but it will be kept available for queries as long as you keep the original container. Analytical store is only deleted when you delete the container. Your analytical queries in Azure Synapse Analytics can read data from both original and restored container's analytical stores. Example:

 * Container `OnlineOrders` has TTTL set to one month and ATTL set for one year.
 * When you restore it to `OnlineOrdersNew` and turn on analytical store to rebuild it, there will be only one month of data in both transactional and analytical store.
 * Original container `OnlineOrders` isn't deleted and its analytical store is still available.
 * New data is only ingested into `OnlineOrdersNew`.
 * Analytical queries will do a UNION ALL from analytical stores while the original data is still relevant.

If you want to delete the original container but don't want to lose its analytical store data, you can persist the analytical store of the original container in another Azure data service. Synapse Analytics has the capability to perform joins between data stored in different locations. An example: A Synapse Analytics query joins analytical store data with external tables located in Azure Blob Storage, Azure Data Lake Store, etc.

It's important to note that the data in the analytical store has a different schema than what exists in the transactional store. While you can generate snapshots of your analytical store data, and export it to any Azure Data service, at no RUs costs, we can't guarantee the use of this snapshot to back feed the transactional store. This process isn't supported.


## Global Distribution

If you have a globally distributed Azure Cosmos DB account, after you enable analytical store for a container, it will be available in all regions of that account.  Any changes to operational data are globally replicated in all regions. You can run analytical queries effectively against the nearest regional copy of your data in Azure Cosmos DB.

## Partitioning

Analytical store partitioning is completely independent of partitioning in the transactional store. By default, data in analytical store isn't partitioned. If your analytical queries have frequently used filters, you have the option to partition based on these fields for better query performance. To learn more, see the [introduction to custom partitioning](custom-partitioning-analytical-store.md) and [how to configure custom partitioning](configure-custom-partitioning.md) articles.  

## Security

* **Authentication with the analytical store** is the same as the transactional store for a given database. You can use primary, secondary, or read-only keys for authentication. You can leverage linked service in Synapse Studio to prevent pasting the Azure Cosmos DB keys in the Spark notebooks. For Azure Synapse SQL serverless, you can use SQL credentials to also prevent pasting the Azure Cosmos DB keys in the SQL notebooks. The Access to these Linked Services or to these SQL credentials are available to anyone who has access to the workspace. Please note that the Cosmos DB read only key can also be used.

* **Network isolation using private endpoints** - You can control network access to the data in the transactional and analytical stores independently. Network isolation is done using separate managed private endpoints for each store, within managed virtual networks in Azure Synapse workspaces. To learn more, see how to [Configure private endpoints for analytical store](analytical-store-private-endpoints.md) article.

* **Data encryption with customer-managed keys** - You can seamlessly encrypt the data across transactional and analytical stores using the same customer-managed keys in an automatic and transparent manner. Azure Synapse Link only supports configuring customer-managed keys using your Azure Cosmos DB account's managed identity. You must configure your account's managed identity in your Azure Key Vault access policy before [enabling Azure Synapse Link](configure-synapse-link.md#enable-synapse-link) on your account. To learn more, see how to [Configure customer-managed keys using Azure Cosmos DB accounts' managed identities](how-to-setup-cmk.md#using-managed-identity) article.

## Support for multiple Azure Synapse Analytics runtimes

The analytical store is optimized to provide scalability, elasticity, and performance for analytical workloads without any dependency on the compute run-times. The storage technology is self-managed to optimize your analytics workloads without manual efforts.

By decoupling the analytical storage system from the analytical compute system, data in Azure Cosmos DB analytical store can be queried simultaneously from the different analytics runtimes supported by Azure Synapse Analytics. As of today, Azure Synapse Analytics supports Apache Spark and serverless SQL pool with Azure Cosmos DB analytical store.

> [!NOTE]
> You can only read from analytical store using Azure Synapse Analytics runtimes. And the opposite is also true, Azure Synapse Analytics runtimes can only read from analytical store. Only the auto-sync process can change data in analytical store. You can write data back to Cosmos DB transactional store using Azure Synapse Analytics Spark pool, using the built-in Azure Cosmos DB OLTP SDK.

## <a id="analytical-store-pricing"></a> Pricing

Analytical store follows a consumption-based pricing model where you're charged for:

* Storage: the volume of the data retained in the analytical store every month including historical data as defined by analytical TTL.

* Analytical write operations: the fully managed synchronization of operational data updates to the analytical store from the transactional store (auto-sync)

* Analytical read operations: the read operations performed against the analytical store from Azure Synapse Analytics Spark pool and serverless SQL pool run times.

Analytical store pricing is separate from the transaction store pricing model. There's no concept of provisioned RUs in the analytical store. See [Azure Cosmos DB pricing page](https://azure.microsoft.com/pricing/details/cosmos-db/) for full details on the pricing model for analytical store.

Data in the analytics store can only be accessed through Azure Synapse Link, which is done in the Azure Synapse Analytics runtimes: Azure Synapse Apache Spark pools and
Azure Synapse serverless SQL pools. See [Azure Synapse Analytics pricing page](https://azure.microsoft.com/pricing/details/synapse-analytics/) for full details on the pricing model to access data in analytical store.

In order to get a high-level cost estimate to enable analytical store on an Azure Cosmos DB container, from the analytical store perspective, you can use the [Azure Cosmos DB Capacity planner](https://cosmos.azure.com/capacitycalculator/) and get an estimate of your analytical storage and write operations costs. Analytical read operations costs depends on the analytics workload characteristics but as a high-level estimate, scan of 1 TB of data in analytical store typically results in 130,000 analytical read operations, and results in a cost of $0.065.

> [!NOTE]
> Analytical store read operations estimates aren't included in the Cosmos DB cost calculator since they are a function of your analytical workload. While the above estimate is for scanning 1TB of data in analytical store, applying filters reduces the volume of data scanned and this determines the exact number of analytical read operations given the consumption pricing model. A proof-of-concept around the analytical workload would provide a more finer estimate of analytical read operations. This estimate doesn't include the cost of Azure Synapse Analytics.



## Next steps

To learn more, see the following docs:

* [Azure Synapse Link for Azure Cosmos DB](synapse-link.md)

* Check out the learn module on how to [Design hybrid transactional and analytical processing using Azure Synapse Analytics](/learn/modules/design-hybrid-transactional-analytical-processing-using-azure-synapse-analytics/)

* [Get started with Azure Synapse Link for Azure Cosmos DB](configure-synapse-link.md)

* [Frequently asked questions about Synapse Link for Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)

* [Azure Synapse Link for Azure Cosmos DB Use cases](synapse-link-use-cases.md)
