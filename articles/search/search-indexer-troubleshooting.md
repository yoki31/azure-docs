---
title: Indexer troubleshooting guidance
titleSuffix: Azure Cognitive Search
description: This article provides indexer problem and resolution guidance for cases when no error messages are returned from the service search. 

manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2021
---

# Indexer troubleshooting guidance for Azure Cognitive Search

Occasionally, indexers run into problems and there is no error to help with diagnosis. This article covers problems and potential resolutions when indexer results are unexpected and there is limited information to go on. If you have an error to investigate, see [Troubleshooting common indexer errors and warnings](cognitive-search-common-errors-warnings.md) instead.

<a name="connection-errors"></a>

## Troubleshoot connections to restricted resources

For data sources that are secured by Azure network security mechanisms, indexers have a limited set of options for making the connection. Currently, indexers can access restricted data sources [behind an IP firewall](search-indexer-howto-access-ip-restricted.md) or on a virtual network through a [private endpoint](search-indexer-howto-access-private.md).

### Firewall rules

Azure Storage, Cosmos DB and Azure SQL provide a configurable firewall. There's no specific error message when the firewall is enabled. Typically, firewall errors are generic and look like `The remote server returned an error: (403) Forbidden` or `Credentials provided in the connection string are invalid or have expired`.

There are two options for allowing indexers to access these resources in such an instance:

* Disable the firewall, by allowing access from **All Networks** (if feasible).

