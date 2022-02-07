---
title: 'Quickstart: Create an Azure Purview account in the Azure portal'
description: This Quickstart describes how to create an Azure Purview account and configure permissions to begin using it.
author: nayenama
ms.author: nayenama
ms.date: 11/15/2021
ms.topic: quickstart
ms.service: purview
ms.custom: mode-ui
#Customer intent: As a data steward, I want create a new Azure Purview Account so that I can scan and classify my data.
---
# Quickstart: Create an Azure Purview account in the Azure portal

This quickstart describes the steps to create an Azure Purview account in the Azure portal and get started on the process of classifying, securing, and discovering your data in Azure Purview!

Azure Purview is a data governance service that helps you manage and govern your data landscape. By connecting to data across your on-premises, multi-cloud, and software-as-a-service (SaaS) sources, Azure Purview creates an up-to-date map of your information. It identifies and classifies sensitive data, and provides end-to-end linage. Data consumers are able to discover data across your organization, and data administrators are able to audit, secure, and ensure right use of your data.

For more information about Azure Purview, [see our overview page](overview.md). For more information about deploying Azure Purview across your organization, [see our deployment best practices](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## Create an Azure Purview account

1. Go to the **Azure Purview accounts** page in the [Azure portal](https://portal.azure.com).

    :::image type="content" source="media/create-catalog-portal/purview-accounts-page.png" alt-text="Screenshot showing the purview accounts page in the Azure portal":::

1. Select **Create** to create a new Azure Purview account.

   :::image type="content" source="media/create-catalog-portal/select-create.png" alt-text="Screenshot with the create button highlighted an Azure Purview in the Azure portal.":::
  
      Or instead, you can go to the marketplace, search for **Azure Purview**, and select **Create**.

     :::image type="content" source="media/create-catalog-portal/search-marketplace.png" alt-text="Screenshot showing Azure Purview in the Azure Marketplace, with the create button highlighted.":::

1. On the new Create Azure Purview account page, under the **Basics** tab, select the Azure subscription where you want to create your Azure Purview account.

1. Select an existing **resource group** or create a new one to hold your Azure Purview account.

    To learn more about resource groups, see our article on [using resource groups to manage your Azure resources](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

1. Enter a **Azure Purview account name**. Spaces and symbols aren't allowed.
    The name of the Azure Purview account must be globally unique. If you see the following error, change the name of Azure Purview account and try creating again.

    :::image type="content" source="media/create-catalog-portal/name-error.png" alt-text="Screenshot showing the Create Azure Purview account screen with an account name that is already in use, and the error message highlighted.":::

1. Choose a **location**.
    The list shows only locations that support Azure Purview. The location you choose will be the region where your Azure Purview account and meta data will be stored. Sources can be housed in other regions.

      > [!Note]
      > Azure Purview does not support moving accounts across regions, so be sure to deploy to the correction region. You can find out more information about this in [move operation support for resources](../azure-resource-manager/management/move-support-resources.md).

1. Select **Review & Create**, and then select **Create**. It takes a few minutes to complete the creation. The newly created Azure Purview account instance will appear in the list on your **Azure Purview accounts** page.

    :::image type="content" source="media/create-catalog-portal/create-resource.png" alt-text="Screenshot showing the Create Azure Purview account screen with the Review + Create button highlighted":::

## Open Azure Purview Studio

After your Azure Purview account is created, you'll use the Azure Purview Studio to access and manage it. There are two ways to open Azure Purview Studio:

* Open your Azure Purview account in the [Azure portal](https://portal.azure.com). Select the "Open Azure Purview Studio" tile on the overview page.
    :::image type="content" source="media/create-catalog-portal/open-purview-studio.png" alt-text="Screenshot showing the Azure Purview account overview page, with the Azure Purview Studio tile highlighted.":::

* Alternatively, you can browse to [https://web.purview.azure.com](https://web.purview.azure.com), select your Azure Purview account, and sign in to your workspace.

## Next steps

In this quickstart, you learned how to create an Azure Purview account and how to access it through the Azure Purview Studio.

Next, you can create a user-assigned managed identity (UAMI) that will enable your new Azure Purview account to authenticate directly with resources using Azure Active Directory (Azure AD) authentication.

To create a UAMI, follow our [guide to create a user-assigned managed identity](manage-credentials.md#create-a-user-assigned-managed-identity).

Follow these next articles to learn how to navigate the Azure Purview Studio, create a collection, and grant access to Azure Purview:

* [Using the Azure Purview Studio](use-azure-purview-studio.md)
* [Create a collection](quickstart-create-collection.md)
* [Add users to your Azure Purview account](catalog-permissions.md)
