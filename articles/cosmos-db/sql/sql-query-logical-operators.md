---
title: Logical operators in Azure Cosmos DB
description: Learn about SQL logical operators supported by Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/07/2022
ms.author: tisande

---
# Logical operators in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

This article details the logical operators supported by Azure Cosmos DB.

## Understanding logical (AND, OR and NOT) operators

Logical operators operate on Boolean values. The following tables show the logical truth tables for these operators:

**OR operator**

Returns `true` when either of the conditions is `true`.

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |Undefined |
| **Undefined** |True |Undefined |Undefined |

**AND operator**

Returns `true` when both expressions are `true`.

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |False |Undefined |
| **False** |False |False |False |
| **Undefined** |Undefined |False |Undefined |

**NOT operator**

Reverses the value of any Boolean expression.

|  | **NOT** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Undefined** |Undefined |

**Operator Precedence**

The logical operators `OR`, `AND`, and `NOT` have the precedence level shown below:

| **Operator** | **Priority** |
| --- | --- |
| **NOT** |1 |
| **AND** |2 |
| **OR** |3 |

## * operator

The special operator * projects the entire item as is. When used, it must be the only projected field. A query like `SELECT * FROM Families f` is valid, but `SELECT VALUE * FROM Families f` and  `SELECT *, f.id FROM Families f` are not valid.

## Next steps

- [Azure Cosmos DB .NET samples](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Keywords](sql-query-keywords.md)
- [SELECT clause](sql-query-select.md)
