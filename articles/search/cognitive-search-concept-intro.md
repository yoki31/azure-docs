---
title: AI enrichment concepts
titleSuffix: Azure Cognitive Search
description: Content extraction, natural language processing (NLP), and image processing are used to create searchable content in Azure Cognitive Search indexes. AI enrichment can use both pre-defined cognitive skills and custom AI algorithms.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2022
ms.custom: references_regions
---
# AI enrichment in Azure Cognitive Search

*AI enrichment* is the application of machine learning models over raw content, where analysis and inference are used to create searchable content and structure where none previously existed. Because Azure Cognitive Search is a full text search solution, the purpose of AI enrichment is to improve the utility of your content in search-related scenarios:

+ Machine translation and language detection support multi-lingual search
+ Entity recognition finds people, places, and other entities in large chunks of text
+ Key phrase extraction identifies and then aggregates important terms
+ Optical Character Recognition (OCR) extracts text from binary files
+ Image analysis tags and describes images in searchable text fields

AI enrichment is an extension of an [**indexer**](search-indexer-overview.md) pipeline.

[**Blobs in Azure Storage**](../storage/blobs/storage-blobs-overview.md) are the most common data input, but any supported data source can provide the initial content. A [**skillset**](cognitive-search-working-with-skillsets.md), attached to an indexer, adds the AI processing. The indexer extracts content and sets up the pipeline. The skillset performs the enrichment steps. Output is always a [**search index**](search-what-is-an-index.md), and optionally a [**knowledge store**](knowledge-store-concept-intro.md).

![Enrichment pipeline diagram](./media/cognitive-search-intro/cogsearch-architecture.png "enrichment pipeline overview")

Skillsets are composed of [*built-in skills*](cognitive-search-predefined-skills.md) from Cognitive Search or [*custom skills*](cognitive-search-create-custom-skill-example.md) for external processing that you provide. Custom skills aren’t always complex. For example, if you have an existing package that provides pattern matching or a document classification model, you can wrap it in a custom skill. 

Built-in skills fall into these categories:

+ **Machine translation** is provided by the [Text Translation](cognitive-search-skill-text-translation.md) skill, often paired with [language detection](cognitive-search-skill-language-detection.md) for multi-language solutions.

+ **Image processing** skills include [Optical Character Recognition (OCR)](cognitive-search-skill-ocr.md) and identification of [visual features](cognitive-search-skill-image-analysis.md), such as facial detection, image interpretation, image recognition (famous people and landmarks), or attributes like image orientation. These skills create text representations of image content for full text search in Azure Cognitive Search.

+ **Natural language processing** skills include [Entity Recognition](cognitive-search-skill-entity-recognition-v3.md), [Language Detection](cognitive-search-skill-language-detection.md), [Key Phrase Extraction](cognitive-search-skill-keyphrases.md), text manipulation, [Sentiment Detection (including opinion mining)](cognitive-search-skill-sentiment-v3.md), and [Personal Identifiable Information Detection](cognitive-search-skill-pii-detection.md). With these skills, unstructured text is mapped as searchable and filterable fields in an index.

Built-in skills are based on the Cognitive Services APIs: [Computer Vision](../cognitive-services/computer-vision/index.yml) and [Language Service](../cognitive-services/language-service/overview.md). Unless your content input is small, expect to [attach a billable Cognitive Services resource](cognitive-search-attach-cognitive-services.md) to run larger workloads.

## Availability and pricing

AI enrichment is available in regions that have Azure Cognitive Services. You can check the availability of AI enrichment on the [Azure products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=search) page. AI enrichment is available in all regions except:

+ Australia Southeast
+ China North 2
+ Germany West Central

