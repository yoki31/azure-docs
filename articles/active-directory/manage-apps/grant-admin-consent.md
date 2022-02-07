---
title: Grant tenant-wide admin consent to an application 
titleSuffix: Azure AD
description: Learn how to grant tenant-wide consent to an application so that end-users are not prompted for consent when signing in to an application.
services: active-directory
author: eringreenlee
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2021
ms.author: ergreenl
ms.reviewer: davidmu
ms.collection: M365-identity-device-management
ms.custom: contperf-fy22q2

#customer intent: As an admin, I want to grant tenant-wide admin consent to an application in Azure AD.
---

# Grant tenant-wide admin consent to an application

  In this article, you'll learn how to grant tenant-wide admin consent to an application in Azure Active Directory (Azure AD).

When you grant tenant-wide admin consent to an application, all users can sign in to the app. To restrict which users can sign in to an application, configure the app to require user assignment and then assign users or groups to the application. 

Tenant-wide admin consent to an app grants the app and the app's publisher access to your organization's data. Carefully review the permissions that the application is requesting before you grant consent. For more information on consenting to applications, see [Azure Active Directory consent framework](../develop/consent-framework.md).

Granting tenant-wide admin consent may revoke any permissions which had previously been granted tenant-wide. Permissions which have previously been granted by users on their own behalf will not be affected.

## Prerequisites

Granting tenant-wide admin consent requires you to sign in as a user that is authorized to consent on behalf of the organization.

To grant tenant-wide admin consent, you need:

- An Azure account with one of the following roles: Global Administrator, Privileged Role Administrator, Cloud Application Administrator, or Application Administrator. A user can also be authorized to grant tenant-wide consent if they are assigned a custom directory role that includes the [permission to grant permissions to applications](../roles/custom-consent-permissions.md).

## Grant tenant-wide admin consent in Enterprise apps

You can grant tenant-wide admin consent through *Enterprise applications* if the application has already been provisioned in your tenant. For example, an app could be provisioned in your tenant if at least one user has already consented to the application. For more information, see [How and why applications are added to Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

To grant tenant-wide admin consent to an app listed in **Enterprise applications**:

1. Sign in to the [Azure portal](https://portal.azure.com) with one of the roles listed in the prerequisites section.
1. Select **Azure Active Directory**, and then select **Enterprise applications**.
1. Select the application to which you want to grant tenant-wide admin consent, and then select **Permissions**.
   :::image type="content" source="media/grant-tenant-wide-admin-consent/grant-tenant-wide-admin-consent.png" alt-text="Screenshot shows how to grant tenant-wide admin consent.":::

1. Carefully review the permissions that the application requires. If you agree with the permissions the application requires, select **Grant admin consent**.

## Grant admin consent in App registrations

For applications your organization has developed, or which are registered directly in your Azure AD tenant, you can also grant tenant-wide admin consent from **App registrations** in the Azure portal.

To grant tenant-wide admin consent from **App registrations**:

1. Sign in to the [Azure portal](https://portal.azure.com) with one of the roles listed in the prerequisites section.
1. Select **Azure Active Directory**, and then select **App registrations**.
1. Select the application to which you want to grant tenant-wide admin consent.
1. Select **API permissions**.
1. Carefully review the permissions that the application requires. If you agree, select **Grant admin consent**.

## Construct the URL for granting tenant-wide admin consent

When granting tenant-wide admin consent using either method described above, a window opens from the Azure portal to prompt for tenant-wide admin consent. If you know the client ID (also known as the application ID) of the application, you can build the same URL to grant tenant-wide admin consent.

The tenant-wide admin consent URL follows the following format:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

where:

- `{client-id}` is the application's client ID (also known as app ID).
- `{tenant-id}` is your organization's tenant ID or any verified domain name.

As always, carefully review the permissions an application requests before granting consent.

## Next steps

[Configure how end-users consent to applications](configure-user-consent.md)

[Configure the admin consent workflow](configure-admin-consent-workflow.md)
