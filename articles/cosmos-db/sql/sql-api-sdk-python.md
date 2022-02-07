---
title: Azure Cosmos DB SQL Python API, SDK & resources
description: Learn all about the SQL Python API and SDK including release dates, retirement dates, and changes made between each version of the Azure Cosmos DB Python SDK.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 01/25/2022
ms.author: rosouz
ms.custom: devx-track-python
---
# Azure Cosmos DB Python SDK for SQL API: Release notes and resources
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP Connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST Resource Provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [Bulk executor - .NET  v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

| Page| Link |
|---|---|
|**Download SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API documentation**|[Python API reference documentation](/python/api/azure-cosmos/azure.cosmos?preserve-view=true&view=azure-python)|
|**SDK installation instructions**|[Python SDK installation instructions](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Get started**|[Get started with the Python SDK](create-sql-api-python.md)|
|**Current supported platform**|[Python 3.6+](https://www.python.org/downloads/)|

> [!IMPORTANT]
> * Versions 4.3.0b2 and higher only support Python 3.6+. Python 2 is not supported.

## Release history
Release history is maintained in the azure-sdk-for-python repo, for detailed list of releases, see the [changelog file](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/cosmos/azure-cosmos/CHANGELOG.md).

## Release & retirement dates

Microsoft provides notification at least **12 months** in advance of retiring an SDK in order to smooth the transition to a newer/supported version. New features and functionality and optimizations are only added to the current SDK, as such it is recommended that you always upgrade to the latest SDK version as early as possible.

> [!WARNING]
> After 31 August 2022, Azure Cosmos DB will no longer make bug fixes or provide support to versions 1.x and 2.x of the Azure Cosmos DB Python SDK for SQL API. If you prefer not to upgrade, requests sent from version 1.x and 2.x of the SDK will continue to be served by the Azure Cosmos DB service.

| Version | Release Date | Retirement Date |
| --- | --- | --- |
| 4.2.0 |Oct 09, 2020 |--- |
| 4.1.0 |Aug 10, 2020 |--- |
| 4.0.0 |May 20, 2020 |--- |
| 3.0.2 |Nov 15, 2018 |--- |
| 3.0.1 |Oct 04, 2018 |--- |
| 2.3.3 |Sept 08, 2018 |August 31, 2022 |
| 2.3.2 |May 08, 2018 |August 31, 2022 |
| 2.3.1 |December 21, 2017 |August 31, 2022 |
| 2.3.0 |November 10, 2017 |August 31, 2022 |
| 2.2.1 |Sep 29, 2017 |August 31, 2022 |
| 2.2.0 |May 10, 2017 |August 31, 2022 |
| 2.1.0 |May 01, 2017 |August 31, 2022 |
| 2.0.1 |October 30, 2016 |August 31, 2022 |
| 2.0.0 |September 29, 2016 |August 31, 2022 |
| 1.9.0 |July 07, 2016 |August 31, 2022 |
| 1.8.0 |June 14, 2016 |August 31, 2022 |
| 1.7.0 |April 26, 2016 |August 31, 2022 |
| 1.6.1 |April 08, 2016 |August 31, 2022 |
| 1.6.0 |March 29, 2016 |August 31, 2022 |
| 1.5.0 |January 03, 2016 |August 31, 2022 |
| 1.4.2 |October 06, 2015 |August 31, 2022 |
| 1.4.1 |October 06, 2015 |August 31, 2022 |
| 1.2.0 |August 06, 2015 |August 31, 2022 |
| 1.1.0 |July 09, 2015 |August 31, 2022 |
| 1.0.1 |May 25, 2015 |August 31, 2022 |
| 1.0.0 |April 07, 2015 |August 31, 2022 |
| 0.9.4-prelease |January 14, 2015 |February 29, 2016 |
| 0.9.3-prelease |December 09, 2014 |February 29, 2016 |
| 0.9.2-prelease |November 25, 2014 |February 29, 2016 |
| 0.9.1-prelease |September 23, 2014 |February 29, 2016 |
| 0.9.0-prelease |August 21, 2014 |February 29, 2016 |

## FAQ

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## Next steps

To learn more about Cosmos DB, see [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service page.
