---
# Mandatory fields.
title: Create an app registration (portal)
titleSuffix: Azure Digital Twins
description: Learn how to create an Azure AD app registration, as an authentication option for client apps, using the Azure portal.
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 1/24/2022
ms.topic: how-to
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Create an app registration to use with Azure Digital Twins (portal)

[!INCLUDE [digital-twins-create-app-registration-selector.md](../../includes/digital-twins-create-app-registration-selector.md)]

This article describes how to create an app registration to use with Azure Digital Twins using the Azure portal. It includes instructions for creating the app registration, collecting important values, providing Azure Digital Twins API permission, verifying success, and other possible steps that your organization may require.

When working with an Azure Digital Twins instance, it's common to interact with that instance through client applications, such as the custom client app built in [Code a client app](tutorial-code.md). Those applications need to authenticate with Azure Digital Twins to interact with it, and some of the [authentication mechanisms](how-to-authenticate-client.md) that apps can use involve an [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **app registration**.

The app registration isn't required for all authentication scenarios. However, if you're using an authentication strategy or code sample that does require an app registration, this article shows you how to set one up using the [Azure portal](https://portal.azure.com). It also covers how to [collect important values](#collect-important-values) that you'll need to use the app registration to authenticate.

## Azure AD app registrations

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) is Microsoft's cloud-based identity and access management service. Setting up an **app registration** in Azure AD is one way to grant a client app access to Azure Digital Twins.

This app registration is where you configure access permissions to the [Azure Digital Twins APIs](concepts-apis-sdks.md). Later, client apps can authenticate against the app registration using the registration's **client and tenant ID values**, and as a result be granted the configured access permissions to the APIs.

>[!TIP]
> You may prefer to set up a new app registration every time you need one, *or* to do this only once, establishing a single app registration that will be shared among all scenarios that require it.

## Create the registration

Start by navigating to [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) in the Azure portal (you can use this link or find it with the portal search bar). Select *App registrations* from the service menu, and then *+ New registration*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Screenshot of the Azure AD service page in the Azure portal, showing the steps to create a new registration in the 'App registrations' page.":::

In the *Register an application* page that follows, fill in the requested values:
* **Name**: An Azure AD application display name to associate with the registration
* **Supported account types**: Select *Accounts in this organizational directory only (Default Directory only - Single tenant)*
* **Redirect URI**: An *Azure AD application reply URL* for the Azure AD application. Add a *Public client/native (mobile & desktop)* URI for `http://localhost`.

When you're finished, select the *Register* button.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Screenshot of the 'Register an application' page in the Azure portal with the described values filled in.":::

When the registration is finished setting up, the portal will redirect you to its details page.

## Collect important values

Next, collect some important values about the app registration that you'll need to use the app registration to authenticate a client application. These values include:
* **resource name**
* **client ID**
* **tenant ID**
* **client secret**

To work with Azure Digital Twins, the **resource name** is `http://digitaltwins.azure.net`.

The following sections describe how to find the other values.

### Collect client ID and tenant ID

The **client ID** and **tenant ID** values can be collected from the app registration's details page in the Azure portal:

:::image type="content" source="media/how-to-create-app-registration/client-id-tenant-id.png" alt-text="Screenshot of the Azure portal showing the important values for the app registration."  lightbox="media/how-to-create-app-registration/client-id-tenant-id.png":::

Take note of the **Application (client) ID** and **Directory (tenant) ID** shown on **your** page.

### Collect client secret

To set up a **client secret** for your app registration, start on your app registration page in the Azure portal. 

1. Select **Certificates and secrets** from the registration's menu, and then select **+ New client secret**.

    :::image type="content" source="media/how-to-create-app-registration/client-secret.png" alt-text="Screenshot of the Azure portal showing an Azure AD app registration and a highlight around 'New client secret'.":::

1. Enter whatever values you want for Description and Expires, and select **Add**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-create-app-registration/add-client-secret.png" alt-text="Screenshot of the Azure portal while adding a client secret.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Verify that the client secret is visible on the **Certificates & secrets** page with Expires and Value fields. 

1. Take note of its **Secret ID** and **Value** to use later (you can also copy them to the clipboard with the Copy icons).

    :::image type="content" source="media/how-to-create-app-registration/client-secret-value.png" alt-text="Screenshot of the Azure portal showing how to copy the client secret value.":::

>[!IMPORTANT]
>Make sure to copy the values now and store them in a safe place, as they can't be retrieved again. If you can't find them later, you'll have to create a new secret.

## Provide Azure Digital Twins permissions

Next, configure the app registration you've created with permissions to access Azure Digital Twins. First, **create a role assignment** for the app registration within the Azure Digital Twins instance. Then, **provide API permissions** for the app to read and write to the Azure Digital Twins APIs.

### Create role assignment

