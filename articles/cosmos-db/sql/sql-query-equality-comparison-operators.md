---
title: Equality and comparison operators in Azure Cosmos DB
description: Learn about SQL equality and comparison operators supported by Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/07/2022
ms.author: tisande

---
# Equality and comparison operators in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

This article details the equality and comparison operators supported by Azure Cosmos DB.

## Understanding equality comparisons

The following table shows the result of equality comparisons in the SQL API between any two JSON types.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

For comparison operators such as `>`, `>=`, `!=`, `<`, and `<=`, comparison across types or between two objects or arrays produces `Undefined`.  

If the result of the scalar expression is `Undefined`, the item isn't included in the result, because `Undefined` doesn't equal `true`.

For example, the following query's comparison between a number and string value produces `Undefined`. Therefore, the filter does not include any results.

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## Next steps

- [Azure Cosmos DB .NET samples](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Keywords](sql-query-keywords.md)
- [SELECT clause](sql-query-select.md)
