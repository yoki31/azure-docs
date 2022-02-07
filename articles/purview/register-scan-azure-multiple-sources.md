---
title: Connect to and manage multiple Azure sources
description: This guide describes how to connect to multiple Azure sources in Azure Purview at once, and use Azure Purview's features to scan and manage your sources.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
---

# Connect to and manage multiple Azure sources in Azure Purview

This article outlines how to register multiple Azure sources and how to authenticate and interact with them in Azure Purview. For more information about Azure Purview, read the [introductory article](overview.md).

## Supported capabilities

|**Metadata Extraction**|  **Full Scan**  |**Incremental Scan**|**Scoped Scan**|**Classification**|**Access Policy**|**Lineage**|
|---|---|---|---|---|---|---|
| [Yes](#register) | [Yes](#scan) | [Yes](#scan) | [Yes](#scan)| [Yes](#scan)| No| [Source Dependant](catalog-lineage-user-guide.md)|

## Prerequisites

* An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* An active [Azure Purview resource](create-catalog-portal.md).

* You will need to be a Data Source Administrator and Data Reader to register a source and manage it in the Azure Purview Studio. See our [Azure Purview Permissions page](catalog-permissions.md) for details.

## Register

This section describes how to register multiple Azure sources in Azure Purview using the [Azure Purview Studio](https://web.purview.azure.com/).

### Prerequisites for registration

You need to set up some authentication to be able to enumerate resources under a subscription or resource group.

1. Go to the subscription or the resource group in the Azure portal.  
1. Select **Access Control (IAM)** from the left menu.
1. Select **+Add**.
1. In the **Select input** box, select the **Reader** role and enter your Azure Purview account name (which represents its MSI file name). 
1. Select **Save** to finish the role assignment.

### Authentication for registration

There are two ways to set up authentication for multiple sources in Azure:

* Managed identity
* Service principal

You must set up authentication on each resource within your subscription or resource group that you want to register and scan. Azure Storage resource types (Azure Blob Storage and Azure Data Lake Storage Gen2) make it easy by allowing you to add the MSI file or service principal at the subscription or resource group level as a storage blob data reader. The permissions then trickle down to each storage account within that subscription or resource group. For all other resource types, you must apply the MSI file or service principal on each resource, or create a script to do so.

To learn how to add permissions on each resource type within a subscription or resource group, see the following resources:
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md#authentication-for-a-scan)
- [Azure SQL Managed Instance](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)

### Steps to register

1. Go to your Azure Purview account.
1. Select **Data Map** on the left menu.
1. Select **Register**.
1. On **Register sources**, select **Azure (multiple)**.

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Screenshot that shows the tile for Azure Multiple on the screen for registering multiple sources.":::

1. Select **Continue**.
1. On the **Register sources (Azure)** screen, do the following:

   1. In the **Name** box, enter a name that the data source will be listed with in the catalog. 
   1. In the **Management group** box, optionally choose a management group to filter down to.
   1. In the **Subscription** and **Resource group** dropdown list boxes, select a subscription or a specific resource group, respectively. The registration scope will be set to the selected subscription or resource group.  

      :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Screenshot that shows the boxes for selecting a subscription and resource group.":::

   1. In the **Select a collection** box, select a collection or create a new one (optional).
   1. Select **Register** to register the data sources.

## Scan

Follow the steps below to scan multiple Azure sources to automatically identify assets and classify your data. For more information about scanning in general, see our [introduction to scans and ingestion](concept-scans-and-ingestion.md).

### Create and run scan

To create and run a new scan, do the following:

1. Select the **Data Map** tab on the left pane in the Azure Purview Studio.
1. Select the data source that you registered.
1. Select **View details** > **+ New scan**, or use the **Scan** quick-action icon on the source tile.
1. For **Name**, fill in the name.
1. For **Type**, select the types of resources that you want to scan within this source. Choose one of these options:

    - Leave it as **All**. This selection includes future resource types that might not currently exist within that subscription or resource group.
    - Use the boxes to specifically select resource types that you want to scan. If you choose this option, future resource types that might be created within this subscription or resource group won't be included for scans, unless the scan is explicitly edited in the future.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Screenshot that shows options for scanning multiple sources.":::

1. Select the credential to connect to the resources within your data source:
    - You can select a credential at the parent level as an MSI file, or you can select a credential for a particular service principal type. You can then use that credential for all the resource types under the subscription or resource group.
    - You can specifically select the resource type and apply a different credential for that resource type.

    Each credential will be considered as the method of authentication for all the resources under a particular type. You must set the chosen credential on the resources in order to successfully scan them, as described [earlier in this article](#authentication-for-registration).
1. Within each type, you can select to either scan all the resources or scan a subset of them by name:
    - If you leave the option as **All**, then future resources of that type will also be scanned in future scan runs.
    - If you select specific storage accounts or SQL databases, then future resources of that type created within this subscription or resource group will not be included for scans, unless the scan is explicitly edited in the future.

1. Select **Test connection**. This will first test access to check if you've applied the Azure Purview MSI file as a reader on the subscription or resource group. If you get an error message, follow [these instructions](#prerequisites-for-registration) to resolve it. Then it will test your authentication and connection to each of your selected sources and generate a report. The number of sources selected will impact the time it takes to generate this report. If failed on some resources, hovering over the **X** icon will display the detailed error message.

    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection.png" alt-text="Screenshot showing the scan set up slider, with the Test Connection button highlighted.":::
    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection-report.png" alt-text="Screenshot showing an example test connection report, with some connections passing and some failing. Hovering over one of the failed connections shows a detailed error report.":::

1. After you test connection has passed, select **Continue** to proceed.

1. Select scan rule sets for each resource type that you chose in the previous step. You can also create scan rule sets inline.
  
   :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Screenshot that shows scan rules for each resource type.":::

1. Choose your scan trigger. You can schedule it to run weekly, monthly, or once.

1. Review your scan and select **Save** to complete setup.

## View your scans and scan runs

1. View source details by selecting **View details** on the tile under the **Data Map** section.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Screenshot that shows source details."::: 

1. View scan run details by going to the **Scan details** page.
   
    The *status bar* is a brief summary of the running status of the child resources. It's displayed on the subscription level or resource group level. The colors have the following meanings:
    
    - Green: The scan was successful.
    - Red: The scan failed. 
    - Gray: The scan is still in progress.
   
    You can select each scan to view finer details.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Screenshot that shows scan details.":::

1. View a summary of recent failed scan runs at the bottom of the source details. You can also view more granular details about these runs.

## Manage your scans: edit, delete, or cancel

To manage a scan, do the following:

1. Go to the management center.
1. Select **Data sources** under the **Sources and scanning** section, and then select the desired data source.
1. Select the scan that you want to manage. Then: 

   - You can edit the scan by selecting **Edit**.
   - You can delete the scan by selecting **Delete**.
   - If the scan is running, you can cancel it by selecting **Cancel**.

## Next steps

Now that you have registered your source, follow the below guides to learn more about Azure Purview and your data.

- [Data insights in Azure Purview](concept-insights.md)
- [Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Search Data Catalog](how-to-search-catalog.md)
