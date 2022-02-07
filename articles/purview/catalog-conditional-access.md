---
title: Configure Azure AD Conditional Access for Azure Purview
description: This article describes steps how to configure Azure AD Conditional Access for Azure Purview
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/14/2022
# Customer intent: As an identity and security admin, I want to set up Azure Active Directory Conditional Access for Azure Purview, for secure access.
---

# Conditional Access with Azure Purview

[Azure Purview](./overview.md) supports Microsoft Conditional Access.

The following steps show how to configure Azure Purview to enforce a Conditional Access policy.  

## Prerequisites

- When multi-factor authentication is enabled, to sign in to Azure Purview Studio, you must perform multi-factor authentication.

## Configure conditional access

1. Sign in to the Azure portal, select **Azure Active Directory**, and then select **Conditional Access**. For more information, see [Azure Active Directory Conditional Access technical reference](../active-directory/conditional-access/concept-conditional-access-conditions.md).  

  :::image type="content" source="media/catalog-conditional-access/conditional-access-blade.png" alt-text="Screenshot that shows Conditional Access blade"lightbox="media/catalog-conditional-access/conditional-access-blade.png":::
  
1. In the **Conditional Access-Policies** menu, select **New policy**, provide a name, and then select **Configure rules**.  
1. Under **Assignments**, select **Users and groups**, check **Select users and groups**, and then select the user or group for Conditional Access. Select **Select**, and then select **Done** to accept your selection.  

  :::image type="content" source="media/catalog-conditional-access/select-users-and-groups.png" alt-text="Screenshot that shows User and Group selection"lightbox="media/catalog-conditional-access/select-users-and-groups.png":::

1. Select **Cloud apps**, select **Select apps**. You see all apps available for Conditional Access. Select **Azure Purview**, at the bottom select **Select**, and then select **Done**.  
  
    :::image type="content" source="media/catalog-conditional-access/select-azure-purview.png" alt-text="Screenshot that shows Applications selection"lightbox="media/catalog-conditional-access/select-azure-purview.png":::

1. Select **Access controls**, select **Grant**, and then check the policy you want to apply. For this example, we select **Require multi-factor authentication**.  

  :::image type="content" source="media/catalog-conditional-access/grant-access.png" alt-text="Screenshot that shows Grant access tab"lightbox="media/catalog-conditional-access/grant-access.png":::

1. Set **Enable policy** to **On** and select **Create**.

## Next steps

- [Use Azure Purview Studio](./use-purview-studio.md)
