---
title: 'Azure AD Connect sync V2 endpoint | Microsoft Docs'
description: This document covers updates to the Azure AD connect sync v2 endpoints API.
services: active-directory
author: billmath
manager: karenhoran
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2022
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# Azure AD Connect sync V2 endpoint API 
Microsoft has deployed a new endpoint (API) for Azure AD Connect that improves the performance of the synchronization service operations to Azure Active Directory. By using the new V2 endpoint, you'll experience noticeable performance gains on export and import to Azure AD. This new endpoint supports:
    
 - Syncing groups with up to 250k members.
 - Performance gains on export and import to Azure AD.
 
> [!NOTE]
> Currently, the new endpoint does not have a configured group size limit for Microsoft 365 groups that are written back. This may have an effect on your Active Directory and sync cycle latencies. It is recommended to increase your group sizes incrementally.  

>[!NOTE]
> The Azure AD Connect sync V2 endpoint API is Generally Available but currently can only be used in these Azure environments:
> - Azure Commercial
> - Azure China cloud
> - Azure US Government cloud
> It will not be made available in the Azure German cloud

## Prerequisites  
In order to use the new V2 endpoint, you'll need to use Azure AD Connect v2.0. When you deploy AADConnect V2.0, the V2 endpoint will be automatically enabled.
There is a known issue where upgrading to the latest 1.6 build resets the group membership limit to 50k. When a server is upgraded to AADConnect 1.6, then the customer should reapply the rule changes that they applied when initially increasing the group membership limit to 250k before they enable sync for the server. 

## Frequently asked questions  
 
**When will the new end point become the default for upgrades and new installations?**  
The V2 endpoint is the default setting for AADConnect V2.0, and we advise customers to upgrade to AADConnect V2.0 to leverage the benefits of this endpoint.
There is an issue where customers who have the V2 endpoint running with an older version and try to upgrade to a newer V1.6 release will see that the 50K limitation on group membership is reinstated. When a server is upgraded to AADConnect 1.6, then the customer should reapply the rule changes that they applied when initially increasing the group membership limit to 250k before they enable sync for the server. 

## Next steps

* [Azure AD Connect sync: Understand and customize synchronization](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md)