In this section, you'll create a role assignment for the app registration on the Azure Digital Twins instance. This role will determine what permissions the app registration holds on the instance, so you should select the role that matches the appropriate level of permission for your situation. One possible role is [Azure Digital Twins Data Owner](../role-based-access-control/built-in-roles.md#azure-digital-twins-data-owner). For a full list of roles and their descriptions, see [Azure built-in roles](../role-based-access-control/built-in-roles.md).

1. First, open the page for your Azure Digital Twins instance in the Azure portal. 

1. Select **Access control (IAM)**.

1. Select **Add** > **Add role assignment** to open the Add role assignment page.

1. Assign the appropriate role. For detailed steps, see [Assign Azure roles using the Azure portal](../role-based-access-control/role-assignments-portal.md).
    
    | Setting | Value |
    | --- | --- |
    | Role | Select as appropriate |
    | Assign access to | User, group, or service principal |
    | Members | Search for the name or [client ID](#collect-client-id-and-tenant-id) of the app registration |
    
    ![Add role assignment page](../../includes/role-based-access-control/media/add-role-assignment-page.png)

#### Verify role assignment

You can view the role assignment you've set up under *Access control (IAM) > Role assignments*.

:::image type="content" source="media/how-to-create-app-registration/verify-role-assignment.png" alt-text="Screenshot of the Role Assignments page for an Azure Digital Twins instance in the Azure portal.":::

The app registration should show up in the list along with the role you assigned to it. 

### Provide API permissions

In this section, you'll grant your app baseline read/write permissions to the Azure Digital Twins APIs.

From the portal page for your app registration, select *API permissions* from the menu. On the following permissions page, select the *+ Add a permission* button.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Screenshot of the app registration in the Azure portal, highlighting the 'API permissions' menu option and 'Add a permission' button.":::

In the *Request API permissions* page that follows, switch to the *APIs my organization uses* tab and search for *Azure digital twins*. Select _**Azure Digital Twins**_ from the search results to continue with assigning permissions for the Azure Digital Twins APIs.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Screenshot of the 'Request API Permissions' page search result in the Azure portal showing Azure Digital Twins.":::

>[!NOTE]
> If your subscription still has an existing Azure Digital Twins instance from the previous public preview of the service (before July 2020), you'll need to search for and select _**Azure Smart Spaces Service**_ instead. This is an older name for the same set of APIs (notice that the *Application (client) ID* is the same as in the screenshot above), and your experience won't be changed beyond this step.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Screenshot of the 'Request API Permissions' page search result showing Azure Smart Spaces Service in the Azure portal.":::

Next, you'll select which permissions to grant for these APIs. Expand the **Read (1)** permission and check the box that says *Read.Write* to grant this app registration reader and writer permissions.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Screenshot of the 'Request API Permissions' page and selecting 'Read.Write' permissions for the Azure Digital Twins APIs in the Azure portal.":::

Select *Add permissions* when finished.

#### Verify API permissions

On the *API permissions* page, verify that there's now an entry for Azure Digital Twins reflecting Read/Write permissions:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Screenshot of the API permissions for the Azure AD app registration in the Azure portal, showing 'Read/Write Access' for Azure Digital Twins.":::

You can also verify the connection to Azure Digital Twins within the app registration's *manifest.json*, which was automatically updated with the Azure Digital Twins information when you added the API permissions.

To do so, select **Manifest** from the menu to view the app registration's manifest code. Scroll to the bottom of the code window and look for the following fields and values under `requiredResourceAccess`: 
* `"resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0"`
* `"resourceAccess"` > `"id": "4589bd03-58cb-4e6c-b17f-b580e39652f8"`

These values are shown in the screenshot below:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Screenshot of the manifest for the Azure AD app registration in the Azure portal.":::

If these values are missing, retry the steps in the [section for adding the API permission](#provide-api-permissions).

## Other possible steps for your organization

It's possible that your organization requires more actions from subscription Owners/administrators to successfully set up an app registration. The steps required may vary depending on your organization's specific settings.

Here are some common potential activities that an Owner/administrator on the subscription may need to do. These and other operations can be performed from the [Azure AD App registrations](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) page in the Azure portal.
* Grant admin consent for the app registration. Your organization may have *Admin Consent Required* globally turned on in Azure AD for all app registrations within your subscription. If so, the Owner/administrator will need to select this button for your company on the app registration's *API permissions* page for the app registration to be valid:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Screenshot of the Azure portal showing the 'Grant admin consent' button under API permissions.":::
  - If consent was granted successfully, the entry for Azure Digital Twins should then show a *Status* value of _Granted for **(your company)**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Screenshot of the Azure portal showing the admin consent granted for the company under API permissions.":::
* Activate public client access
* Set specific reply URLs for web and desktop access
* Allow for implicit OAuth2 authentication flows

For more information about app registration and its different setup options, see [Register an application with the Microsoft identity platform](/graph/auth-register-app-v2).

## Next steps

In this article, you set up an Azure AD app registration that can be used to authenticate client applications with the Azure Digital Twins APIs.

Next, read about authentication mechanisms, including one that uses app registrations and others that don't:
* [Write app authentication code](how-to-authenticate-client.md)