* Alternatively, you can allow access for the IP address of your search service and the IP address range of `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) in the firewall rules of your resource (IP address range restriction).

Details for configuring IP address range restrictions for each data source type can be found from the following links:

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Limitation**: As stated in the documentation above for Azure Storage, IP address range restrictions will only work if your search service and your storage account are in different regions.

Azure functions (that could be used as a [Custom Web Api skill](cognitive-search-custom-skill-web-api.md)) also support [IP address restrictions](../azure-functions/ip-addresses.md#ip-address-restrictions). The list of IP addresses to configure would be the IP address of your search service and the IP address range of `AzureCognitiveSearch` service tag.

For more information about connecting to a virtual machine, see [Configure a connection to SQL Server on an Azure VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### Configure network security group (NSG) rules

When accessing data in a SQL managed instance, or when an Azure VM is used as the web service URI for a [Custom Web Api skill](cognitive-search-custom-skill-web-api.md), customers need not be concerned with specific IP addresses.

In such cases, the Azure VM, or the SQL managed instance can be configured to reside within a virtual network. Then a network security group can be configured to filter the type of network traffic that can flow in and out of the virtual network subnets and network interfaces.

The `AzureCognitiveSearch` service tag can be directly used in the inbound [NSG rules](../virtual-network/manage-network-security-group.md#work-with-security-rules) without needing to look up its IP address range.

More details for accessing data in a SQL managed instance are outlined [here](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## Azure SQL Database serverless indexing (error code 40613)

If your SQL database is on a [serverless compute tier](../azure-sql/database/serverless-tier-overview.md), make sure that the database is running (and not paused) when the indexer connects to it.

If the database is paused, the first login from your search service will auto-resume the database, but it will also return an error stating that the database is unavailable with error code 40613. After the database is running, retry the login to establish connectivity.

## SharePoint Conditional Access policies

When creating a SharePoint indexer you will go through a step that requires you to sign in to your Azure AD app after providing a device code. If you receive a message that says `"Your sign-in was successful but your admin requires the device requesting access to be managed"` the indexer is likely being blocked from accessing the SharePoint document library due to a [Conditional Access](../active-directory/conditional-access/overview.md) policy.

To update the policy to allow the indexer access to the document library, follow the below steps:

1. Open the Azure portal and search **Azure AD Conditional Access**, then select **Policies** on the left menu. If you don't have access to view this page you will need to either find someone who has access or get access.

1. Determine which policy is blocking the SharePoint indexer from accessing the document library. The policy that might be blocking the indexer will include the user account that you used to authenticate during the indexer creation step in the **Users and groups** section. The policy also might have **Conditions** that:
    * Restrict **Windows** platforms.
    * Restrict **Mobile apps and desktop clients**.
    * Have **Device state** configured to **Yes**.

1. Once you've confirmed there is a policy that is blocking the indexer, you next need to make an exemption for the indexer. Retrieve the search service IP address.

    1. Obtain the fully qualified domain name (FQDN) of your search service. This will look like `<search-service-name>.search.windows.net`. You can find out the FQDN by looking up your search service on the Azure portal.

   ![Obtain service FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "Obtain service FQDN")

    The IP address of the search service can be obtained by performing a `nslookup` (or a `ping`) of the FQDN. In the example below, you would add "150.0.0.1" to an inbound rule on the Azure Storage firewall. It might take up to 15 minutes after the firewall settings have been updated for the search service indexer to be able to access the Azure Storage account.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Get the IP address ranges for the indexer execution environment for your region.

    Additional IP addresses are used for requests that originate from the indexer's [multi-tenant execution environment](search-indexer-securing-resources.md#indexer-execution-environment). You can get this IP address range from the service tag.

    The IP address ranges for the `AzureCognitiveSearch` service tag can be either obtained via the [discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) or the [downloadable JSON file](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    For this walkthrough, assuming the search service is the Azure Public cloud, the [Azure Public JSON file](https://www.microsoft.com/download/details.aspx?id=56519) should be downloaded.

   ![Download JSON file](media\search-indexer-troubleshooting\service-tag.png "Download JSON file")

    From the JSON file, assuming the search service is in West Central US, the list of IP addresses for the multi-tenant indexer execution environment are listed below.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Back on the Conditional Access page in Azure portal, select **Named locations** from the menu on the left, then select **+ IP ranges location**. Give your new named location a name and add the IP ranges for your search service and indexer execution environments that you collected in the last two steps.
    * For your search service IP address you may need to add "/32" to the end of the IP address since it only accepts valid IP ranges.
    * Remember that for the indexer execution environment IP ranges, you only need to add the IP ranges for the region that your search service is in.

1. Exclude the new Named location from the policy. 
    1. Select **Policies** on the left menu. 
    1. Select the policy that is blocking the indexer.
    1. Select **Conditions**.
    1. Select **Locations**.
    1. Select **Exclude** then add the new Named location.
    1. **Save** the changes.

1. Wait a few minutes for the policy to update and enforce the new policy rules.

1. Attempt to create the indexer again
    1. Send an update request for the data source object that you created.
    1. Resend the indexer create request. Use the new code to sign in, then send another indexer creation request.

## Indexing unsupported document types

If you are indexing content from Azure Blob Storage, and the container includes blobs of an [unsupported content type](search-howto-indexing-azure-blob-storage.md#SupportedFormats), the indexer will skip that document. In other cases, there may be problems with individual documents. 

You can [set configuration options](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) to allow indexer processing to continue in the event of problems with individual documents.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

## Missing documents

Indexers extract documents or rows from an external [data source](/rest/api/searchservice/create-data-source) and create *search documents* which are then indexed by the search service. Occasionally, a document that exists in data source fails to appear in a search index. This unexpected result can occur due to the following reasons:

* The document was updated after the indexer was run. If your indexer is on a [schedule](/rest/api/searchservice/create-indexer#indexer-schedule), it will eventually rerun and pick up the document.
* The indexer timed out before the document could be ingested. There are [maximum processing time limits](search-limits-quotas-capacity.md#indexer-limits) after which no documents will be processed. You can check indexer status in the portal or by calling [Get Indexer Status (REST API)](/rest/api/searchservice/get-indexer-status).
* [Field mappings](/rest/api/searchservice/create-indexer#fieldmappings) or [AI enrichment](./cognitive-search-concept-intro.md) have changed the document and its articulation in the search index is different from what you expect.
* [Change tracking](/rest/api/searchservice/create-data-source#data-change-detection-policies) values are erroneous or prerequisites are missing. If your high watermark value is a date set to a future time, then any documents that have a date less than this will be skipped by the indexer. You can understand your indexer's change tracking state using the 'initialTrackingState' and 'finalTrackingState' fields in the [indexer status](/rest/api/searchservice/get-indexer-status#indexer-execution-result). Indexers for Azure SQL and MySQL must have an index on the high water mark column of the source table, or queries used by the indexer may time out. 

> [!TIP]
> If documents are missing, check the [query](/rest/api/searchservice/search-documents) you are using to make sure it isn't excluding the document in question. To query for a specific document, use the [Lookup Document REST API](/rest/api/searchservice/lookup-document).

## Missing content from Blob Storage

The blob indexer [finds and extracts text from blobs in a container](search-howto-indexing-azure-blob-storage.md). Some problems with extracting text include:

* The document only contains scanned images. PDF blobs that have non-text content, such as scanned images (JPGs), don't produce results in a standard blob indexing pipeline. If you have image content with text elements, you can use [OCR or image analysis](cognitive-search-concept-image-scenarios.md) to find and extract the text.

* The blob indexer is configured to only index metadata. To extract content, the blob indexer must be configured to [extract both content and metadata](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## Missing content from Cosmos DB

Azure Cognitive Search has an implicit dependency on Cosmos DB indexing. If you turn off automatic indexing in Cosmos DB, Azure Cognitive Search returns a successful state, but fails to index container contents. For instructions on how to check settings and turn on indexing, see [Manage indexing in Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

## Documents processed multiple times

Indexers leverage a conservative buffering strategy to ensure that every new and changed document in the data source is picked up during indexing. In certain situations, these buffers can overlap, causing an indexer to index a document two or more times resulting in the processed documents count to be more than actual number of documents in the data source. This behavior does **not** affect the data stored in the index, such as duplicating documents, only that it may take longer to reach eventual consistency. This can be especially prevalent if any of the following conditions are true:

- On-demand indexer requests are issued in quick succession
- The data source's topology includes multiple replicas and partitions (one such example is discussed [here](../cosmos-db/consistency-levels.md))

Indexers are not intended to be invoked multiple times in quick succession. If you need updates quickly, the supported approach is to push updates to the index while simultaneously updating the data source. For on-demand processing, we recommend that you pace your requests in five-minute intervals or more, and run the indexer on a schedule.

### Example of duplicate document processing with 30 second buffer
Conditions under which a document is processed twice is explained below in a timeline that notes each action and counter action. The following timeline illustrates the issue:

| Timeline (hh:mm:ss) | Event | Indexer High Water Mark | Comment |
|---------------------|-------|-------------------------|---------|
| 00:01:00 | Write `doc1` to data source with eventual consistency | `null` | Document timestamp is 00:01:00. |
| 00:01:05 | Write `doc2` to data source with eventual consistency | `null` | Document timestamp is 00:01:05. |
| 00:01:10 | Indexer starts | `null` | |
| 00:01:11 | Indexer queries for all changes before 00:01:10; the replica that the indexer queries happens to be only aware of `doc2`; only `doc2` is retrieved | `null` | Indexer requests all changes before starting timestamp but actually receives a subset. This behavior necessitates the look back buffer period. |
| 00:01:12 | Indexer processes `doc2` for the first time | `null` | |
| 00:01:13 | Indexer ends | 00:01:10 | High water mark is updated to starting timestamp of current indexer execution. |
| 00:01:20 | Indexer starts | 00:01:10 | |
| 00:01:21 | Indexer queries for all changes between 00:00:40 and 00:01:20; the replica that the indexer queries happens to be aware of both `doc1` and `doc2`; retrieves `doc1` and `doc2` | 00:01:10 | Indexer requests for all changes between current high water mark minus the 30 second buffer, and starting timestamp of current indexer execution. |
| 00:01:22 | Indexer processes `doc1` for the first time | 00:01:10 | |
| 00:01:23 | Indexer processes `doc2` for the second time | 00:01:10 | |
| 00:01:24 | Indexer ends | 00:01:20 | High water mark is updated to starting timestamp of current indexer execution. |
| 00:01:32 | Indexer starts | 00:01:20 | |
| 00:01:33 | Indexer queries for all changes between 00:00:50 and 00:01:32; retrieves `doc1` and `doc2` | 00:01:20 | Indexer requests for all changes between current high water mark minus the 30 second buffer, and starting timestamp of current indexer execution. |
| 00:01:34 | Indexer processes `doc1` for the second time | 00:01:20 | |
| 00:01:35 | Indexer processes `doc2` for the third time | 00:01:20 | |
| 00:01:36 | Indexer ends | 00:01:32 | High water mark is updated to starting timestamp of current indexer execution. |
| 00:01:40 | Indexer starts | 00:01:32 | |
| 00:01:41 | Indexer queries for all changes between 00:01:02 and 00:01:40; retrieves `doc2` | 00:01:32 | Indexer requests for all changes between current high water mark minus the 30 second buffer, and starting timestamp of current indexer execution. |
| 00:01:42 | Indexer processes `doc2` for the fourth time | 00:01:32 | |
| 00:01:43 | Indexer ends | 00:01:40 | Notice this indexer execution started more than 30 seconds after the last write to the data source and also processed `doc2`. This is the expected behavior because if all indexer executions before 00:01:35 are eliminated, this will become the first and only execution to process `doc1` and `doc2`. |

In practice, this scenario only happens when on-demand indexers are manually invoked within minutes of each other, for certain data sources. It may result in mismatched numbers (like the indexer processed 345 documents total according to the indexer execution stats, but there are 340 documents in the data source and index) or potentially increased billing if you are running the same skills for the same document multiple times. Running an indexer using a schedule is the preferred recommendation.

## See also

* [Troubleshooting common indexer errors and warnings](cognitive-search-common-errors-warnings.md)
* [Monitor indexer-based indexing](search-howto-monitor-indexers.md)