Billing follows a pay-as-you-go pricing model. The costs of using built-in skills are passed on when a multi-region Cognitive Services key is specified in the skillset. There are also costs associated with image extraction, as metered by Cognitive Search. Text extraction and utility skills, however, aren't billable. For more information, see [How you're charged for Azure Cognitive Search](search-sku-manage-costs.md#how-youre-charged-for-azure-cognitive-search).

## When to use AI enrichment

Enrichment is useful if raw content is unstructured text, image content, or content that needs language detection and translation. Applying AI through the built-in cognitive skills can unlock this content for full text search and data science applications.

Enrichment also unlocks external processing. Open-source, third-party, or first-party code can be integrated into the pipeline as a custom skill. Classification models that identify salient characteristics of various document types fall into this category, but any external package that adds value to your content could be used.

### Use-cases for built-in skills

A [skillset](cognitive-search-defining-skillset.md) that's assembled using built-in skills is well suited for the following application scenarios:

+ [Optical Character Recognition (OCR)](cognitive-search-skill-ocr.md) that recognizes typeface and handwritten text in scanned documents (JPEG) is perhaps the most commonly used skill. 

+ [Text translation](cognitive-search-skill-text-translation.md) of multilingual content is another commonly used skill. Language detection is built into Text Translation, but you can also run [Language Detection](cognitive-search-skill-language-detection.md) as a separate skill to output a language code for each chunk of content.

+ PDFs with combined image and text. Embedded text can be extracted without AI enrichment, but adding image and language skills can unlock more information than what could be obtained through standard text-based indexing.

+ Unstructured or semi-structured documents containing content that has inherent meaning or organization that is hidden in the larger document. 

  Blobs in particular often contain a large body of content that is packed into a single "field". By attaching image and natural language processing skills to an indexer, you can create information that is extant in the raw content, but not otherwise surfaced as distinct fields. 

  Some ready-to-use built-in cognitive skills that can help: [Key Phrase Extraction](cognitive-search-skill-keyphrases.md) and [Entity Recognition](cognitive-search-skill-entity-recognition-v3.md) (people, organizations, and locations to name a few).

  Additionally, built-in skills can also be used restructure content through text split, merge, and shape operations.

### Use-cases for custom skills

Custom skills can support more complex scenarios, such as recognizing forms, or custom entity detection using a model that you provide and wrap in the [custom skill web interface](cognitive-search-custom-skill-interface.md). Several examples of custom skills include:

+ [Forms Recognizer](../applied-ai-services/form-recognizer/overview.md)
+ [Bing Entity Search API](./cognitive-search-create-custom-skill-example.md)
+ [Custom entity recognition](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)

## Enrichment steps <a name="enrichment-steps"></a>

An enrichment pipeline consists of [*indexers*](search-indexer-overview.md) that have [*skillsets*](cognitive-search-working-with-skillsets.md). A skillset defines the enrichment steps, and the indexer drives the skillset. When configuring an indexer, you can include properties like output field mappings that send enriched content to a [search index](search-what-is-an-index.md) or projections that define data structures in a [knowledge store](knowledge-store-concept-intro.md).

Post-indexing, you can access content via search requests through all [query types supported by Azure Cognitive Search](search-query-overview.md).

### Step 1: Connection and document cracking phase

Indexers connect to external sources using information provided in an indexer data source. When the indexer connects to the resource, it will ["crack documents"](search-indexer-overview.md#document-cracking) to extract text and images.Image content can be routed to skills that perform image processing, while text content is queued for text processing. 

![Document cracking phase](./media/cognitive-search-intro/document-cracking-phase-blowup.png "document cracking")

This step assembles all of the initial or raw content that will undergo AI enrichment. For each document, an enrichment tree is created. Initially, the tree is just a root node representation, but it will grow and gain structure during skillset execution.

### Step 2: Skillset enrichment phase

A skillset defines the atomic operations that are performed on each document. For example, for text and images extracted from a PDF, a skillset might apply entity recognition, language detection, or key phrase extraction to produce new fields in your index that aren’t available natively in the source. 

![Enrichment phase](./media/cognitive-search-intro/enrichment-phase-blowup.png "enrichment phase")

 skillset can be minimal or highly complex, and determines not only the type of processing, but also the order of operations. Most skillsets contain about three to five skills.

A skillset, plus the [output field mappings](cognitive-search-output-field-mapping.md) defined as part of an indexer, fully specifies the enrichment pipeline. For more information about pulling all of these pieces together, see [Define a skillset](cognitive-search-defining-skillset.md).

Internally, the pipeline generates a collection of enriched documents. You can decide which parts of the enriched documents should be mapped to indexable fields in your search index. For example, if you applied the key phrase extraction and the entity recognition skills, those new fields would become part of the enriched document, and can be mapped to fields on your index. See [Annotations](cognitive-search-concept-annotations-syntax.md) to learn more about input/output formations.

### Step 3: Indexing

Indexing is the process wherein raw and enriched content is ingested as fields in a search index, and as [projections](knowledge-store-projection-overview.md) if you're also creating a knowledge store. The same enriched content can appear in both, using implicit or explicit field mappings to send the content to the correct fields.

Enriched content is generated during skillset execution, and is temporary unless you save it. In order for enriched content to appear in a search index, the indexer must have mapping information so that it can send enriched content to a field in a search index. [Output field mappings](cognitive-search-output-field-mapping.md) set up these associations.

## Storing enriched output

In Azure Cognitive Search, an indexer saves the output it creates.

A [**searchable index**](search-what-is-an-index.md) is one of the outputs that is always created by an indexer. Specification of an index is an indexer requirement, and when you attach a skillset, the output of the skillset, plus any fields that are mapped directly from the source, are used to populate the index. Usually, the outputs of specific skills, such as key phrases or sentiment scores, are ingested into the index in fields created for that purpose.

A [**knowledge store**](knowledge-store-concept-intro.md) is an optional output, used for downstream apps like knowledge mining. A knowledge store is defined within a skillset. Its definition determines whether your enriched documents are projected as tables or objects (files or blobs). Tabular projections are recommended for interactive analysis in tools like Power BI. Files and blobs are typically used in data science or similar workloads.

Finally, an indexer can [**cache enriched documents**](cognitive-search-incremental-indexing-conceptual.md) in Azure Blob Storage for potential reuse in subsequent skillset executions. The cache is for internal use. Cached enrichments are consumable by the same skillset that you rerun at a later date. Caching is helpful if your skillset include image analysis or OCR, and you want to avoid the time and expense of reprocessing image files.

Indexes and knowledge stores are fully independent of each other. While you must attach an index to satisfy indexer requirements, if your sole objective is a knowledge store, you can ignore the index after it's populated. Avoid deleting it though. If you want to rerun the indexer and skillset, you'll need the index in order for the indexer to run.

## Consuming enriched content

The output of AI enrichment is either a [fully text-searchable index](search-what-is-an-index.md) on Azure Cognitive Search, or a [knowledge store](knowledge-store-concept-intro.md) in Azure Storage.

### Check content in a search index

[Run queries](search-query-overview.md) to access the enriched content generated by the pipeline. The index is like any other you might create for Azure Cognitive Search: you can supplement text analysis with custom analyzers, invoke fuzzy search queries, add filters, or experiment with scoring profiles to tune search relevance.

### Check content in a knowledge store

In Azure Storage, a [knowledge store](knowledge-store-concept-intro.md) can assume the following forms: a blob container of JSON documents, a blob container of image objects, or tables in Table Storage. You can use [Storage Browser](knowledge-store-view-storage-explorer.md), [Power BI](knowledge-store-connect-power-bi.md), or any app that connects to Azure Storage to access your content.

+ A blob container captures enriched documents in their entirety, which is useful if you're creating a feed into other processes. 

+ A table is useful if you need slices of enriched documents, or if you want to include or exclude specific parts of the output. For analysis in Power BI, tables are the recommended data source for data exploration and visualization in Power BI.

## Checklist: A typical workflow

1. When beginning a project, it's helpful to work with a subset of data. Indexer and skillset design is an iterative process, and the work goes faster with a small representative data set.

1. Create a [data source](/rest/api/searchservice/create-data-source) that specifies a connection to your data.

1. Create a [skillset](/rest/api/searchservice/create-skillset) to add enrichment.

1. Create an [index schema](/rest/api/searchservice/create-index) that defines a search index.

1. Create an [indexer](/rest/api/searchservice/create-indexer) to bring all of the above components together. This step retrieves the data, runs the skillset, and loads the index.

1. Run queries to evaluate results and modify code to update skillsets, schema, or indexer configuration.

To repeat any of the above steps, [reset the indexer](search-howto-reindex.md) before you run it. Or, delete and recreate the objects on each run (recommended if you’re using the free tier). You should also [enable enrichment caching](cognitive-search-incremental-indexing-conceptual.md) to reuse existing enrichments wherever possible.

## Next steps

+ [Quickstart: Create a text translation and entity skillset](cognitive-search-quickstart-blob.md)
+ [Quickstart: Create an OCR image skillset](cognitive-search-quickstart-ocr.md)
+ [Tutorial: Learn about the AI enrichment REST APIs](cognitive-search-tutorial-blob.md)
+ [Skillset concepts](cognitive-search-working-with-skillsets.md)
+ [Knowledge store concepts](knowledge-store-concept-intro.md)
+ [Create a skillset](cognitive-search-defining-skillset.md)
+ [Create a knowledge store](knowledge-store-create-rest.md)