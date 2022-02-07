---
title: Preview feature list
titleSuffix: Azure Cognitive Search
description: Preview features are released so that customers can provide feedback on their design and utility. This article is a comprehensive list of all features currently in preview.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/03/2021
---
# Preview features in Azure Cognitive Search

This article is a comprehensive list of all features that are in public preview. Preview functionality is provided without a service level agreement, and is not recommended for production workloads. For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Preview features that transition to general availability are removed from this list. If a feature isn't listed below, you can assume it is generally available. For announcements regarding general availability, see [Service Updates](https://azure.microsoft.com/updates/?product=search) or [What's New](whats-new.md).

|Feature&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Description | Availability  |
|---------|------------------|-------------|---------------|
|  [**Azure Files indexer**](search-file-storage-integration.md) | Indexer data source | Adds REST API support for creating indexers for [Azure Files](https://azure.microsoft.com/services/storage/files/) | Public preview, [Search REST API 2021-04-30-Preview](/rest/api/searchservice/index-preview). Announced in November 2021.  |
| [**Azure RBAC support**](search-security-rbac.md) | Security | Use new built-in roles to control access to indexes and indexing, eliminating or reducing the dependency on API keys. | Public preview ([registration required](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#step-1-preview-sign-up)). After you are registered, use the Azure portal or the Management REST API version 2021-04-01-Preview to configure a search service for data plane authentication. Announced in July 2021. |
| [**Search REST API 2021-04-30-Preview**](/rest/api/searchservice/index-preview) | Security | Modifies [Create or Update Data Source](/rest/api/searchservice/preview-api/create-or-update-data-source) to support managed identities under Azure Active Directory, for indexers that connect to external data sources. | Public preview, [Search REST API 2021-04-30-Preview](/rest/api/searchservice/index-preview). Announced in May 2021.  |
| [**Management REST API 2021-04-01-Preview**](/rest/api/searchmanagement/) | Security | Modifies [Create or Update Service](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) to support new [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions). | Public preview, [Management REST API](/rest/api/searchmanagement/), API version 2021-04-01-Preview. Announced in May 2021. |
| [**Reset Documents**](search-howto-run-reset-indexers.md) | Indexer | Reprocesses individually selected search documents in indexer workloads. | Use the [Reset Documents REST API](/rest/api/searchservice/preview-api/reset-documents), API versions 2021-04-30-Preview or 2020-06-30-Preview. |
|  [**Power Query connectors**](search-how-to-index-power-query-data-sources.md) | Indexer data source | Indexers can now index from other cloud platforms. If you are using an indexer to crawl external data sources for indexing, you can now use Power Query connectors to connect to Amazon Redshift, Elasticsearch, PostgreSQL, Salesforce Objects, Salesforce Reports, Smartsheet, and Snowflake. | [Sign up](https://aka.ms/azure-cognitive-search/indexer-preview) is required so that support can be enabled for your subscription on the backend. Configure this data source using [Create or Update Data Source](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30-Preview or 2020-06-30-Preview, or the Azure portal.|
| [**SharePoint Indexer**](search-howto-index-sharepoint-online.md) | Indexer data source | New data source for indexer-based indexing of SharePoint content. | [Sign up](https://aka.ms/azure-cognitive-search/indexer-preview) is required so that support can be enabled for your subscription on the backend. Configure this data source using [Create or Update Data Source](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30-Preview or 2020-06-30-Preview, or the Azure portal. |
|  [**MySQL indexer data source**](search-howto-index-mysql.md) | Indexer data source | Index content and metadata from Azure MySQL data sources.| [Sign up](https://aka.ms/azure-cognitive-search/indexer-preview) is required so that support can be enabled for your subscription on the backend. Configure this data source using [Create or Update Data Source](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30-Preview or 2020-06-30-Preview, [.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql), and Azure portal. |
| [**Cosmos DB indexer: MongoDB API, Gremlin API**](search-howto-index-cosmosdb.md) | Indexer data source | For Cosmos DB, SQL API is generally available, but MongoDB and Gremlin APIs are in preview. | For MongoDB and Gremlin, [sign up first](https://aka.ms/azure-cognitive-search/indexer-preview) so that support can be enabled for your subscription on the backend. MongoDB data sources can be configured in the portal. Configure this data source using [Create or Update Data Source](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30-Preview or 2020-06-30-Preview. |
| [**Native blob soft delete**](search-howto-index-changed-deleted-blobs.md) | Indexer data source | The Azure Blob Storage indexer in Azure Cognitive Search will recognize blobs that are in a soft deleted state, and remove the corresponding search document during indexing. | Configure this data source using [Create or Update Data Source](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30-Preview or 2020-06-30-Preview. |
| [**Semantic search**](semantic-search-overview.md) | Relevance (scoring) | Semantic ranking of results, captions, and answers. | Configure semantic search using [Search Documents](/rest/api/searchservice/preview-api/search-documents), API versions 2021-04-30-Preview or 2020-06-30-Preview, and Search Explorer (portal). |
| [**speller**](cognitive-search-aml-skill.md) | Query | Optional spelling correction on query term inputs for simple, full, and semantic queries. | [Search Preview REST API](/rest/api/searchservice/preview-api/search-documents), API versions 2021-04-30-Preview or 2020-06-30-Preview, and Search Explorer (portal). |
| [**Normalizers**](search-normalizers.md) | Query |  Normalizers provide simple text pre-processing: consistent casing, accent removal, and ASCII folding, without invoking the full text analysis chain.| Use [Search Documents](/rest/api/searchservice/preview-api/search-documents), API versions 2021-04-30-Preview or 2020-06-30-Preview.|
| [**featuresMode parameter**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | Relevance (scoring) | Relevance score expansion to include details: per field similarity score, per field term frequency, and per field number of unique tokens matched. You can consume these data points in [custom scoring solutions](https://github.com/Azure-Samples/search-ranking-tutorial). | Add this query parameter using [Search Documents](/rest/api/searchservice/preview-api/search-documents), API versions 2021-04-30-Preview, 2020-06-30-Preview, or 2019-05-06-Preview. |
| [**Azure Machine Learning (AML) skill**](cognitive-search-aml-skill.md) | AI enrichment (skills) | A new skill type to integrate an inferencing endpoint from Azure Machine Learning. Get started with [this tutorial](cognitive-search-tutorial-aml-custom-skill.md). | Use [Search Preview REST API](/rest/api/searchservice/), API versions 2021-04-30-Preview, 2020-06-30-Preview, or 2019-05-06-Preview. Also available in the portal, in skillset design, assuming Cognitive Search and Azure ML services are deployed in the same subscription. |
| [**Incremental enrichment**](cognitive-search-incremental-indexing-conceptual.md) | AI enrichment (skills) | Adds caching to an enrichment pipeline, allowing you to reuse existing output if a targeted modification, such as an update to a skillset or another object, does not change the content. Caching applies only to enriched documents produced by a skillset.| Add this configuration setting using [Create or Update Indexer Preview REST API](/rest/api/searchservice/create-indexer), API versions 2021-04-30-Preview, 2020-06-30-Preview, or 2019-05-06-Preview. |
| [**Debug Sessions**](cognitive-search-debug-session.md) | Portal, AI enrichment (skills) | An in-session skillset editor used to investigate and resolve issues with a skillset. Fixes applied during a debug session can be saved to a skillset in the service. | Portal only, using mid-page links on the Overview page to open a debug session. |
| [**moreLikeThis**](search-more-like-this.md) | Query | Finds documents that are relevant to a specific document. This feature has been in earlier previews. | Add this query parameter in [Search Documents Preview REST API](/rest/api/searchservice/search-documents) calls, with API versions 2021-04-30-Preview, 2020-06-30-Preview, 2019-05-06-Preview, 2016-09-01-Preview, or 2017-11-11-Preview. |

## How to call a preview REST API

Azure Cognitive Search always pre-releases experimental features through the REST API first, then through prerelease versions of the .NET SDK.

Preview features are available for testing and experimentation, with the goal of gathering feedback on feature design and implementation. For this reason, preview features can change over time, possibly in ways that break backwards compatibility. This is in contrast to features in a GA version, which are stable and unlikely to change with the exception of small backward-compatible fixes and enhancements. Also, preview features do not always make it into a GA release.

While some preview features might be available in the portal and .NET SDK, the REST API always has preview features.

+ For search operations, [**`2021-04-30-Preview`**](/rest/api/searchservice/index-preview) is the current preview version.

+ For management operations, [**`2021-04-01-Preview`**](/rest/api/searchmanagement/management-api-versions) is the current preview version.

Older previews are still operational but become stale over time. If your code calls `api-version=2019-05-06-Preview` or `api-version=2016-09-01-Preview` or `api-version=2017-11-11-Preview`, those calls are still valid, but those versions will not include new features and bug fixes are not guaranteed.

The following example syntax illustrates a call to the preview API version.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2021-04-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Azure Cognitive Search service is available in multiple versions and client libraries. For more information, see [API versions](search-api-versions.md).

## Next steps

Review the Search REST Preview API reference documentation. If you encounter problems, ask us for help on [Stack Overflow](https://stackoverflow.com/) or [contact support](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Search service REST API Reference (Preview)](/rest/api/searchservice/index-preview)
