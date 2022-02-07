---
title: 'How to view, edit, and delete assets'
description: This how to guide describes how you can view and edit asset details. 
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
---
# View, edit and delete assets in Azure Purview catalog

This article discusses how to you can view your assets and their relevant details. It also describes how you can edit and delete assets from your catalog.

## Prerequisites

- Set up your data sources and scan the assets into your catalog.
- *Or* Use the Azure Purview Atlas APIs to ingest assets into the catalog. 

## Viewing asset details

You can discover your assets in Azure Purview by either:
- [Browsing the Azure Purview Data catalog](how-to-browse-catalog.md)
- [Searching the Azure Purview Data Catalog](how-to-search-catalog.md)

Once you find the asset you are looking for, you can view all of its details, edit, or delete them as described in following sections.

## Asset details tabs explained

:::image type="content" source="media/catalog-asset-details/asset-tabs.png" alt-text="Asset details tabs":::

- **Overview** - The overview tab covers an asset's basic details like description, classification, hierarchy, and glossary terms.
- **Properties** - The properties tab covers both basic and advanced properties regarding an asset.
- **Schema** - The schema of the asset including column names, data types, column level classifications, terms, and descriptions are represented in the schema tab.
- **Lineage** - This tab contains lineage graph details for assets where it is available.
- **Contacts** - Every asset can have an assigned owner and expert that can be viewed and managed from the contacts tab.
- **Related** - This tab lets you navigate to assets that are related to the current asset you are viewing. 

## Asset overview
The overview section of the asset details gives you a summarized view of an asset. The sections that follow explains the different parts of the overview page.

### Asset hierarchy

You can view the full asset hierarchy within the overview tab. As an example: if you navigate to a SQL table, then you can see the schema, database, and the server the table belongs to.

:::image type="content" source="media/catalog-asset-details/asset-hierarchy.png" alt-text="Asset hierarchy":::

### Asset classifications

Asset classifications identify the kind of data being represented, and are applied manually or during a scan. For example: a National ID or passport number are supported classifications. (For a full list of classifications, see the [supported classifications page](supported-classifications.md).) The overview tab reflects both asset level classifications and column level classifications that have been applied, which you can also view as part of the schema.

:::image type="content" source="media/catalog-asset-details/asset-classifications.png" alt-text="Asset classifications":::

### Asset description

You can view the description on an asset in the overview section. You can add an asset description by [editing the asset](#editing-assets)

:::image type="content" source="media/catalog-asset-details/asset-description.png" alt-text="Asset description":::

### Asset glossary terms

Asset glossary terms are a managed vocabulary for business terms that can be used to categorize and relate assets across your environment. For example, terms like 'customer', 'buyer', 'cost center', or any terms that give your data context for your users. For more information, see the [business glossary page](concept-business-glossary.md). You can view the glossary terms for an asset in the overview section, and you can add a glossary term on an asset by [editing the asset](#editing-assets).

:::image type="content" source="media/catalog-asset-details/asset-glossary.png" alt-text="Asset glossary terms":::

## Editing assets

You can edit an asset by selecting the edit icon on the top-left corner of the asset.

:::image type="content" source="media/catalog-asset-details/asset-edit-delete.png" alt-text="Asset edit and delete buttons":::

At the asset level you can edit or add a description, classification, or glossary term by staying on the overview tab of the edit screen.

You can navigate to the schema tab on the edit screen to update column name, data type, column level classification, terms, or asset description.

You can navigate to the contact tab of the edit screen to update owners and experts on the asset. You can search by full name, email or alias of the person within your Azure active directory.

### Scans on edited assets

If you edit an asset by adding a description, asset level classification, glossary term, or a contact, later scans will still update the asset schema (new columns and classifications detected by the scanner in subsequent scan runs).

If you make some column level updates, like adding a description, column level classification, or glossary term, then subsequent scans will also update the asset schema (new columns and classifications will be detected by the scanner in subsequent scan runs). 

Even on edited assets, after a scan Azure Purview will reflect the truth of the source system. For example: if you edit a column and it's deleted from the source, it will be deleted from your asset in Azure Purview. 

>[!NOTE]
> If you update the **name or data type of a column** in an Azure Purview asset, later scans **will not** update the asset schema. New columns and classifications **will not** be detected.

## Deleting assets

You can delete an asset by selecting the delete icon under the name of the asset.

### Delete behavior explained

Any asset you delete using the delete button is permanently deleted in Azure Purview. However, if you run a **full scan** on the source from which the asset was ingested into the catalog, then the asset is reingested and you can discover it using the Azure Purview catalog.

If you have a scheduled scan (weekly or monthly) on the source, the **deleted asset will not get re-ingested** into the catalog unless the asset is modified by an end user since the previous run of the scan.   For example, if a SQL table was deleted from Azure Purview, but after the table was deleted a user added a new column to the table in SQL, at the next scan the asset will be rescanned and ingested into the catalog.

If you delete an asset, only that asset is deleted. Azure Purview does not currently support cascaded deletes. For example, if you delete a storage account asset in your catalog - the containers, folders and files within them are not deleted. 


## Next steps

- [Browse the Azure Purview Data catalog](how-to-browse-catalog.md)
- [Search the Azure Purview Data Catalog](how-to-search-catalog.md)
