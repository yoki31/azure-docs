---
title: Indexer overview
titleSuffix: Azure Cognitive Search
description: Crawl Azure SQL Database, SQL Managed Instance, Azure Cosmos DB, or Azure storage to extract searchable data and populate an Azure Cognitive Search index.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2022
---

# Indexers in Azure Cognitive Search

An *indexer* in Azure Cognitive Search is a crawler that extracts searchable content from cloud data sources and populates a search index using field-to-field mappings between source data and a search index. This approach is sometimes referred to as a 'pull model' because the search service pulls data in without you having to write any code that adds data to an index. Indexers also drive the [AI enrichment](cognitive-search-concept-intro.md) capabilities of Cognitive Search, integrating external processing of content en route to an index.

Indexers are cloud-only, with individual indexers for [supported data sources](#supported-data-sources). When configuring an indexer, you'll specify a data source (origin) and a search index (destination). Several sources, such as Azure Blob Storage, have additional configuration properties specific to that content type.

You can run indexers on demand or on a recurring data refresh schedule that runs as often as every five minutes. More frequent updates require a ['push model'](search-what-is-data-import.md) that simultaneously updates data in both Azure Cognitive Search and your external data source.

## How to use indexers

You can use an indexer as the sole means for data ingestion, or as part of a combination of techniques that load and optionally transform or enrich content along the way. The following table summarizes the main scenarios.

| Scenario |Strategy |
|----------|---------|
| Single data source | This pattern is the simplest: one data source is the sole content provider for a search index. Most supported data sources provide some form of change detection so that subsequent indexer runs pick up the difference when content is added or updated in the source. |
| Multiple data sources | An indexer specification can have only one data source, but the search index itself can accept content from multiple sources, where each indexer run brings new content from a different data provider. Each source can contribute its share of full documents, or populate selected fields in each document. For a closer look at this scenario, see [Tutorial: Index from multiple data sources](tutorial-multiple-data-sources.md). |
| Multiple indexers | Multiple data sources are typically paired with multiple indexers if you need to vary run time parameters, the schedule, or field mappings. </br></br>[Cross-region scale out of Cognitive Search](search-performance-optimization.md#data-sync) is another scenario. You might have copies of the same search index in different regions. To synchronize search index content, you could have multiple indexers pulling from the same data source, where each indexer targets a different search index in each region.</br></br>[Parallel indexing](search-howto-large-index.md#parallel-indexing) of very large data sets also requires a multi-indexer strategy, where each indexer targets a subset of the data. |
| Content transformation | Indexers drive [AI enrichment](cognitive-search-concept-intro.md). Content transforms are defined in a [skillset](cognitive-search-working-with-skillsets.md) that you attach to the indexer.|

<a name="supported-data-sources"></a>

## Supported data sources

Indexers crawl data stores on Azure and outside of Azure.

+ [Amazon Redshift](search-how-to-index-power-query-data-sources.md) (in preview)
+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure MySQL](search-howto-index-mysql.md) (in preview)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Elasticsearch](search-how-to-index-power-query-data-sources.md) (in preview)
+ [PostgreSQL](search-how-to-index-power-query-data-sources.md) (in preview)
+ [Salesforce Objects](search-how-to-index-power-query-data-sources.md) (in preview)
+ [Salesforce Reports](search-how-to-index-power-query-data-sources.md) (in preview)
+ [Smartsheet](search-how-to-index-power-query-data-sources.md) (in preview)
+ [Snowflake](search-how-to-index-power-query-data-sources.md) (in preview)
+ [Azure SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server on Azure Virtual Machines](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
+ [Azure Files](search-file-storage-integration.md) (in preview)

Indexers accept flattened row sets, such as a table or view, or items in a container or folder. In most cases, it creates one search document per row, record, or item.

Indexer connections to remote data sources can be made using standard Internet connections (public) or encrypted private connections when you use Azure virtual networks for client apps. You can also set up connections to authenticate using a managed identity. For more information about secure connections, see [Granting access via private endpoints](search-indexer-securing-resources.md#granting-access-via-private-endpoints) and [Connect to a data source using a managed identity](search-howto-managed-identities-data-sources.md).

## Stages of indexing

On an initial run, when the index is empty, an indexer will read in all of the data provided in the table or container. On subsequent runs, the indexer can usually detect and retrieve just the data that has changed. For blob data, change detection is automatic. For other data sources like Azure SQL or Cosmos DB, change detection must be enabled.

For each document it receives, an indexer implements or coordinates multiple steps, from document retrieval to a final search engine "handoff" for indexing. Optionally, an indexer also drives [skillset execution and outputs](cognitive-search-concept-intro.md), assuming a skillset is defined.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Indexer Stages" border="false":::

<a name="document-cracking"></a>

### Stage 1: Document cracking

Document cracking is the process of opening files and extracting content. Text-based content can be extracted from files on a service, rows in a table, or items in container or collection. If you add a skillset and [image skills](cognitive-search-concept-image-scenarios.md), document cracking can also extract images and queue them for image processing.

Depending on the data source, the indexer will try different operations to extract potentially indexable content:

+ When the document is a file, such as a PDF or other supported file format in [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#supported-document-formats), the indexer will open the file and extract text, images, and metadata. Indexers can also open files from [SharePoint](search-howto-index-sharepoint-online.md#supported-document-formats) and [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md#supported-document-formats).

+ When the document is a record in [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), the indexer will extract non-binary content from each field in each record.

+ When the document is a record in [Cosmos DB](search-howto-index-cosmosdb.md), the indexer will extract non-binary content from fields and subfields from the Cosmos DB document.

### Stage 2: Field mappings 

An indexer extracts text from a source field and sends it to a destination field in an index or knowledge store. When field names and data types coincide, the path is clear. However, you might want different names or types in the output, in which case you need to tell the indexer how to map the field.

To [specify field mappings](search-indexer-field-mappings.md), enter the source and destination fields in the indexer definition.

Field mapping occurs after document cracking, but before transformations, when the indexer is reading from the source documents. When you define a field mapping, the value of the source field is sent as-is to the destination field with no modifications. 

### Stage 3: Skillset execution

Skillset execution is an optional step that invokes built-in or custom AI processing. You might need it for optical character recognition (OCR) in the form of image analysis if the source data is a binary image, or you might need text translation if content is in different languages. 

Whatever the transformation, skillset execution is where enrichment occurs. If an indexer is a pipeline, you can think of a [skillset](cognitive-search-defining-skillset.md) as a "pipeline within the pipeline".

### Stage 4: Output field mappings

If you include a skillset, you will need to [specify output field mappings](cognitive-search-output-field-mapping.md) in the indexer definition. The output of a skillset is manifested internally as a tree structure referred to as an *enriched document*. Output field mappings allow you to select which parts of this tree to map into fields in your index.

Despite the similarity in names, output field mappings and field mappings build associations from different sources. Field mappings associate the content of source field to a destination field in a search index. Output field mappings associate the content of an internal enriched document (skill outputs) to destination fields in the index. Unlike field mappings, which are considered optional, you will always need to define an output field mapping for any transformed content that needs to reside in an index.

The next image shows a sample indexer [debug session](cognitive-search-debug-session.md) representation of the indexer stages: document cracking, field mappings, skillset execution, and output field mappings.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="sample debug session" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## Basic workflow

Indexers can offer features that are unique to the data source. In this respect, some aspects of indexer or data source configuration will vary by indexer type. However, all indexers share the same basic composition and requirements. Steps that are common to all indexers are covered below.

### Step 1: Create a data source

Indexers require a *data source* object that provides a connection string and possibly credentials. Call the [Create Data Source (REST)](/rest/api/searchservice/create-data-source) or [SearchIndexerDataSourceConnection class](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) to create the resource.

Data sources are configured and managed independently of the indexers that use them, which means a data source can be used by multiple indexers to load more than one index at a time.

### Step 2: Create an index

An indexer will automate some tasks related to data ingestion, but creating an index is generally not one of them. As a prerequisite, you must have a predefined index with fields that match those in your external data source. Fields need to match by name and data type. If not, you can [define field mappings](search-indexer-field-mappings.md) to establish the association. For more information about structuring an index, see [Create an Index (REST)](/rest/api/searchservice/Create-Index) or [SearchIndex class](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Although indexers cannot generate an index for you, the **Import data** wizard in the portal can help. In most cases, the wizard can infer an index schema from existing metadata in the source, presenting a preliminary index schema which you can edit in-line while the wizard is active. Once the index is created on the service, further edits in the portal are mostly limited to adding new fields. Consider the wizard for creating, but not revising, an index. For hands-on learning, step through the [portal walkthrough](search-get-started-portal.md).

### Step 3: Create and run (or schedule) the indexer

By default, the first indexer execution occurs when you [create an indexer](/rest/api/searchservice/Create-Indexer) on the search service. You can set the "disabled" property in an indexer to create it without running it.

During indexer execution is when you'll find out if the data source is accessible or the skillset is valid. Until indexer execution starts, dependent objects such as data sources and skillsets are inactive on the search service. 

After the first indexer run, you can re-run it on demand using [Run Indexer](/rest/api/searchservice/run-indexer), or you can [define a recurring schedule](search-howto-schedule-indexers.md). 

You can monitor [indexer status in the portal](search-howto-monitor-indexers.md) or through [Get Indexer Status API](/rest/api/searchservice/get-indexer-status). You should also [run queries on the index](search-query-create.md) to verify the result is what you expected.

## Next steps

Now that you've been introduced to indexers, a next step is to review indexer properties and parameters, scheduling, and indexer monitoring. Alternatively, you could return to the list of [supported data sources](#supported-data-sources) for more information about a specific source.

+ [Create indexers](search-howto-create-indexers.md)
+ [Reset and run indexers](search-howto-run-reset-indexers.md)
+ [Schedule indexers](search-howto-schedule-indexers.md)
+ [Define field mappings](search-indexer-field-mappings.md)
+ [Monitor indexer status](search-howto-monitor-indexers.md)
