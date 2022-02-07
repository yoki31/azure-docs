---
title: Azure AD B2B in government and national clouds - Azure Active Directory
description: Learn what features are available in Azure Active Directory B2B collaboration in US Government and national clouds 

services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/31/2022

ms.author: mimart
author: msmimart
manager: celestedg

ms.collection: M365-identity-device-management
---

# Azure AD B2B in government and national clouds

## National clouds
[National clouds](../develop/authentication-national-cloud.md) are physically isolated instances of Azure. B2B collaboration is not supported across national cloud boundaries. For example, if your Azure tenant is in the public, global cloud, you can't invite a user whose account is in a national cloud. To collaborate with the user, ask them for another email address or create a member user account for them in your directory.

## Azure US Government clouds
Within the Azure US Government cloud, B2B collaboration is supported between tenants that are both within Azure US Government cloud and that both support B2B collaboration. Azure US Government tenants that support B2B collaboration can also collaborate with social users using Microsoft, Google accounts, or email one-time passcode accounts. If you invite a user outside of these groups (for example, if the user is in a tenant that isn't part of the Azure US Government cloud or doesn't yet support B2B collaboration), the invitation will fail or the user won't be able to redeem the invitation. For Microsoft accounts (MSAs), there are known limitations with accessing the Azure portal: newly invited MSA guests are unable to redeem direct link invitations to the Azure portal, and existing MSA guests are unable to sign in to the Azure portal. For details about other limitations, see [Azure Active Directory Premium P1 and P2 Variations](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

### How can I tell if B2B collaboration is available in my Azure US Government tenant?
To find out if your Azure US Government cloud tenant supports B2B collaboration, do the following:

1. In a browser, go to the following URL, substituting your tenant name for *&lt;tenantname&gt;*:

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Find `"tenant_region_scope"` in the JSON response:

   - If `"tenant_region_scope":"USGOV”` appears, B2B is supported.
   - If `"tenant_region_scope":"USG"` appears, B2B is not supported.

## Next steps

See the following articles on Azure AD B2B collaboration:

- [What is Azure AD B2B collaboration?](what-is-b2b.md)
- [Delegate B2B collaboration invitations](external-collaboration-settings-configure.md)