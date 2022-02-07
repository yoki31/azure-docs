---
title: Azure Cosmos DB dedicated gateway
description: A dedicated gateway is compute that is a front-end to your Azure Cosmos DB account. When you connect to the dedicated gateway, it routes requests and caches data.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: tisande
---

# Azure Cosmos DB dedicated gateway - Overview (Preview)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A dedicated gateway is server-side compute that is a front-end to your Azure Cosmos DB account. When you connect to the dedicated gateway, it both routes requests and caches data. Like provisioned throughput, the dedicated gateway is billed hourly.

## Overview

You can provision a dedicated gateway to improve performance at scale. The most common reason that you would want to provision a dedicated gateway would be for caching. When you provision a dedicated gateway, an [integrated cache](integrated-cache.md) is automatically configured within the dedicated gateway. Point reads and queries that hit the integrated cache do not use any of your RUs. Provisioning a dedicated gateway with an integrated cache can help read-heavy workloads lower costs on Azure Cosmos DB.

The dedicated gateway is built into Azure Cosmos DB. When you [provision a dedicated gateway](how-to-configure-integrated-cache.md), you have a fully-managed node that routes requests to backend partitions. Connecting to Azure Cosmos DB with the dedicated gateway provides lower and more predictable latency than connecting to Azure Cosmos DB with the standard gateway. Even cache misses will see latency improvements when comparing the dedicated gateway and standard gateway.

There are only minimal code changes required in order for your application to use a dedicated gateway. Both new and existing Azure Cosmos DB accounts can provision a dedicated gateway for improved read performance.

> [!NOTE]
> Do you have any feedback about the dedicated gateway? We want to hear it! Feel free to share feedback directly with the Azure Cosmos DB engineering team:
cosmoscachefeedback@microsoft.com

## Connection modes

There are three ways to connect to an Azure Cosmos DB account:

