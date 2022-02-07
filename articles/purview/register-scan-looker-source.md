---
title: Connect to and manage Looker
description: This guide describes how to connect to  Looker  in Azure Purview, and use Azure Purview's features to scan and manage your Looker source.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 01/20/2022
ms.custom: template-how-to, ignite-fall-2021
---

# Connect to and manage Looker in Azure Purview (Preview)

This article outlines how to register Looker, and how to authenticate and interact with Looker in Azure Purview. For more information about Azure Purview, read the [introductory article](overview.md).

[!INCLUDE [feature-in-preview](includes/feature-in-preview.md)]

## Supported capabilities

|**Metadata Extraction**|  **Full Scan**  |**Incremental Scan**|**Scoped Scan**|**Classification**|**Access Policy**|**Lineage**|
|---|---|---|---|---|---|---|
| [Yes](#register)| [Yes](#scan)| No | [Yes](#scan) | No | No| [Yes](#lineage)|

The supported Looker server version is 7.2.

When scanning Looker source, Azure Purview supports:

- Extracting technical metadata including:

    - Server
    - Folders
    - Projects
    - Models
    - Dashboards
    - Looks
    - Explore diagrams including the joins
    - Views including the dimensions, measures, parameters, and filters
    - Layouts including the chart layouts, table layouts, text, and fields

- Fetching static lineage on assets relationships among views and layouts.

When setting up scan, you can choose to scan an entire Looker server, or scope the scan to a subset of Looker projects matching the given name(s).

## Prerequisites

* An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* An active [Azure Purview resource](create-catalog-portal.md).

* You will need to be a Data Source Administrator and Data Reader to register a source and manage it in the Azure Purview Studio. See our [Azure Purview Permissions page](catalog-permissions.md) for details.

* Set up the latest [self-hosted integration runtime](https://www.microsoft.com/download/details.aspx?id=39717). For more information, see [the create and configure a self-hosted integration runtime guide](manage-integration-runtimes.md).

* Ensure [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) is installed on the virtual machine where the self-hosted integration runtime is installed.

* Ensure Visual C++ Redistributable for Visual Studio 2012 Update 4 is installed on the self-hosted integration runtime machine. If you don't have this update installed, [you can download it here](https://www.microsoft.com/download/details.aspx?id=30679).

## Register

This section describes how to register Looker in Azure Purview using the [Azure Purview Studio](https://web.purview.azure.com/).

### Authentication for registration

An API3 key is required to connect to the Looker server. The API3 key consists in a public client_id and a private client_secret and follows an OAuth2 authentication pattern.

### Steps to register

To register a new Looker server in your data catalog, do the following:

1. Navigate to your Azure Purview account.
1. Select **Data Map** on the left navigation.
1. Select **Register.**
1. On Register sources, select **Looker**. Select **Continue.**

:::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="register looker source" border="true":::

On the Register sources (Looker) screen, do the following:

1. Enter a **Name** that the data source will be listed within the Catalog.

1. Enter the Looker API URL in the **Server API URL** field. The default port for API requests is port 19999. Also, all Looker API endpoints require an HTTPS connection. For example: 'https://azurepurview.cloud.looker.com'

1. Select a collection or create a new one (Optional)

1. Finish to register the data source.

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="scan looker source" border="true":::

## Scan

Follow the steps below to scan Looker to automatically identify assets and classify your data. For more information about scanning in general, see our [introduction to scans and ingestion](concept-scans-and-ingestion.md)

### Create and run scan

To create and run a new scan, do the following:

1. In the Management Center, select Integration runtimes. Make sure a self-hosted integration runtime is set up on the VM where erwin Mart instance is running. If it is not set up, use the steps mentioned [here](./manage-integration-runtimes.md) to set up a self-hosted integration runtime.

1. Navigate to **Sources**.

1. Select the registered **Looker** server.

1. Select **+ New scan**.

1. Provide the below details:

    1. **Name**: The name of the scan

    1. **Connect via integration runtime**: Select the configured self-hosted integration runtime.

    1. **Server API URL** is auto populated based on the value entered during registration.

    1. **Credential:** While configuring Looker credential, make sure to:

        * Select **Basic Authentication** as the Authentication method
        * Provide your Looker API3 key's client ID in the User name field
        * Save your Looker API3 key's client secret in the key vault's secret.

        To access client ID and client secret, navigate to Looker -\>Admin -\> Users -\> Select **Edit** on a user -\> Select **EditKeys** -\> Use the Client ID and Client Secret or create a new one.

        :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="get looker details" border="true":::

        To understand more on credentials, refer to the link [here](manage-credentials.md)

    1. **Project filter** -- Scope your scan by providing a semicolon separated list of Looker projects. This option is used to select looks and dashboards by their parent project.

    1. **Maximum memory available**: Maximum memory (in GB) available on customer's VM to be used by scanning processes. This is dependent on the size of erwin Mart to be scanned.

        :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="trigger scan" border="true":::

1. Select **Test connection.**

1. Select **Continue**.

1. Choose your **scan trigger**. You can set up a schedule or ran the scan once.

1. Review your scan and select on **Save and Run**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## Lineage

After scanning your Looker source, you can [browse data catalog](how-to-browse-catalog.md) or [search data catalog](how-to-search-catalog.md) to view the asset details. 

Go to the asset -> lineage tab, you can see the asset relationship when applicable. Refer to the [supported capabilities](#supported-capabilities) section on the supported Looker lineage scenarios. For more information about lineage in general, see [data lineage](concept-data-lineage.md) and [lineage user guide](catalog-lineage-user-guide.md).

:::image type="content" source="media/register-scan-looker-source/lineage.png" alt-text="Looker lineage view" border="true":::

## Next steps

Now that you have registered your source, follow the below guides to learn more about Azure Purview and your data.

- [Data insights in Azure Purview](concept-insights.md)
- [Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Search Data Catalog](how-to-search-catalog.md)
