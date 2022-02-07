---
title: How to create and manage collections
description: This article explains how to create and manage collections within Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 01/24/2022
ms.custom: template-how-to
---

# Create and manage collections in Azure Purview

Collections in Azure Purview can be used to organize assets and sources by your business's flow. They are also the tool used to manage access across Azure Purview. This guide will take you through the creation and management of these collections, as well as cover steps about how to register sources and add assets into your collections.

## Prerequisites

* An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Your own [Azure Active Directory tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* An active [Azure Purview resource](create-catalog-portal.md).

### Check permissions

In order to create and manage collections in Azure Purview, you will need to be a **Collection Admin** within Azure Purview. We can check these permissions in the [Azure Purview Studio](https://web.purview.azure.com/resource/). You can find Studio in the overview page of the Azure Purview resource in [Azure portal](https://portal.azure.com).

1. Select Data Map > Collections from the left pane to open collection management page.

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Screenshot of Azure Purview studio window, opened to the Data Map, with the Collections tab selected." border="true":::

1. Select your root collection. This is the top collection in your collection list and will have the same name as your Azure Purview resource. In the following example, it's called Contoso Azure Purview. Alternatively, if collections already exist you can select any collection where you want to create a subcollection.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-root-collection.png" alt-text="Screenshot of Azure Purview studio window, opened to the Data Map, with the root collection highlighted." border="true":::

1. Select **Role assignments** in the collection window.

    :::image type="content" source="./media/how-to-create-and-manage-collections/role-assignments.png" alt-text="Screenshot of Azure Purview studio window, opened to the Data Map, with the role assignments tab highlighted." border="true":::

1. To create a collection, you'll need to be in the collection admin list under role assignments. If you created the Azure Purview resource, you should be listed as a collection admin under the root collection already. If not, you'll need to contact the collection admin to grant your permission.

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-admins.png" alt-text="Screenshot of Azure Purview studio window, opened to the Data Map, with the collection admin section highlighted." border="true":::

## Collection management

### Create a collection

You'll need to be a collection admin in order to create a collection. If you aren't sure, follow the [guide above](#check-permissions) to check permissions.

1. Select Data Map > Collections from the left pane to open collection management page.

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Screenshot of Azure Purview studio window, opened to the Data Map, with the Collections tab selected and open." border="true":::

1. Select **+ Add a collection**. Again, note that only [collection admins](#check-permissions) can manage collections.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-add-a-collection.png" alt-text="Screenshot of Azure Purview studio window, showing the new collection window, with the 'Add a collection' button highlighted." border="true":::

1. In the right panel, enter the collection name and description. If needed you can also add users or groups as collection admins to the new collection.
1. Select **Create**.

    :::image type="content" source="./media/how-to-create-and-manage-collections/create-collection.png" alt-text="Screenshot of Azure Purview studio window, showing the new collection window, with a display name and collection admins selected, and the create button highlighted." border="true":::

1. The new collection's information will reflect on the page.

    :::image type="content" source="./media/how-to-create-and-manage-collections/created-collection.png" alt-text="Screenshot of Azure Purview studio window, showing the newly created collection window." border="true":::

### Edit a collection

1. Select **Edit** either from the collection detail page, or from the collection's dropdown menu.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-collection.png" alt-text="Screenshot of Azure Purview studio window, open to collection window, with the 'edit' button highlighted both in the selected collection window, and under the ellipsis button next to the name of the collection." border="true":::

1. Currently collection description and collection admins can be edited. Make any changes, then select **Save** to save your change.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-description.png" alt-text="Screenshot of Azure Purview studio window with the edit collection window open, a description added to the collection, and the save button highlighted." border="true":::

### View Collections

1. Select the triangle icon beside the collection's name to expand or collapse the collection hierarchy. Select the collection names to navigate.

    :::image type="content" source="./media/how-to-create-and-manage-collections/subcollection-menu.png" alt-text="Screenshot of Azure Purview studio collection window, with the button next to the collection name highlighted." border="true":::

1. Type in the filter box at the top of the list to filter collections.

    :::image type="content" source="./media/how-to-create-and-manage-collections/filter-collections.png" alt-text="Screenshot of Azure Purview studio collection window, with the filter above the collections highlighted." border="true":::

1. Select **Refresh** in Root collection's contextual menu to reload the collection list.

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-collections.png" alt-text="Screenshot of Azure Purview studio collection window, with the button next to the Resource name selected, and the refresh button highlighted." border="true":::

1. Select **Refresh** in collection detail page to reload the single collection.

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-single-collection.png" alt-text="Screenshot of Azure Purview studio collection window, with the refresh button under the collection window highlighted." border="true":::

### Delete a collection

You'll need to be a collection admin in order to delete a collection. If you aren't sure, follow the guide above to check permissions. Collection can be deleted only if no child collections, assets, data sources or scans are associated with it. 

1. Select **Delete** from the collection detail page.
   
   :::image type="content" source="./media/how-to-create-and-manage-collections/delete-collections.png" alt-text="Screenshot of Azure Purview studio window to delete a collection" border="true":::

2. Select **Confirm** when prompted, **Are you sure you want to delete this collection?**

   :::image type="content" source="./media/how-to-create-and-manage-collections/delete-collection-confirmation.png" alt-text="Screenshot of Azure Purview studio window showing confirmation message to delete a collection" border="true":::

3. Verify deletion of the collection from your Azure Purview Data Map.

## Add roles and restrict access through collections

Since permissions are managed through collections in Azure Purview, it is important to understand the roles and what permissions they will give your users. A user granted permissions on a collection will have access to sources and assets associated with that collection, and inherit permissions to subcollections. Inheritance [can be restricted](#restrict-inheritance), but is allowed by default.

The following guide will discuss the roles, how to manage them, and permissions inheritance.

### Roles

All assigned roles apply to sources, assets, and other objects within the collection where the role is applied.

* **Collection admins** can edit the collection, its details, and add subcollections. They can also add data curators, data readers, and other Azure Purview roles to a collection scope. Collection admins that are automatically inherited from a parent collection can't be removed.
* **Data source admins** can manage data sources and data scans. They can also enter the policy management app to view and publish policies.
* **Data curators** can perform create, read, modify, and delete actions on catalog data objects and establish relationships between objects. They can also enter the policy management app to view policies.
* **Data readers** can access but not modify catalog data objects.
* **Policy Authors** can enter the policy management app and create/edit policy statements.

### Add role assignments

1. Select the **Role assignments** tab to see all the roles in a collection. Only a collection admin can manage role assignments.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-role-assignments.png" alt-text="Screenshot of Azure Purview studio collection window, with the role assignments tab highlighted." border="true":::

1. Select **Edit role assignments** or the person icon to edit each role member.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-role-assignments.png" alt-text="Screenshot of Azure Purview studio collection window, with the edit role assignments dropdown list selected." border="true":::

1. Type in the textbox to search for users you want to add to the role member. Select **X** to remove members you don't want to add.

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-user-permissions.png" alt-text="Screenshot of Azure Purview studio collection admin window with the search bar highlighted." border="true":::

1. Select **OK** to save your changes, and you will see the new users reflected in the role assignments list.

### Remove role assignments

1. Select **X** button next to a user's name to remove a role assignment.

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-role-assignment.png" alt-text="Screenshot of Azure Purview studio collection window, with the role assignments tab selected, and the x button beside one of the names highlighted." border="true":::

1. Select **Confirm** if you're sure to remove the user.

    :::image type="content" source="./media/how-to-create-and-manage-collections/confirm-remove.png" alt-text="Screenshot of a confirmation pop-up, with the confirm button highlighted." border="true":::

### Restrict inheritance

Collection permissions are inherited automatically from the parent collection. For example, any permissions on the root collection (the collection at the top of the list that has the same name as your Azure Purview resource), will be inherited by all collections below it. You can restrict inheritance from a parent collection at any time, using the restrict inherited permissions option.

Once you restrict inheritance, you will need to add users directly to the restricted collection to grant them access.

1. Navigate to the collection where you want to restrict inheritance and select the **Role assignments** tab.
1. Select **Restrict inherited permissions** and select **Restrict access** in the popup dialog to remove inherited permissions from this collection and any subcollections. Note that collection admin permissions won't be affected.

    :::image type="content" source="./media/how-to-create-and-manage-collections/restrict-access-inheritance.png" alt-text="Screenshot of Azure Purview studio collection window, with the role assignments tab selected, and the restrict inherited permissions slide button highlighted." border="true":::

1. After restriction, inherited members are removed from the roles expect for collection admin.
1. Select the **Restrict inherited permissions** toggle button again to revert.

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-restriction.png" alt-text="Screenshot of Azure Purview studio collection window, with the role assignments tab selected, and the unrestrict inherited permissions slide button highlighted." border="true":::
    
## Register source to a collection

1. Select **Register** or register icon on collection node to register a data source. Only a data source admin can register sources.

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-by-collection.png" alt-text="Screenshot of the data map Azure Purview studio window with the register button highlighted both at the top of the page and under a collection."border="true":::

1. Fill in the data source name, and other source information.  It lists all the collections where you have scan permission on the bottom of the form. You can select one collection. All assets under this source will belong to the collection you select.

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-source.png" alt-text="Screenshot of the source registration window."border="true":::

1. The created data source will be put under the selected collection. Select **View details** to see the data source.

    :::image type="content" source="./media/how-to-create-and-manage-collections/see-registered-source.png" alt-text="Screenshot of the data map Azure Purview studio window with the newly added source card highlighted."border="true":::

1. Select **New scan** to create scan under the data source.

    :::image type="content" source="./media/how-to-create-and-manage-collections/new-scan.png" alt-text="Screenshot of a source Azure Purview studio window with the new scan button highlighted."border="true":::

1. Similarly, at the bottom of the form, you can select a collection, and all assets scanned will be included in the collection.
The collections listed here are restricted to subcollections of the data source collection.

    :::image type="content" source="./media/how-to-create-and-manage-collections/scan-under-collection.png" alt-text="Screenshot of a new scan window with the collection dropdown highlighted."border="true":::

1. Back in the collection window, you will see the data sources linked to the collection on the sources card.

    :::image type="content" source="./media/how-to-create-and-manage-collections/source-under-collection.png" alt-text="Screenshot of the data map Azure Purview studio window with the newly added source card highlighted in the map."border="true":::

## Add assets to collections

Assets and sources are also associated with collections. During a scan, if the scan was associated with a collection the assets will be automatically added to that collection, but can also be manually added to any subcollections.

1. Check the collection information in asset details. You can find collection information in the **Collection path** section on right-top corner of the asset details page.

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-path.png" alt-text="Screenshot of Azure Purview studio asset window, with the collection path highlighted." border="true":::

1. Permissions in asset details page:
    1. Check the collection-based permission model by following the [add roles and restricting access on collections guide above](#add-roles-and-restrict-access-through-collections).
    1. If you don't have read permission on a collection, the assets under that collection will not be listed in search results. If you get the direct URL of one asset and open it, you will see the no access page. Contact your Azure Purview admin to grant you the access. You can select the **Refresh** button to check the permission again.

        :::image type="content" source="./media/how-to-create-and-manage-collections/no-access.png" alt-text="Screenshot of Azure Purview studio asset window where the user has no permissions, and has no access to information or options." border="true":::

    1. If you have the read permission to one collection but don't have the write permission, you can browse the asset details page, but the following operations are disabled:
        * Edit the asset. The **Edit** button will be disabled.
        * Delete the asset. The **Delete** button will be disabled.
        * Move asset to another collection. The ellipsis button on the right-top corner of Collection path section will be hidden.
    1. The assets in **Hierarchy** section are also affected by permissions. Assets without read permission will be grayed.

        :::image type="content" source="./media/how-to-create-and-manage-collections/hierarchy-permissions.png" alt-text="Screenshot of Azure Purview studio hierarchy window where the user has only read permissions, and has no access to options." border="true":::

### Move asset to another collection

1. Select the ellipsis button on the right-top corner of Collection path section.

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-asset.png" alt-text="Screenshot of Azure Purview studio asset window with the collection path highlighted and the ellipsis button next to collection path selected." border="true":::

1. Select the **Move to another collection** button.
1. In the right side panel, choose the target collection you want move to. You can only see the collections where you have write permissions. The asset can also only be added to the subcollections of the data source collection.

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-select-collection.png" alt-text="Screenshot of Azure Purview studio pop-up window with the select a collection dropdown menu highlighted." border="true":::

1. Select **Move** button on the bottom of the window to move the asset.

## Search and browse by collections

### Search by collection

1. In Azure Purview, the search bar is located at the top of the Azure Purview studio UX.

   :::image type="content" source="./media/how-to-create-and-manage-collections/purview-search-bar.png" alt-text="Screenshot showing the location of the Azure Purview search bar" border="true":::

1. When you select the search bar, you can see your recent search history and recently accessed assets. Select **View all** to see all of the recently viewed assets.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-no-keywords.png" alt-text="Screenshot showing the search bar before any keywords have been entered" border="true":::

1. Enter in keywords that help identify your asset such as its name, data type, classifications, and glossary terms. As you enter in keywords relating to your desired asset, Azure Purview displays suggestions on what to search and potential asset matches. To complete your search, select **View search results** or press **Enter**.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-keywords.png" alt-text="Screenshot showing the search bar as a user enters in keywords" border="true":::

1. The search results page shows a list of assets that match the keywords provided in order of relevance. There are various factors that can affect the relevance score of an asset. You can filter down the list more by selecting specific collections, data stores, classifications, contacts, labels, and glossary terms that apply to the asset you are looking for.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-results.png" alt-text="Screenshot showing the results of a search" border="true":::

1. Select your desired asset to view the asset details page where you can view properties including schema, lineage, and asset owners.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-by-collection.png" alt-text="Screenshot showing search results with collections." border="true":::

### Browse by collection

1. You can browse data assets, by selecting the **Browse assets** on the homepage.

    :::image type="content" source="./media/how-to-create-and-manage-collections/browse-by-collection.png" alt-text="Screenshot of the catalog Azure Purview studio window with the browse assets button highlighted." border="true":::

1. On the Browse asset page, select **By collection** pivot. Collections are listed with hierarchical table view. To further explore assets in each collection, select the corresponding collection name.

    :::image type="content" source="./media/how-to-create-and-manage-collections/by-collection-view.png" alt-text="Screenshot of the asset Azure Purview studio window with the by collection tab selected."border="true":::

1. On the next page, the search results of the assets under selected collection will be shown. You can narrow the results by selecting the facet filters. Or you can see the assets under other collections by selecting the sub/related collection names.

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-results-by-collection.png" alt-text="Screenshot of the catalog Azure Purview studio window with the by collection tab selected."border="true":::

1. To view the details of an asset, select the asset name in the search result. Or you can check the assets and bulk edit them.

    :::image type="content" source="./media/how-to-create-and-manage-collections/view-asset-details.png" alt-text="Screenshot of the catalog Azure Purview studio window with the by collection tab selected and asset check boxes highlighted."border="true":::

## Next steps

Now that you have a collection, you can follow these guides below to add resources and scan.

* [Manage data sources](manage-data-sources.md)

* [Supported data sources](azure-purview-connector-overview.md)

* [Scan and ingestion](concept-scans-and-ingestion.md)
