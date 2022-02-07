---
title: Create and manage a workspace
titleSuffix: Azure Cognitive Services
description: How to create and manage workspaces
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 01/20/2022
ms.author: lajanuar
ms.topic: conceptual

---

# Create and manage a workspace | Preview

> [!IMPORTANT]
> Custom Translator v2.0 is currently in public preview. Some features may not be supported or have constrained capabilities.

 Workspaces are places to manage your documents, projects, and models. When you create a workspace, you can choose to use the workspace independently or share it with teammates to divide up the work.

## Create workspace

1. After you sign in to Custom Translator, you will be asked for permission to read your profile from the Microsoft identity platform to request your user access token and refresh token. Both tokens are needed for authentication and to ensure that you aren't signed out during your live session or while training your models. </br>Select **Yes**.

    :::image type="content" source="../media/quickstart/first-time-user.png" alt-text="Screenshot illustrating first-time sign-in.":::

1. Select **My workspaces**

1. Select **Create a new workspace**

1. Type a **Workspace name** and select **Next**

1. Select "Global" for **Select resource region** from the dropdown list.

1. Copy/paste your Translator Services key.

1. Select **Next**.

1. Select **Done**

   >[!Note]
   > Region must match the region that was selected during the resource creation. You can use **KEY 1** or **KEY 2.**

   :::image type="content" source="../media/quickstart/resource-key.png" alt-text="Screenshot illustrating the resource key.":::

   :::image type="content" source="../media/quickstart/create-workspace-1.png" alt-text="Screenshot illustrating workspace creation.":::

## Manage workspace settings

Select a workspace and navigate to **Workspace settings**. You can manage the following workspace settings:

* Change the resource key for global regions. If you are using a regional specific resource, you cannot change your resource key.

* Change the workspace name.

* [Share the workspace with others](#share-workspace-for-collaboration).

* Delete the workspace.

### Share workspace for collaboration

The person who created the workspace is the owner. Within **Workspace settings**, an owner can designate three different roles for a collaborative workspace:

* **Owner**. An owner has full permissions within the workspace.

* **Editor**. An editor can add documents, train models, and delete documents and projects. They cannot modify who the workspace is shared with, delete the workspace, or change the workspace name.

* **Reader**. A reader can view (and download if available) all information in the workspace.

1. Select **Share**.

1. Complete the **email address** field for collaborators.

1. Select **role** from the dropdown list.

1. Select **Share**.

:::image type="content" source="../media/quickstart/manage-workspace-settings-1.png" alt-text="Screenshot illustrating how to share a workspace.":::

:::image type="content" source="../media/quickstart/manage-workspace-settings-2.png" alt-text="Screenshot illustrating share workspace settings.":::

### Remove somebody from a workspace

1. Select **Share**.

2. Select the **X** icon next to the **Role** and email address that you want to remove.

:::image type="content" source="../media/quickstart/manage-workspace-settings-3.png" alt-text="Screenshot illustrating how to unshare a workspace.":::

## Next steps

> [!div class="nextstepaction"]
> [Learn how to manage projects](create-manage-project.md)