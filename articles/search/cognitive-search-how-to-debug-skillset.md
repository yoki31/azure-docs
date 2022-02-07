---
title: Debug a skillset
titleSuffix: Azure Cognitive Search
description: Investigate skillset errors and issues by starting a debug session in Azure portal.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.date: 12/31/2021
---

# Debug an Azure Cognitive Search skillset in Azure portal

Start a debug session to identify and resolve errors, validate changes, and push changes to a published skillset in your Azure Cognitive Search service.

A debug session is a cached indexer and skillset execution, scoped to a single document, that you can use to edit and test your changes interactively. If you're unfamiliar with how a debug session works, see [Debug sessions in Azure Cognitive Search](cognitive-search-debug-session.md). To practice a debug workflow with a sample document, see [Tutorial: Debug sessions](cognitive-search-tutorial-debug-sessions.md).

> [!Important]
> Debug sessions is a preview portal feature, provided under [Supplemental Terms of Use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites

+ An existing enrichment pipeline, including a data source, a skillset, an indexer, and an index.

  A debug session works with all generally available [indexer data sources](search-data-sources-gallery.md) and most preview data sources. The MongoDB API (preview) of Cosmos DB is currently not supported.

+ Azure Storage, used to save session state.

## Create a debug session

1. [Sign in to Azure portal](https://portal.azure.com) and find your search service.

1. In the **Overview** page of your search service, select the **Debug Sessions** tab.

1. Select **+ New Debug Session**.

   :::image type="content" source="media/cognitive-search-debug/new-debug-session.png" alt-text="Screenshot of the debug sessions commands in the portal page." border="true":::

1. In **Debug session name**, provide a name that will help you remember which skillset, indexer, and data source the debug session is about.

1. In **Storage connection**, find a general-purpose storage account for caching the debug session. You'll be prompted to select and optionally create a blob container in Blob Storage or Azure Data Lake Storage Gen2. You can reuse the same container for all subsequent debug sessions you create. A helpful container name might be "cognitive-search-debug-sessions".

1. In **Indexer template**, select the indexer that drives the skillset you want to debug. Copies of both the indexer and skillset are used to initialize the session.

1. In **Document to debug**, choose the first document in the index or select a specific document. If you select a specific document, depending on the data source, you'll be asked for a URI or a row ID.

   If your specific document is a blob, you'll be asked for the blob URI. You can find the URL in the blob property page in the portal.

   :::image type="content" source="media/cognitive-search-debug/copy-blob-url.png" alt-text="Screenshot of the URI property in blob storage." border="true":::

1. Optionally, in **Indexer settings**, specify any indexer execution settings used to create the session. The settings should mimic the settings used by the actual indexer. Any indexer options that you specify in a debug session have no effect on the indexer itself.

1. Your configuration should look similar to this screenshot. Select **Save Session** to get started.

   :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Screenshot of a debug session page." border="true":::

The debug session begins by executing the indexer and skillset on the selected document. The document's content and metadata created will be visible and available in the session.

## Start with errors and warnings

Indexer execution history in the portal gives you the full error and warning list for all documents. In a debug session, the errors and warnings will be limited to one document. You'll work through this list, make your changes, and then return to the list to verify whether issues are resolved. 

To view the messages, select a skill in **AI Enrichment > Skill Graph** and then select **Errors/Warnings** in the details pane.

As a best practice, resolve problems with inputs before moving on to outputs.

To prove whether a modification resolves an error, follow these steps:

1. Select **Save** in the skill details pane to preserve your changes.

1. Select **Run** in the session window to invoke skillset execution using the modified definition.

1. Return to **Errors/Warnings** to see if the count is reduced. The list won't be refreshed until you open the tab.

## View content of enrichment nodes

AI enrichment pipelines extract or infer information and structure from source documents, creating an enriched document in the process. An enriched document is first created during document cracking and populated with a root node (`/document`), plus nodes for any content that is lifted directly from the data source, such as metadata and the document key. More nodes are created by skills during skill execution, where each skill output adds a new node to the enrichment tree. 

Enriched documents are internal, but a debug session gives you access to the content produced during skill execution. To view the content or output of each skill, follow these steps:

1. Start with the default views: **AI enrichment > Skill Graph**, with the graph type set to **Dependency Graph**.

1. Select a skill.

1. In the details pane to the right, select **Executions**, select an OUTPUT, and then open the Expression Evaluator (**`</>`**) to view the expression and its result.

   :::image type="content" source="media/cognitive-search-debug/enriched-doc-output-expression.png" alt-text="Screenshot of a skill execution showing output values." border="true":::

1. Alternatively, open **AI enrichment > Enriched Data Structure** to scroll down the list of nodes. The list includes potential and actual nodes, with a column for output, and another column that indicates the upstream object used to produce the output.

   :::image type="content" source="media/cognitive-search-debug/enriched-doc-output.png" alt-text="Screenshot of enriched document showing output values." border="true":::

## Edit skill definitions

If the field mappings are correct, check individual skills for configuration and content. If a skill fails to produce output, it might be missing a property or parameter, which can be determined through error and validation messages. 

Other issues, such as an invalid context or input expression, can be harder to resolve because the error will tell you what is wrong, but not how to fix it. For help with context and input syntax, see [Reference annotations in an Azure Cognitive Search skillset](cognitive-search-concept-annotations-syntax.md#background-concepts). For help with individual messages, see [Troubleshooting common indexer errors and warnings](cognitive-search-common-errors-warnings.md).

The following steps show you how to get information about a skill.

1. In **AI enrichment > Skill Graph**, select a skill. The Skill Details pane opens to the right.

1. Edit a skill definition using either approach:

   + **Skill Settings** if you prefer a visual editor
   + **Skill JSON Editor** to edit the JSON document directly

1. Check the [path syntax for referencing nodes](cognitive-search-concept-annotations-syntax.md) in an enrichment tree. Following are some of the most common input paths:

   + `/document/content` for chunks of text. This node is populated from the blob's content property.
   + `/document/merged_content` for chunks of text in skillets that include Text Merge skill.
   + `/document/normalized_images/*` for text that is recognized or inferred from images.

## Check field mappings

If skills produce output but the search index is empty, check the field mappings. Field mappings specify how content moves out of the pipeline and into a search index.

1. Start with the default views: **AI enrichment > Skill Graph**, with the graph type set to **Dependency Graph**.

1. Select **Field Mappings** near the top. You should find at least the document key that uniquely identifies and associates each search document in the search index with its source document in the data source. 

   If you're importing raw content straight from the data source, bypassing enrichment, you should find those fields in **Field Mappings**.

1. Select **Output Field Mappings** at the bottom of the graph. Here you'll find mappings from skill outputs to target fields in the search index. Unless you used the Import Data wizard, output field mappings are defined manually and could be incomplete or mistyped. 

   Verify that the fields in **Output Field Mappings** exist in the search index as specified, checking for spelling and [enrichment node path syntax](cognitive-search-concept-annotations-syntax.md). 

   :::image type="content" source="media/cognitive-search-debug/output-field-mappings.png" alt-text="Screenshot of the Output Field Mappings node and details." border="true":::

## Next steps

Now that you understand the layout and capabilities of the Debug Sessions visual editor, try the tutorial for a hands-on experience.

> [!div class="nextstepaction"]
> [Tutorial: Explore Debug sessions](./cognitive-search-tutorial-debug-sessions.md)
