---
title: B2B collaboration overview - Azure AD
description: Azure Active Directory B2B collaboration supports guest user access so you can securely share resources and collaborate with external partners.

services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 01/31/2022

ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: "it-pro, seo-update-azuread-jan"
ms.collection: M365-identity-device-management
---

# B2B collaboration overview

Azure Active Directory (Azure AD) B2B collaboration is a feature within External Identities that lets you invite guest users to collaborate with your organization. With B2B collaboration, you can securely share your company's applications and services with guest users from any other organization, while maintaining control over your own corporate data. Work safely and securely with external partners, large or small, even if they don't have Azure AD or an IT department.

![Diagram illustrating B2B collaboration](media/what-is-b2b/b2b-collaboration-overview.png)

A simple invitation and redemption process lets partners use their own credentials to access your company's resources. You can also enable self-service sign-up user flows to let external users sign up for apps or resources themselves. Once the external user has redeemed their invitation or completed sign-up, they're represented in your directory as a [user object](user-properties.md). B2B collaboration user objects are typically given a user type of "guest" and can be identified by the #EXT# extension in their user principal name.

Developers can use Azure AD business-to-business APIs to customize the invitation process or write applications like self-service sign-up portals. For licensing and pricing information related to guest users, refer to [Azure Active Directory External Identities pricing](https://azure.microsoft.com/pricing/details/active-directory/external-identities/).


> [!IMPORTANT]
> **As of November 1, 2021**, Microsoft no longer supports the redemption of invitations by creating unmanaged Azure AD accounts and tenants for B2B collaboration scenarios. At that time, we began rolling out a change to turn on the email one-time passcode feature for all existing tenants and enable it by default for new tenants. If you don't want to allow this feature to turn on automatically, you can [disable it](one-time-passcode.md#disable-email-one-time-passcode).

## Collaborate with any partner using their identities

With Azure AD B2B, the partner uses their own identity management solution, so there is no external administrative overhead for your organization. Guest users sign in to your apps and services with their own work, school, or social identities.

- The partner uses their own identities and credentials, whether or not they have an Azure AD account.
- You don't need to manage external accounts or passwords.
- You don't need to sync accounts or manage account lifecycles.

## Manage external access with inbound and outbound settings

B2B collaboration is enabled by default, but comprehensive admin settings let you control your B2B collaboration with external partners and organizations:

- For B2B collaboration with other Azure AD organizations, you can use [cross-tenant access settings](cross-tenant-access-overview.md) to manage inbound and outbound B2B collaboration and scope access to specific users, groups, and applications. You can set a default configuration that applies to all external organizations, and then create individual, organization-specific settings as needed. Using cross-tenant access settings, you can also trust multi-factor (MFA) and device claims (compliant claims and hybrid Azure AD joined claims) from other Azure AD organizations.

- You can use [external collaboration settings](external-collaboration-settings-configure.md) to limit who can invite external users, allow or block B2B specific domains, and set restrictions on guest user access to your directory.

## Easily invite guest users from the Azure AD portal

As an administrator, you can easily add guest users to your organization in the Azure portal.

- [Create a new guest user](b2b-quickstart-add-guest-users-portal.md) in Azure AD, similar to how you'd add a new user.
- Assign guest users to apps or groups.
- Send an invitation email that contains a redemption link, or send a direct link to an app you want to share.

![Screenshot showing the New Guest User invitation entry page](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Guest users follow a few simple [redemption steps](redemption-experience.md) to sign in.

![Screenshot showing the Review permissions page](media/what-is-b2b/consentscreen.png)

## Allow self-service sign-up

With a self-service sign-up user flow, you can create a sign-up experience for external users who want to access your apps. As part of the sign-up flow, you can provide options for different social or enterprise identity providers, and collect information about the user. Learn about [self-service sign-up and how to set it up](self-service-sign-up-overview.md).

You can also use [API connectors](api-connectors-overview.md) to integrate your self-service sign-up user flows with external cloud systems. You can connect with custom approval workflows, perform identity verification, validate user-provided information, and more.

![Screenshot showing the user flows page](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)

## Use policies to securely share your apps and services

You can use authentication and authorization policies to protect your corporate content. Conditional Access policies, such as multi-factor authentication, can be enforced:

- At the tenant level.
- At the application level.
- For specific guest users to protect corporate apps and data.

![Screenshot showing the Conditional Access option](media/what-is-b2b/tutorial-mfa-policy-2.png)



## Let application and group owners manage their own guest users

You can delegate guest user management to application owners so that they can add guest users directly to any application they want to share, whether it's a Microsoft application or not.

- Administrators set up self-service app and group management.
- Non-administrators use their [Access Panel](https://myapps.microsoft.com) to add guest users to applications or groups.

![Screenshot showing the Access panel for a guest user](media/what-is-b2b/access-panel-manage-app.png)

## Customize the onboarding experience for B2B guest users

Bring your external partners on board in ways customized to your organization's needs.

- Use [Azure AD entitlement management](../governance/entitlement-management-overview.md) to configure policies that [manage access for external users](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).
- Use the [B2B collaboration invitation APIs](/graph/api/resources/invitation) to customize your onboarding experiences.

## Integrate with Identity providers

Azure AD supports external identity providers like Facebook, Microsoft accounts, Google, or enterprise identity providers. You can set up federation with identity providers so your external users can sign in with their existing social or enterprise accounts instead of creating a new account just for your application. Learn more about [identity providers for External Identities](identity-providers.md).

![Screenshot showing the Identity providers page](media/what-is-b2b/identity-providers.png)

## Next steps

- [External Identities pricing](external-identities-pricing.md)
- [Add B2B collaboration guest users in the portal](add-users-administrator.md)
- [Understand the invitation redemption process](redemption-experience.md)