- [Direct mode](#connect-to-azure-cosmos-db-using-direct-mode)
- [Gateway mode using the standard gateway](#connect-to-azure-cosmos-db-using-gateway-mode)
- [Gateway mode using the dedicated gateway](#connect-to-azure-cosmos-db-using-the-dedicated-gateway) (only available for SQL API accounts)

### Connect to Azure Cosmos DB using direct mode

When you connect to Azure Cosmos DB using direct mode, your application connects directly to the Azure Cosmos DB backend. Even if you have many physical partitions, request routing is handled entirely client-side. Direct mode offers low latency because your application can communicate directly with the Azure Cosmos DB backend and doesn't need an intermediate network hop.

Graphical representation of direct mode connection:

:::image type="content" source="./media/dedicated-gateway/direct-mode.png" alt-text="An image that shows how Cosmos DB direct mode works" border="false":::

### Connect to Azure Cosmos DB using gateway mode

If you connect to Azure Cosmos DB using gateway mode, your application will connect to a front-end node first, which handles routing the request to the appropriate backend nodes. Because gateway mode involves an additional network hop, you may observe slightly higher latency when compared to direct mode. 

When connecting to Azure Cosmos DB with gateway mode, you can connect with either of the following options:

* **Standard gateway** -  While the backend, which includes your provisioned throughput and storage, has dedicated capacity per container, the standard gateway is shared between many Azure Cosmos accounts. It is practical for many customers to share a standard gateway since the compute resources consumed by each individual customer is small.
* **Dedicated gateway** - In this gateway, the backend and gateway both have dedicated capacity. The integrated cache requires a dedicated gateway because it requires significant CPU and memory that is specific to your Azure Cosmos account.

### Connect to Azure Cosmos DB using the dedicated gateway

You must connect to Azure Cosmos DB using the dedicated gateway in order to use the integrated cache. The dedicated gateway has a different endpoint from the standard one provided with your Azure Cosmos DB account. When you connect to your dedicated gateway endpoint, your application sends a request to the dedicated gateway, which then routes the request to different backend nodes. If possible, the integrated cache will serve the result.

Diagram of gateway mode connection with a dedicated gateway:

:::image type="content" source="./media/dedicated-gateway/dedicated-gateway-mode.png" alt-text="An image that shows how the Cosmos DB dedicated gateway works" border="false":::
 
## Provisioning the dedicated gateway

A dedicated gateway cluster can be provisioned in Core (SQL) API accounts. A dedicated gateway cluster can have up to five nodes and you can add or remove nodes at any time. All dedicated gateway nodes within your account [share the same connection string](how-to-configure-integrated-cache.md#configuring-the-integrated-cache).

Dedicated gateway nodes are independent from one another. When you provision multiple dedicated gateway nodes, any single node can route any given request. In addition, each node has a separate integrated cache from the others. The cached data within each node depends on the data that was recently [written or read](integrated-cache.md#item-cache) through that specific node. In other words, if an item or query is cached on one node, it isn't necessarily cached on the others.

For development, we recommend starting with one node but for production, you should provision three or more nodes for high availability. [Learn how to provision a dedicated gateway cluster with an integrated cache](how-to-configure-integrated-cache.md). Provisioning multiple dedicated gateway nodes allows the dedicated gateway cluster to continue to route requests and serve cached data, even when one of the dedicated gateway nodes is unavailable.

Because it is in public preview, the dedicated gateway does not have an availability SLA. However, you should generally expect comparable availability to the rest of your Azure Cosmos DB account.

The dedicated gateway is available in the following sizes:

| **Sku Name** | **vCPU** | **Memory**  |
| ------------ | -------- | ----------- |
| **D4s**      | **4**    | **16 GB** |
| **D8s**      | **8**    | **32 GB** |
| **D16s**     | **16**   | **64 GB** |

> [!NOTE]
> Once created, you can't modify the size of the dedicated gateway nodes. However, you can add or remove nodes.

There are many different ways to provision a dedicated gateway:

- [Provision a dedicated gateway using the Azure Portal](how-to-configure-integrated-cache.md#provision-a-dedicated-gateway-cluster)
- [Use Azure Cosmos DB's REAT API](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/service/create)
- [Azure CLI](/cli/azure/cosmosdb/service?view=azure-cli-latest#az_cosmosdb_service_create)
- [ARM template](/azure/templates/microsoft.documentdb/databaseaccounts/services?tabs=bicep)
    - Note: You cannot deprovision a dedicated gateway using ARM templates

## Dedicated gateway in multi-region accounts

When you provision a dedicated gateway cluster in multi-region accounts, identical dedicated gateway clusters are provisioned in each region. For example, consider an Azure Cosmos DB account in East US and North Europe. If you provision a dedicated gateway cluster with two D8 nodes in this account, you'd have four D8 nodes in total - two in East US and two in North Europe. You don't need to explicitly configure dedicated gateways in each region and your connection string remains the same. There are also no changes to best practices for performing failovers.

> [!NOTE]
> You cannot provision a dedicated gateway cluster in accounts with availability zones enabled

Like nodes within a cluster, dedicated gateway nodes across regions are independent. It's possible that the cached data in each region will be different, depending on the recent reads or writes to that region.

## Limitations

The dedicated gateway has the following limitations during the public preview:

- Dedicated gateways are only supported on SQL API accounts.
- You can't provision a dedicated gateway in Azure Cosmos DB accounts with [IP firewalls](how-to-configure-firewall.md) or [Private Link](how-to-configure-private-endpoints.md) configured.
- You can't provision a dedicated gateway in an Azure Cosmos DB account in a [Virtual Network (Vnet)](how-to-configure-vnet-service-endpoint.md)
- You can't provision a dedicated gateway in Azure Cosmos DB accounts with [availability zones](../availability-zones/az-region.md).
- You can't use [role-based access control (RBAC)](how-to-setup-rbac.md) to authenticate data plane requests routed through the dedicated gateway

The dedicated gateway blade is hidden on Azure Cosmos DB accounts with IP firewalls, Vnet, Private Link, or availability zones.

## Supported regions

The dedicated gateway is in public preview and isn't supported in every Azure region yet. Throughout the public preview, we'll be adding new capacity. We won't have region restrictions when the dedicated gateway is generally available.

Current list of supported Azure regions:

| **Americas** | **Europe and Africa**  | **Asia Pacific**  |
| ------------ | -------- | ----------- | 
| Brazil South      | France Central    | Australia Central |
| Canada Central  | France South    | Australia Central 2 |
| Canada East     | Germany North   | Australia Southeast |
| Central US     | Germany West Central   | Central India |
| East US     | North Europe   | East Asia |
| East US 2     | Switzerland North   | Japan West |
| North Central US     | UK South   | Korea Central |
| South Central US     | UK West   | Korea South |
| West Central US     | West Europe   | Southeast Asia |
| West US     |   | UAE Central |
| West US 2     |    | West India |


## Next steps

Read more about dedicated gateway usage in the following articles:

- [Integrated cache](integrated-cache.md)
- [Configure the integrated cache](how-to-configure-integrated-cache.md)
- [Integrated cache FAQ](integrated-cache-faq.md)
- Trying to do capacity planning for a migration to Azure Cosmos DB? You can use information about your existing database cluster for capacity planning.
    - If all you know is the number of vcores and servers in your existing database cluster, read about [estimating request units using vCores or vCPUs](convert-vcore-to-request-unit.md) 
    - If you know typical request rates for your current database workload, read about [estimating request units using Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md)