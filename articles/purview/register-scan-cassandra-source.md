---
title: Connect to and manage Cassandra
description: This guide describes how to connect to Cassandra in Azure Purview, and use Azure Purview's features to scan and manage your Cassandra source.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 01/20/2022
ms.custom: template-how-to, ignite-fall-2021
---

# Connect to and manage Cassandra in Azure Purview (Preview)

This article outlines how to register Cassandra, and how to authenticate and interact with Cassandra in Azure Purview. For more information about Azure Purview, read the [introductory article](overview.md).

## Supported capabilities

|**Metadata Extraction**|  **Full Scan**  |**Incremental Scan**|**Scoped Scan**|**Classification**|**Access Policy**|**Lineage**|
|---|---|---|---|---|---|---|
| [Yes](#register) | [Yes](#scan)| No | [Yes](#scan) | No | No| [Yes](#lineage)|

The supported Cassandra server versions are 3.*x* or 4.*x*.

When scanning Cassandra source, Azure Purview supports:

- Extracting technical metadata including:

    - Cluster
    - Keyspaces
    - Tables including the columns and indexes
    - Materialized views including the columns

- Fetching static lineage on assets relationships among tables and materialized views.

When setting up scan, you can choose to scan an entire Cassandra instance, or scope the scan to a subset of keyspaces matching the given name(s) or name pattern(s).

## Prerequisites

* An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* An active [Azure Purview resource](create-catalog-portal.md).

* You will need to be a Data Source Administrator and Data Reader to register a source and manage it in the Azure Purview Studio. See our [Azure Purview Permissions page](catalog-permissions.md) for details.

* Set up the latest [self-hosted integration runtime](https://www.microsoft.com/download/details.aspx?id=39717).
  For more information, see [the create and configure a self-hosted integration runtime guide](manage-integration-runtimes.md).

* Ensure [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) is installed on the virtual machine where the self-hosted integration runtime is installed.

* Ensure Visual C++ Redistributable for Visual Studio 2012 Update 4 is installed on the self-hosted integration runtime machine. If you don't have this update installed, [you can download it here](https://www.microsoft.com/download/details.aspx?id=30679).

## Register

This section describes how to register Cassandra in Azure Purview using the [Azure Purview Studio](https://web.purview.azure.com/).

### Steps to register

To register a new Cassandra server in your data catalog:

1. Go to your Azure Purview account.
1. Select **Data Map** on the left pane.
1. Select **Register**.
1. On the **Register sources** screen, select **Cassandra**, and then select **Continue**:

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Screenshot that shows the Register sources screen." border="true":::

1. On the **Register sources (Cassandra)** screen:

   1. Enter a **Name**. The data source will use this name in the
    catalog.
   1. In the **Host** box, enter the server address where the Cassandra server is running. For example, 20.190.193.10.
   1. In the **Port** box, enter the port used by the Cassandra server.
   1. Select a collection or create a new one (optional).
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Screenshot that shows the Register sources (Cassandra) screen." border="true":::
   1. Select **Register**.

## Scan

Follow the steps below to scan Cassandra to automatically identify assets and classify your data. For more information about scanning in general, see our [introduction to scans and ingestion](concept-scans-and-ingestion.md)

### Create and run scan

To create and run a new scan:

1. In the Management Center, select **Integration runtimes**. Make sure a
    self-hosted integration runtime is set up. If you don't have one set up, complete
    [these steps to set up a self-hosted integration runtime](./manage-integration-runtimes.md).

1. Go to **Sources**.

1. Select the registered Cassandra server.

1. Select **New scan**.

1. Provide the following details.

    1. **Name**: Specify a name for the scan.

    1. **Connect via integration runtime**: Select the configured
        self-hosted integration runtime.

    1. **Credential**: When you configure the Cassandra credentials, be sure
        to:

        * Select **Basic Authentication** as the authentication method.
        * In the **User name** box, provide the name of the user you're making the connection for. 
        * In the key vault's secret, save the password of the Cassandra user you're making the connection for.

        For more information, see [Credentials for source authentication in Azure Purview](manage-credentials.md).

    1. **Keyspaces**: Specify a list of Cassandra keyspaces to import. Multiple keyspaces must be separated with semicolons. For example, keyspace1; keyspace2. When the list is empty, all available keyspaces are imported.

        You can use keyspace name patterns that use SQL LIKE expression syntax, including %.

        For example: A%; %B; %C%; D

        This expression means:
        * Starts with A or
        * Ends with B or
        * Contains C or
        * Equals D

        You can't use NOT or special characters.

    1. **Use Secure Sockets Layer(SSL)**: Select **True** or **False** to specify whether
    to use Secure Sockets Layer (SSL) when connecting to the
    Cassandra server. By default, this option is set to **False**.

    1. **Maximum memory available**: Specify the maximum memory (in GB) available on your VM to be used for scanning processes. This value depends on the size of Cassandra server to be scanned.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="scan Cassandra source" border="true":::

1. Select **Test connection.**

1. Select **Continue**.

1. Select a **scan trigger**. You can set up a schedule or run the
    scan once.

1. Review your scan, and then select **Save and Run**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## Lineage

After scanning your Cassandra source, you can [browse data catalog](how-to-browse-catalog.md) or [search data catalog](how-to-search-catalog.md) to view the asset details. 

Go to the asset -> lineage tab, you can see the asset relationship when applicable. Refer to the [supported capabilities](#supported-capabilities) section on the supported Cassandra lineage scenarios. For more information about lineage in general, see [data lineage](concept-data-lineage.md) and [lineage user guide](catalog-lineage-user-guide.md).

:::image type="content" source="media/register-scan-cassandra-source/lineage.png" alt-text="Cassandra lineage view" border="true":::

## Next steps

Now that you have registered your source, follow the below guides to learn more about Azure Purview and your data.

- [Data insights in Azure Purview](concept-insights.md)
- [Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Search Data Catalog](how-to-search-catalog.md)
