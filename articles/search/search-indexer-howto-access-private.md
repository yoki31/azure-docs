---
title: Connect through a private endpoint
titleSuffix: Azure Cognitive Search
description: Configure indexer connections to access content from other Azure resources that are protected through a private endpoint.

manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: how-to
ms.date: 08/13/2021
---

# Make indexer connections through a private endpoint

Many Azure resources, such as Azure storage accounts, can be configured to accept connections from a list of virtual networks and refuse outside connections that originate from a public network. If you're using an indexer to index data in Azure Cognitive Search, and your data source is on a private network, you can create an outbound [private endpoint connection](../private-link/private-endpoint-overview.md) to reach the data.

This indexer connection method is subject to the following two requirements:

+ The Azure resource that provides content or code must be previously registered with the [Azure Private Link service](https://azure.microsoft.com/services/private-link/).

+ The Azure Cognitive Search service must be on the Basic tier or higher. The feature isn't available on the Free tier. Additionally, if your indexer has a skillset, the tier must be Standard 2 (S2) or higher. For more information, see [Service limits](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## Shared private link resources management APIs

Private endpoints of secured resources that are created through Azure Cognitive Search APIs are referred to as *shared private link resources* or *managed outbound private endpoints*. This is because you're "sharing" access to a resource, such as a storage account, that has been integrated with the [Azure Private Link service](https://azure.microsoft.com/services/private-link/). The shared private link is the mechanism by which Azure Cognitive Search makes the connection to resources in a private network.

Through its Management REST API, Azure Cognitive Search provides a [CreateOrUpdate](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) operation that you can use to configure access from an Azure Cognitive Search indexer.

You can create private endpoint connections to some resources only by using the preview version of the Search Management API (version *2020-08-01-preview* or later), which is designated *preview* in the following table. Resources without a *preview* designation can be created with either the preview or generally available API version (*2020-08-01* or later).

The following table lists Azure resources for which you can create outbound private endpoints from Azure Cognitive Search. To create a shared private link resource, enter the **Group ID** values exactly as they're written in the API. The values are case-sensitive.

| Azure resource | Group ID |
| --- | --- |
| Azure Storage - Blob | `blob`|
| Azure Storage - Data Lake Storage Gen2 | `dfs` and `blob` |
| Azure Storage - Tables | `table`|
| Azure Cosmos DB - SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (preview) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (preview) | `sites` |

You can also query the Azure resources for which outbound private endpoint connections are supported by using the [list of supported APIs](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported).

In the remainder of this article, a mix of Azure portal (or the [Azure CLI](/cli/azure/) if you prefer) and [Postman](https://www.postman.com/) (or any other HTTP client like [curl](https://curl.se/) if you prefer) is used to demonstrate the REST API calls.

> [!NOTE]
> There are Azure Cognitive Search data sources and other configurations that require creating particular shared private link resource(s) to work appropriately. For the full list, see **[Additional configuration requirements](#additional-configuration-requirements)**.

## Set up indexer connection through private endpoint

Use the following instructions to set up an indexer connection through a private endpoint to a secure Azure resource. 

### Step 1: Create a shared private link resource to the Azure resource

The following section describes how to create a shared private link resource either using the Azure portal or the Azure CLI. 

#### Option 1: Portal

> [!NOTE]
> Azure portal only supports creating a shared private link resource using **Group ID** values that are generally available. For **[MySQL Private Link (Preview)](../mysql/concepts-data-access-security-private-link.md)** and **[Azure Functions Private Link (Preview)](../azure-functions/functions-networking-options.md)**, use the Azure CLI steps described in **Option 2**, which follows.

To request Azure Cognitive Search to create an outbound private endpoint connection, via the *Shared Private Access* blade, click on "Add Shared Private Access". On the blade that opens on the right, you can choose to "Connect to an Azure resource in my directory" or "Connect to an Azure resource by resource ID or alias".

When using the first option (recommended), the blade will help guide you to pick the appropriate Azure resource and will autofill in other properties such as the **Group ID** of the resource and the resource type.

   ![Screenshot of the "Add Shared Private Access" pane, showing a guided experience for creating a shared private link resource. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource.png)

When using the second option, you can enter the Azure resource ID manually and choose the appropriate **Group ID**. The **Group ID**s are listed at the beginning of this article.

![Screenshot of the "Add Shared Private Access" pane, showing the manual experience for creating a shared private link resource. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-manual.png)

#### Option 2: Azure CLI

Alternatively, you can make the following API call with the [Azure CLI](/cli/azure/). Use the preview or generally available API version (*2020-08-01* or later) if you're using a **Group ID** that is in preview. For example, **Group ID**s *sites* and *mysqlServer* and in preview and require you to use the preview API.

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/<search service subscription ID>/resourceGroups/<search service resource group name>/providers/Microsoft.Search/searchServices/<search service name>/sharedPrivateLinkResources/<shared private endpoint name>?api-version=2020-08-01 --body @create-pe.json
```

The following is an example of the contents of the *create-pe.json* file:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

A `202 Accepted` response is returned on success. The process of creating an outbound private endpoint is a long-running (asynchronous) operation. It involves deploying the following resources:

+ A private endpoint, allocated with a private IP address in a `"Pending"` state. The private IP address is obtained from the address space that's allocated to the virtual network of the execution environment for the search service-specific private indexer. Upon approval of the private endpoint, any communication from Azure Cognitive Search to the Azure resource originates from the private IP address and a secure private link channel.

+ A private DNS zone for the type of resource, based on the **Group ID**. By deploying this resource, you ensure that any DNS lookup to the private resource utilizes the IP address that's associated with the private endpoint.

Be sure to specify the correct **Group ID** for the type of resource for which you're creating the private endpoint. Any mismatch will result in a non-successful response message.

### Step 2: Check the status of the private endpoint creation

In this step you'll confirm that the provisioning state of the resource changes to "Succeeded".

#### Option 1: Portal

> [!NOTE]
> The "Provisioning State" will be visible in the Azure portal for **Group ID** that are both generally available and Preview.

The portal will show you the state of the shared private endpoint. In the following example the status is "Updating".

![Screenshot of the "Add Shared Private Access" pane, showing the resource creation in progress. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-progress.png)

Once the resource is successfully created, you will receive a portal notification and the provisioning state of the resource will change to "Succeeded".

![Screenshot of the "Add Shared Private Access" pane, showing the resource creation completed. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-success.png)

#### Option 2: Azure CLI

The `PUT` call to create the shared private endpoint returns an `Azure-AsyncOperation` header value that looks like the following:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

You can poll for the status by manually querying the `Azure-AsyncOperationHeader` value.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01
```

### Step 3: Approve the private endpoint connection

> [!NOTE]
> In this section, you use the Azure portal for the approval flow of a private endpoint to the Azure resource you're connecting to. Alternatively, you could use the **[REST API](/rest/api/storagerp/privateendpointconnections)** that's available via the Storage resource provider.
>
> Other providers, such as Azure Cosmos DB or Azure SQL Server, offer similar resource provider REST APIs for managing private endpoint connections.

1. In the Azure portal, navigate to the Azure resource that you're connecting to and select the **Networking** tab. Then navigate to the section that lists the private endpoint connections. Following is an example for a storage account. After the asynchronous operation has succeeded, there should be a request for a private endpoint connection with the request message from the previous API call.

   ![Screenshot of the Azure portal, showing the "Private endpoint connections" pane.](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Select the private endpoint that Azure Cognitive Search created. In the **Private endpoint** column, identify the private endpoint connection by the name that's specified in the previous API, select **Approve**, and then enter an appropriate message. The message content isn't significant.

   Make sure that the private endpoint connection appears as shown in the following screenshot. It could take one to two minutes for the status to be updated in the portal.

   ![Screenshot of the Azure portal, showing an "Approved" status on the "Private endpoint connections" pane.](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

After the private endpoint connection request is approved, traffic is *capable* of flowing through the private endpoint. After the private endpoint is approved, Azure Cognitive Search creates the necessary DNS zone mappings in the DNS zone that's created for it.

### Step 4: Query the status of the shared private link resource

To confirm that the shared private link resource has been updated after approval, revisit the "Shared Private Access" blade of the search service on the Azure portal and check the "Connection State".

   ![Screenshot of the Azure portal, showing an "Approved" shared private link resource.](media\search-indexer-howto-secure-access\new-shared-private-link-resource-approved.png)

Alternatively, you can also obtain the "Connection state" by using the [GET API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get).

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01
```

This would return a JSON, where the connection state would show up as "status" under the "properties" section. Following is an example for a storage account.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

If the "Provisioning State" (`properties.provisioningState`) of the resource is `Succeeded` and "Connection State" (`properties.status`) is `Approved`, it means that the shared private link resource is functional and the indexer can be configured to communicate over the private endpoint.

### Step 5: Secure your Azure resource

The steps for restricting access varies by resource. The following scenarios show three of the more common types of resources.

- Scenario 1: Azure Storage

    The following is an example of how to configure an Azure storage account firewall. If you select this option and leave the page empty, it means that no traffic from virtual networks is allowed.

    ![Screenshot of the "Firewalls and virtual networks" pane for Azure storage, showing the option to allow access to selected networks.](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

- Scenario 2: Azure Key Vault

    The following is an example of how to configure Azure Key Vault firewall.
 
    ![Screenshot of the "Firewalls and virtual networks" pane for Azure Key Vault, showing the option to allow access to selected networks.](media\search-indexer-howto-secure-access\key-vault-firewall-noaccess.png)
    
- Scenario 3: Azure Functions

    No network setting changes are needed for Azure Functions firewalls. Later in the following steps, when you create the shared private endpoint, the Function will automatically only allow access through private link after the creation of a shared private endpoint to the Function.


### Step 6: Configure the indexer to run in the private environment

> [!NOTE]
> You can perform this step before the private endpoint connection is approved. However, until the private endpoint connection shows as approved, any indexer that tries to communicate with a secure resource (such as the storage account) will end up in a transient failure state and new indexers will fail to be created. 

The following steps show how to configure the indexer to run in the private environment using the REST API. You can also set the execution environment using the JSON editor in the portal.

1. Create the data source definition, index, and skillset (if you're using one) as you would normally. There are no properties in any of these definitions that vary when using a shared private endpoint.

1. [Create an indexer](/rest/api/searchservice/create-indexer) that points to the data source, index, and skillset that you created in the preceding step. In addition, force the indexer to run in the private execution environment by setting the indexer `executionEnvironment` configuration property to `private`.

    ```json
    {
        "name": "indexer",
        "dataSourceName": "blob-datasource",
        "targetIndexName": "index",
        "parameters": {
            "configuration": {
                "executionEnvironment": "private"
            }
        },
        "fieldMappings": []
    }
    ```

    Following is an example of the request in Postman.
    
    ![Screenshot showing the creation of an indexer on the Postman user interface.](media\search-indexer-howto-secure-access\create-indexer.png)    

After the indexer is created successfully, it should connect to the Azure resource over the private endpoint connection. You can monitor the status of the indexer by using the [Indexer Status API](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> If you already have existing indexers, you can update them via the [PUT API](/rest/api/searchservice/create-indexer) by setting the `executionEnvironment` to `private` or using the JSON editor in the portal.

## Additional configuration requirements

Here is a list of the data sources and configurations that have special conditions for shared private link resources and which **Group ID**s are necessary for each to work appropriately:

+ **[Azure Data Lake Storage Gen2 data source](search-howto-index-azure-data-lake-storage.md)** - Create two shared private links: One shared private link with the **Group ID** *'dfs'* and another shared private link with the **Group ID** *'blob'*.
+ **[Skillset with knowledge store configured](knowledge-store-concept-intro.md)** - One or two shared private links are necessary, depending on the projections set for knowledge store:
    + If using object or file projections, create one shared private link with the **Group ID** *'blob'*. 
    + If using table projections, create one shared private link with the **Group ID** *'table'*. 
	 + If using all of the projections (object, table, and file), create two shared private links: one with **Group ID** *'blob'* and one with **Group ID** *'table'*. 
+ **[Indexer with incremental enrichment (Cache enabled)](cognitive-search-incremental-indexing-conceptual.md)** - Create two shared private links: one shared private link with the **Group ID** 'table' and another shared private link with the **Group ID** 'blob'.

## Troubleshooting

+ If your indexer creation fails with an error message such as "Data source credentials are invalid," it means that either the status of the private endpoint connection is not yet *Approved* or the connection is not functional. To remedy the issue: 
  + Obtain the status of the shared private link resource by using the [GET API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get). If the status is *Approved*, check the `properties.provisioningState` of the resource. If the status here is `Incomplete`, this means that some of the underlying dependencies for the resource failed to be set up. Reissuing the `PUT` request to re-create the shared private link resource should fix the issue. A reapproval might be necessary. Re-check the status of the resource to verify that the issue is fixed.

+ If you create the indexer without setting its `executionEnvironment` property, the creation might succeed, but its execution history will show that the indexer runs are unsuccessful. To remedy the issue:
  + [Update the indexer](/rest/api/searchservice/update-indexer) to specify the execution environment.

+ If you've created the indexer without setting the `executionEnvironment` property and it runs successfully, it means that Azure Cognitive Search has decided that its execution environment is the search service-specific *private* environment. This can change, depending on resources consumed by the indexer, the load on the search service, and other factors, and it can fail later. To remedy the issue:
  + We highly recommend that you set the `executionEnvironment` property to `private` to ensure that it won't fail in the future.

+ If you're viewing your data source's networking page in the Azure portal and you select a private endpoint that you created for your Azure Cognitive Search service to access this data source, you may receive a *No Access* error. This is expected. You can change the status of the connection request via the target service's portal page but to further manage the shared private link resource you need to view the shared private link resource in your search service's network page in the Azure portal.

  [Quotas and limits](search-limits-quotas-capacity.md) determine how many shared private link resources can be created and depend on the SKU of the search service.

+ If you are experiencing errors after you have followed all the steps listed in [Set up indexer connection through private endpoint](#set-up-indexer-connection-through-private-endpoint), check [Additional configuration requirements](#additional-configuration-requirements) in case you are missing a necessary managed outbound private endpoints for your setup.

## Next steps

Learn more about private endpoints and other secure connection methods:

+ [Troubleshoot issues with shared private link resources](troubleshoot-shared-private-link-resources.md)
+ [What are private endpoints?](../private-link/private-endpoint-overview.md)
+ [DNS configurations needed for private endpoints](../private-link/private-endpoint-dns.md)
+ [Indexer access to content protected by Azure network security features](search-indexer-securing-resources.md)