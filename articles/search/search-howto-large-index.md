---
title: Index large data set using built-in indexers
titleSuffix: Azure Cognitive Search
description: Strategies for large data indexing or computationally-intensive indexing through batch mode, resourcing, and techniques for scheduled, parallel, and distributed indexing.

manager: nitinme
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/20/2022
---

# Index large data sets in Azure Cognitive Search

Azure Cognitive Search supports [two basic approaches](search-what-is-data-import.md) for importing data into a search index: *pushing* your data into the index programmatically, or pointing an [Azure Cognitive Search indexer](search-indexer-overview.md) at a supported data source to *pull* in the data.

As data volumes grow or processing needs change, you might find that simple or default indexing strategies are no longer practical. For Azure Cognitive Search, there are several approaches for accommodating larger data sets, ranging from how you structure a data upload request, to using a source-specific indexer for scheduled and distributed workloads.

The same techniques also apply to long-running processes. In particular, the steps outlined in [parallel indexing](#parallel-indexing) are helpful for computationally intensive indexing, such as image analysis or natural language processing in an [AI enrichment pipeline](cognitive-search-concept-intro.md).

The following sections explain techniques for indexing large amounts of data using both the push API and indexers.For more information and code samples that illustrate push model indexing, see [Tutorial: Optimize indexing speeds](tutorial-optimize-indexing-push-api.md).

## Indexing with the "push" API

When pushing data into an index using the [Add Documents REST API](/rest/api/searchservice/addupdate-or-delete-documents) or the [IndexDocuments method (.NET)](/dotnet/api/azure.search.documents.searchclient.indexdocuments), there are several key considerations that impact indexing speed. Those factors are outlined in the section below, and range from setting service capacity to code optimizations.

+ [Index schema](#review-index-schema)
+ [Data location and transfer speed](#check-data-location)
+ [Batch multiple documents per request](#check-the-batch-size)
+ [Service capacity](#check-service-capacity-and-partitions)
+ [Manage threads](#add-threads-and-a-retry-strategy)

## Review index schema

The schema of your index plays an important role in indexing data. The more fields you have, and the more properties you set (such as *searchable*, *facetable*, or *filterable*), all contribute to increased indexing time.

To keep document size down, avoid adding non-queryable data to an index. Every field that you add to an index should be there for a reason. If you need to integrate non-queryable data such as images into search results, you should define a non-searchable field that stores a URL reference to the resource.

## Check data location

Network data transfer speeds can be a limiting factor when indexing data. Indexing data from within your Azure environment is an easy way to speed up indexing.

## Check service capacity and partitions

1. Review the characteristics and [limits](search-limits-quotas-capacity.md) of the tier at which you provisioned the service. Service tiers differ by the size and speed of partitions, which has a direct impact on indexing speed. If the tier is insufficient for the workload, upgrading might be the easiest and most effective solution for increasing indexing throughput.

1. [Increase the number of partitions](search-capacity-planning.md#add-or-reduce-replicas-and-partitions), even if only on a temporary basis. Partition allocation can be readjusted downwards after an initial indexing run to reduce the overall cost of running the service.

Adding more replicas may also increase indexing speeds but it isn't guaranteed. On the other hand, additional replicas will increase the query volume your search service can handle. Because indexing does not run in the background, increasing query capacity should help overall performance.

> [!NOTE]
> When [adding partition and replicas](search-capacity-planning.md#add-or-reduce-replicas-and-partitions), or provisioning a service at a higher tier, consider the monetary cost and allocation time. Adding partitions can significantly increase indexing speed, but adding and removing them can take anywhere from 15 minutes to several hours.
>

## Check the batch size

One of the simplest mechanisms for indexing a larger data set is to submit multiple documents or records in a single request. As long as the entire payload is under 16 MB, a request can handle up to 1000 documents in a bulk upload operation. These limits apply whether you're using the [Add Documents REST API](/rest/api/searchservice/addupdate-or-delete-documents) or the [IndexDocuments method](/dotnet/api/azure.search.documents.searchclient.indexdocuments) in the .NET SDK. For either API, you would package 1000 documents in the body of each request.

Using batches to index documents will significantly improve indexing performance. Determining the optimal batch size for your data is a key component of optimizing indexing speeds. The two primary factors influencing the optimal batch size are:

+ The schema of your index
+ The size of your data

Because the optimal batch size depends on your index and your data, the best approach is to test different batch sizes to determine what results in the fastest indexing speeds for your scenario. [Tutorial: Optimize indexing with the push API](tutorial-optimize-indexing-push-api.md) provides sample code for testing batch sizes using the .NET SDK.

## Add threads and a retry strategy

In contrast with indexer APIs, when you are using the push APIs to index documents, your application code should ensure there are sufficient threads to make full use of the available capacity. 

1. [Increase the number of threads](tutorial-optimize-indexing-push-api.md#use-multiple-threadsworkers) in your client code. As you increase the tier of your search service or increase the partitions, you should also increase the number of concurrent threads so that you can take full advantage of the new capacity.

1. As you ramp up the requests hitting the search service, you may encounter [HTTP status codes](/rest/api/searchservice/http-status-codes) indicating the request didn't fully succeed. During indexing, two common HTTP status codes are:

   + **503 Service Unavailable** - This error means that the system is under heavy load and your request can't be processed at this time.

   + **207 Multi-Status** - This error means that some documents succeeded, but at least one failed.

1. To handle failures, requests should be retried using an [exponential backoff retry strategy](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

The Azure .NET SDK automatically retries 503s and other failed requests but you'll need to implement your own logic to retry 207s. Open-source tools such as [Polly](https://github.com/App-vNext/Polly) can also be used to implement a retry strategy.

## Indexer-based "pull" indexing 

[Indexers](search-indexer-overview.md) crawl [supported data sources](search-indexer-overview.md#supported-data-sources) for searchable content. While not specifically intended for large-scale indexing, several indexer capabilities are particularly useful for accommodating larger data sets:

+ Schedules allow you to parcel out indexing at regular intervals so that you can spread it out over time.

+ Scheduled indexing can resume at the last known stopping point. If a data source is not fully crawled within a 24-hour window, the indexer will resume indexing on day two at wherever it left off.

+ Partitioning data into smaller individual data sources enables parallel processing. You can break up source data into smaller components, such as into multiple containers in Azure Blob Storage, create a [data source](/rest/api/searchservice/create-data-source) for each partition, and then run multiple indexers in parallel. 

### Check indexer batchSize

As with the push API, indexers allow you to configure the number of items per batch. For indexers based on the [Create Indexer REST API](/rest/api/searchservice/Create-Indexer), you can set the `batchSize` argument to customize this setting to better match the characteristics of your data. 

Default batch sizes are data source specific. Azure SQL Database and Azure Cosmos DB have a default batch size of 1000. In contrast, Azure Blob indexing sets batch size at 10 documents in recognition of the larger average document size. 

## Scheduled indexers for long-running processes

Indexer scheduling is an important mechanism for processing large data sets, and slow-running processes like image analysis in a cognitive search pipeline. Indexer processing operates within a 24-hour window. If processing fails to finish within 24 hours, the behaviors of indexer scheduling can work to your advantage. 

By design, scheduled indexing starts at specific intervals, with a job typically completing before resuming at the next scheduled interval. However, if processing does not complete within the interval, the indexer stops (because it ran out of time). At the next interval, processing resumes where it last left off, with the system keeping track of where that occurs. 

In practical terms, for index loads spanning several days, you can put the indexer on a 24-hour schedule. When indexing resumes for the next 24-hour cycle, it restarts at the last known good document. In this way, an indexer can work its way through a document backlog over a series of days until all unprocessed documents are processed. For more information about setting schedules in general, see [Create Indexer REST API](/rest/api/searchservice/Create-Indexer) or see [How to schedule indexers for Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

## Parallel indexers

If you have partitioned data, you can create indexer-data-source combinations that pull from each data source and write to the same search index. Because each indexer is distinct, you can run them at the same time, populating a search index more quickly than if you ran them sequentially.

There are some risks associated with parallel indexing. First, recall that indexing does not run in the background, increasing the likelihood that queries will be throttled or dropped. Second, Azure Cognitive Search does not lock the index for updates. Concurrent writes are managed, invoking a retry if a particular write does not succeed on first attempt, but you might notice an increase in indexing failures.

The number of indexing jobs that can run simultaneously varies for text-based and skills-based indexing. For more information, see [Indexer execution](search-howto-run-reset-indexers.md#indexer-execution).

1. For text-based indexing, [sign in to Azure portal](https://portal.azure.com) and check the number of search units used by your search service. Select **Settings** > **Scale** to view the number at the top of the page. The number of indexers that will run in parallel is approximately equal to the number of search units. 

1. Partition source data among multiple containers or multiple virtual folders inside the same container.

1. Map each partition to its own [data source](/rest/api/searchservice/create-data-source), paired to its own [indexer](/rest/api/searchservice/create-indexer).

1. Specify the same target search index in each indexer.

1. Schedule the indexers. Review indexer status and execution history for confirmation.

Although multiple indexer-data-source sets can target the same index, be careful of indexer runs that can overwrite existing values in the index. If a second indexer-data-source targets the same documents and fields, any values from the first run will be overwritten. Field values are replaced in full; an indexer cannot merge values from multiple runs into the same field.

If you are pulling from different data source types, a challenge for this scenario lies in designing an index schema that works for all incoming data, and a document key structure that is uniform in the search index. Natively, the values that uniquely identify a document are metadata_storage_path in a blob container and a primary key in a SQL table. You can imagine that one or both sources must be amended to provide key values in a common format, regardless of content origin. For this scenario, you should expect to perform some level of pre-processing to homogenize the data so that it can be pulled into a single index.  

## See also

+ [Indexer overview](search-indexer-overview.md)
+ [Create an indexer](search-howto-create-indexers.md)
+ [Monitor indexer status](search-howto-monitor-indexers.md)
+ [Performance analysis](search-performance-analysis.md)
