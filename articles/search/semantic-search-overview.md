---
title: Semantic search
titleSuffix: Azure Cognitive Search
description: Learn how Cognitive Search is using deep learning semantic search models from Bing to make search results more intuitive.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2022
ms.custom: references_regions
---
# Semantic search in Azure Cognitive Search

> [!IMPORTANT]
> Semantic search is in public preview under [supplemental terms of use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). It's available through the Azure portal, preview REST API, and beta SDKs. These features are billable (see [Availability and pricing](semantic-search-overview.md#availability-and-pricing)).

Semantic search is a collection of query-related capabilities that bring semantic relevance and language understanding to search results. This article is a high-level introduction to semantic search. The embedded video describes the technology, and the section at the end covers availability and pricing.

Semantic search is a premium feature. We recommend this article for background, but if you'd rather get started, follow these steps:

> [!div class="checklist"]
> * [Check regional and service tier requirements](#availability-and-pricing).
> * [Enable semantic search](#enable-semantic-search) on your search service.
> * Create or modify queries to [return semantic captions and highlights](semantic-how-to-query-request.md).
> * Add a few more query properties to also [return semantic answers](semantic-answers.md).

## What is semantic search?

Semantic search is a collection of features that improve the quality of search results. When enabled on your search service, it extends the query execution pipeline in two ways. First, it adds secondary ranking over an initial result set, promoting the most semantically relevant results to the top of the list. Second, it extracts and returns captions and answers in the response, which you can render on a search page to improve the user's search experience.

| Feature | Description |
|---------|-------------|
| [Semantic re-ranking](semantic-ranking.md) | Uses the context or semantic meaning of a query to compute a new relevance score over existing results. |
| [Semantic captions and highlights](semantic-how-to-query-request.md) | Extracts sentences and phrases from a document that best summarize the content, with highlights over key passages for easy scanning. Captions that summarize a result are useful when individual content fields are too dense for the results page. Highlighted text elevates the most relevant terms and phrases so that users can quickly determine why a match was considered relevant. |
| [Semantic answers](semantic-answers.md) | An optional and additional substructure returned from a semantic query. It provides a direct answer to a query that looks like a question. It requires that a document have text with the characteristics of an answer. |

## How semantic ranking works

*Semantic ranking* looks for context and relatedness among terms, elevating matches that make more sense given the query. Language understanding finds summarizations or *captions* and *answers* within your content and includes them in the response, which can then be rendered on a search results page for a more productive search experience.

State-of-the-art pretrained models are used for summarization and ranking. To maintain the fast performance that users expect from search, semantic summarization and ranking are applied to just the top 50 results, as scored by the [default similarity scoring algorithm](index-similarity-and-scoring.md#similarity-ranking-algorithms). Using those results as the document corpus, semantic ranking re-scores those results based on the semantic strength of the match.

The underlying technology is from Bing and Microsoft Research, and integrated into the Cognitive Search infrastructure as an add-on feature. For more information about the research and AI investments backing semantic search, see [How AI from Bing is powering Azure Cognitive Search (Microsoft Research Blog)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

The following video provides an overview of the capabilities.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

### Order of operations

Components of semantic search extend the existing query execution pipeline in both directions. If you enable spelling correction, the [speller](speller-how-to-add.md) corrects typos at query onset, before terms reach the search engine.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Semantic components in query execution" border="true":::

Query execution proceeds as usual, with term parsing, analysis, and scans over the inverted indexes. The engine retrieves documents using token matching, and scores the results using the [default similarity scoring algorithm](index-similarity-and-scoring.md#similarity-ranking-algorithms). Scores are calculated based on the degree of linguistic similarity between query terms and matching terms in the index. If you defined them, scoring profiles are also applied at this stage. Results are then passed to the semantic search subsystem.

In the preparation step, the document corpus returned from the initial result set is analyzed at the sentence and paragraph level to find passages that summarize each document. In contrast with keyword search, this step uses machine reading and comprehension to evaluate the content. Through this stage of content processing, a semantic query returns [captions](semantic-how-to-query-request.md) and [answers](semantic-answers.md). To formulate them, semantic search uses language representation to extract and highlight key passages that best summarize a result. If the search query is a question - and answers are requested - the response will also include a text passage that best answers the question, as expressed by the search query. 

For both captions and answers, existing text is used in the formulation. The semantic models do not compose new sentences or phrases from the available content, nor does it apply logic to arrive at new conclusions. In short, the system will never return content that doesn't already exist.

Results are then re-scored based on the [conceptual similarity](semantic-ranking.md) of query terms.

To use semantic capabilities in queries, you'll need to make small modifications to the [search request](semantic-how-to-query-request.md), but no extra configuration or reindexing is required.

## Semantic capabilities and limitations

Semantic search is a newer technology so it's important to set expectations about what it can and cannot do. What it can do is improve the quality of search by:

* Promoting matches that are semantically closer to the intent of original query.

* Finding strings in each result that can be used as captions, and potentially answers, that can be rendered on a search results page.

What it can't do is rerun the query over the entire corpus to find semantically relevant results. Semantic search re-ranks the *existing* result set, consisting of the top 50 results as scored by the default ranking algorithm. Furthermore, semantic search cannot create new information or strings. Captions and answers are extracted verbatim from your content so if the results do not include answer-like text, the language models will not produce one.

Although semantic search is not beneficial in every scenario, certain content can benefit significantly from its capabilities. The language models in semantic search work best on searchable content that is information-rich and structured as prose. A knowledge base, online documentation, or documents that contain descriptive content see the most gains from semantic search capabilities.

## Availability and pricing

Semantic search and spell check are available on services that meet the criteria in the table below. To use semantic search, your first need to [enable the capabilities](#enable-semantic-search) on your search service.

| Feature | Tier | Region | Sign up | Pricing |
|---------|------|--------|---------------------|-------------------|
| Semantic search (rank, captions, highlights, answers) | Standard tier (S1, S2, S3) | Australia East, East US, East US 2, North Central US, South Central US, West US, West US 2, North Europe, UK South, West Europe | Required | [Cognitive Search pricing page](https://azure.microsoft.com/pricing/details/search/)  |
| Spell check | Basic<sup>1</sup> and above  | All | None | None (free) |

<sup>1</sup> Due to the provisioning mechanisms and lifespan of shared (free) search services, a small number of services happen to have spell check on the free tier. However, spell check availability on free tier services is not guaranteed and should not be expected.

Charges for semantic search are levied when query requests include "queryType=semantic" and the search string is not empty (for example, "search=pet friendly hotels in New York". If your search string is empty ("search=*"), you won't be charged, even if the queryType is set to "semantic".

## Enable semantic search

By default, semantic search is disabled on all services. To enable semantic search for your search service:

1. Open the [Azure portal](https://portal.azure.com).
1. Navigate to your Standard tier search service.
1. On the left-nav pane, select **Semantic Search (Preview)**.
1. Select either the **Free plan** or the **Standard plan**. You can switch between the free plan and the standard plan at any time.

:::image type="content" source="media/semantic-search-overview/semantic-search-billing.png" alt-text="Screenshot of enabling semantic search in the Azure portal" border="true":::

 Semantic Search's free plan is capped at 1,000 queries per month. After the first 1,000 queries in the free plan, you'll receive an error message letting you know you've exhausted your quota whenever you issue a semantic query. When this happens, you'll need to upgrade to the standard plan to continue using semantic search.

Alternatively, you can also enable semantic search using the [Create or Update Service API](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch) that's described in the next section.

## Disable semantic search

For full protection against accidental usage and charges, you can [disable semantic search](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch)  using the Create or Update Service API on your search service. After the feature is disabled, any requests that include the semantic query type will be rejected.

* Management REST API version 2021-04-01-Preview provides this option

* Owner or Contributor permissions are required to disable features

```http
PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "semanticSearch": "disabled"
      }
    }
```

To re-enable semantic search, rerun the above request, setting "semanticSearch" to either "free" (default) or "standard".

> [!TIP]
> Management REST API calls are authenticated through Azure Active Directory. For guidance on setting up a security principle and a request, see this blog post [Azure REST APIs with Postman (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/). The previous example was tested using the instructions and Postman collection provided in the blog post.

## Next steps

[Enable semantic search](#enable-semantic-search) for your search service and follow the documentation on how to [create a semantic query](semantic-how-to-query-request.md) so that you can test out semantic search on your content.
