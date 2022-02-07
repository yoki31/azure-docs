---
title: Supported Azure AD features
description: Learn about Azure AD features, which are still supported in Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG

ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/04/2022
ms.author: kengaderdus
ms.subservice: B2C
---

# Supported Azure AD features

An Azure AD B2C tenant is different than an Azure Active Directory tenant, which you may already have, but it relies on it. The following Azure AD features can be used in your Azure AD B2C tenant.

|Feature  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Groups](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Groups can be used to manage administrative and user accounts.| Groups can be used to manage administrative accounts. [Consumer accounts](user-overview.md#consumer-user) can't be member of any group, so you can't perform [group-based assignment of enterprise applications](../active-directory/manage-apps/assign-user-or-group-access-portal.md).|
| [Inviting External Identities guests](../active-directory//external-identities/add-users-administrator.md)| You can invite guest users and configure External Identities features such as federation and sign-in with Facebook and Google accounts. | You can invite only a Microsoft account or an Azure AD user as a guest to your Azure AD tenant for accessing applications or managing tenants. For [consumer accounts](user-overview.md#consumer-user), you use Azure AD B2C user flows and custom policies to manage users and sign-up or sign-in with external identity providers, such as Google or Facebook. |
| [Roles and administrators](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Fully supported for administrative and user accounts. | Roles are not supported with [consumer accounts](user-overview.md#consumer-user). Consumer accounts don't have access to any Azure resources.|
| [Custom domain names](../active-directory/fundamentals/add-custom-domain.md) |  You can use Azure AD custom domains for administrative accounts only. | [Consumer accounts](user-overview.md#consumer-user) can sign in with a username, phone number, or any email address. You can use [custom domains](custom-domain.md) in your redirect URLs.|
| [Conditional Access](../active-directory/conditional-access/overview.md) | Fully supported for administrative and user accounts. | A subset of Azure AD Conditional Access features is supported with [consumer accounts](user-overview.md#consumer-user) Lean how to configure Azure AD B2C [conditional access](conditional-access-user-flow.md).|
| [Premium P1](https://azure.microsoft.com/pricing/details/active-directory) | Fully supported for Azure AD premium P1 features. For example, [Password Protection](../active-directory/authentication/concept-password-ban-bad.md), [Hybrid Identities](../active-directory/hybrid/whatis-hybrid-identity.md),  [Conditional Access](../active-directory/roles/permissions-reference.md#), [Dynamic groups](../active-directory/enterprise-users/groups-create-rule.md), and more. | Azure AD B2C uses [Azure AD B2C Premium P1 license](https://azure.microsoft.com/pricing/details/active-directory/external-identities/), which is different from Azure AD premium P1. A subset of Azure AD Conditional Access features is supported with [consumer accounts](user-overview.md#consumer-user). Learn how to configure Azure AD B2C [Conditional Access](conditional-access-user-flow.md).|
| [Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) | Fully supported for Azure AD premium P2 features. For example, [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md), and [Identity Governance](../active-directory/governance/identity-governance-overview.md).  | Azure AD B2C uses [Azure AD B2C Premium P2 license](https://azure.microsoft.com/pricing/details/active-directory/external-identities/), which is different from Azure AD premium P2. A subset of Azure AD Identity Protection features is supported with [consumer accounts](user-overview.md#consumer-user). Learn how to [Investigate risk with Identity Protection](identity-protection-investigate-risk.md) and configure Azure AD B2C [Conditional Access](conditional-access-user-flow.md). |

> [!NOTE]
> **Other Azure resources in your tenant:** <br>In an Azure AD B2C tenant, you can't provision other Azure resources such as virtual machines, Azure web apps, or Azure functions. You must create these resources in your Azure AD tenant.
