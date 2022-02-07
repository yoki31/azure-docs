---
title: 'Azure AD Connect: Troubleshoot errors during synchronization | Microsoft Docs'
description: This article explains how to troubleshoot errors that occur during synchronization with Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: karenhoran
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 01/21/2022
ms.subservice: hybrid
ms.author: billmath
ms.custom: contperf-fy21q3-portal

ms.collection: M365-identity-device-management
---
# Understanding errors during Azure AD synchronization

Errors can occur when identity data is synced from Windows Server Active Directory to Azure Active Directory (Azure AD). This article provides an overview of different types of sync errors, some of the possible scenarios that cause those errors, and potential ways to fix the errors. This article includes common error types and might not cover all possible errors.

 This article assumes you're familiar with the underlying [design concepts of Azure AD and Azure AD Connect](plan-connect-design-concepts.md).

>[!IMPORTANT]
>This article attempts to address the most common synchronization errors.  Unfortunately, covering every scenario in one document is not possible.  For more information including in-depth troubleshooting steps, see [End-to-end troubleshooting of Azure AD Connect objects and attributes](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes) and the [User Provisioning and Synchronization](https://docs.microsoft.com/troubleshoot/azure/active-directory/welcome-azure-ad) section under the Azure AD troubleshooting documentation.

With the latest version of Azure AD Connect \(August 2016 or higher\), a Synchronization Errors Report is available in the [Azure portal](https://aka.ms/aadconnecthealth) as part of Azure AD Connect Health for sync.

Starting September 1, 2016, [Azure AD duplicate attribute resiliency](how-to-connect-syncservice-duplicate-attribute-resiliency.md) is enabled by default for all the *new* Azure AD tenants. This feature is automatically enabled for existing tenants.

Azure AD Connect performs three types of operations from the directories it keeps in sync: Import, Synchronization, and Export. Errors can occur in all three operations. This article mainly focuses on errors during export to Azure AD.

## Errors during export to Azure AD

The following section describes different types of synchronization errors that can occur during the export operation to Azure AD by using the Azure AD connector. You can identify this connector by the name format contoso.*onmicrosoft.com*.
Errors during export to Azure AD indicate that an operation like add, update, or delete attempted by Azure AD Connect \(sync engine\) on Azure AD failed.

![Diagram that shows the export errors overview.](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## Data mismatch errors

This section discusses data mismatch errors.

### InvalidSoftMatch

#### Description

* When Azure AD Connect \(sync engine\) instructs Azure AD to add or update objects, Azure AD matches the incoming object by using the **sourceAnchor** attribute and matching it to the **immutableId** attribute of objects in Azure AD. This match is called a *hard match*.
* When Azure AD *doesn't find* any object that matches the **immutableId** attribute with the **sourceAnchor** attribute of the incoming object, before Azure AD provisions a new object, it falls back to use the **proxyAddresses** and **userPrincipalName** attributes to find a match. This match is called a *soft match*. The soft match matches objects already present in Azure AD (that are sourced in Azure AD) with the new objects being added or updated during synchronization that represent the same entity (like users and groups) on-premises.
* The InvalidSoftMatch error occurs when the hard match doesn't find any matching object *and* the soft match finds a matching object, but that object has a different **immutableId** value than the incoming object's **sourceAnchor** attribute. This mismatch suggests that the matching object was synced with another object from on-premises Active Directory.

In other words, for the soft match to work, the object to be soft-matched with shouldn't have any value for the **immutableId** attribute. If any object with the **immutableId** attribute set with a value fails the hard match but satisfies the soft-match criteria, the operation results in an InvalidSoftMatch synchronization error.

Azure AD schema doesn't allow two or more objects to have the same value of the following attributes. This list isn't exhaustive:

* proxyAddresses
* userPrincipalName
* onPremisesSecurityIdentifier
* objectId

[Azure AD attribute duplicate attribute resiliency](how-to-connect-syncservice-duplicate-attribute-resiliency.md) is also being rolled out as the default behavior of Azure AD. This feature reduces the number of synchronization errors seen by Azure AD Connect and other sync clients. It makes Azure AD more resilient in the way it handles duplicated **proxyAddresses** and **userPrincipalName** attributes present in on-premises Active Directory environments. 

This feature doesn't fix the duplication errors, so the data still needs to be fixed. But it allows provisioning of new objects that are otherwise blocked from being provisioned because of duplicated values in Azure AD. This capability will also reduce the number of synchronization errors returned to the synchronization client.

> [!NOTE]
> If Azure AD attribute duplicate attribute resiliency is enabled for your tenant, you won't see the InvalidSoftMatch synchronization errors seen during provisioning of new objects.
>

#### Example scenarios for an InvalidSoftMatch error

- Two or more objects with the same value for the **proxyAddresses** attribute exist in on-premises Active Directory. Only one is getting provisioned in Azure AD.
- Two or more objects with the same value for the **userPrincipalName** attribute exist in on-premises Active Directory. Only one is getting provisioned in Azure AD.
- An object was added in on-premises Active Directory with the same value for the **proxyAddresses** attribute as that of an existing object in Azure AD. The object added on-premises isn't getting provisioned in Azure AD.
- An object was added in on-premises Active Directory with the same value for the **userPrincipalName** attribute as that of an account in Azure AD. The object isn't getting provisioned in Azure AD.
- A synced account was moved from Forest A to Forest B. Azure AD Connect (sync engine) was using the **objectGUID** attribute to compute the **sourceAnchor** attribute. After the forest move, the value of the **sourceAnchor** attribute is different. The new object from Forest B fails to sync with the existing object in Azure AD.
- A synced object was accidentally deleted from on-premises Active Directory and a new object was created in Active Directory for the same entity (such as user) without deleting the account in Azure AD. The new account fails to sync with the existing Azure AD object.
- Azure AD Connect was uninstalled and reinstalled. During the reinstallation, a different attribute was chosen as the **sourceAnchor** attribute. All the objects that had previously synced stopped syncing with the InvalidSoftMatch error.

#### Example case

1. Bob Smith is a synced user in Azure AD from the on-premises Active Directory of *contoso.com*.
1. Bob Smith's user principal name is set as bobs\@contoso.com.
1. The **sourceAnchor** attribute of **"abcdefghijklmnopqrstuv=="** is calculated by Azure AD Connect by using Bob Smith's **objectGUID** attribute from on-premises Active Directory. This attribute is the **immutableId** attribute for Bob Smith in Azure AD.
1. Bob also has the following values for the **proxyAddresses** attribute:
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * smtp: bob\@contoso.com
1. A new user, Bob Taylor, is added to the on-premises Active Directory.
1. Bob Taylor's user principal name is set as bobt\@contoso.com.
1. The **sourceAnchor** attribute of **"abcdefghijkl0123456789=="** is calculated by Azure AD Connect by using Bob Taylor's **objectGUID** attribute from on-premises Active Directory. Bob Taylor's object has *not* synced to Azure AD yet.
1. Bob Taylor has the following values for the **proxyAddresses** attribute:
   * smtp: bobt@contoso.com
   * smtp: bob.taylor@contoso.com
   * smtp: bob\@contoso.com
1. During sync, Azure AD Connect recognizes the addition of Bob Taylor in on-premises Active Directory and asks Azure AD to make the same change.
1. Azure AD first performs a hard match. That is, it searches for any object with the **immutableId** attribute equal to **"abcdefghijkl0123456789=="**. The hard match fails because no other object in Azure AD has that **immutableId** attribute.
1. Azure AD then performs a soft match to find Bob Taylor. That is, it searches to see if there's any object with **proxyAddresses** attributes equal to the three values, including smtp: bob@contoso.com.
1. Azure AD finds Bob Smith's object to match the soft-match criteria. But this object has the value of **immutableId = "abcdefghijklmnopqrstuv=="**, which indicates this object was synced from another object from on-premises Active Directory. Azure AD can't soft match these objects so an InvalidSoftMatch sync error is thrown.

#### Fix the InvalidSoftMatch error

The most common reason for the InvalidSoftMatch error is two objects with different **sourceAnchor** \(**immutableId**\) attributes that have the same value for the **proxyAddresses** or **userPrincipalName** attributes, which are used during the soft-match process on Azure AD. To fix the InvalidSoftMatch error:

1. Identify the duplicated **proxyAddresses**, **userPrincipalName**, or other attribute value that's causing the error. Also identify which two or more objects are involved in the conflict. The report generated by [Azure AD Connect Health for sync](./how-to-connect-health-sync.md) can help you identify the two objects.
1. Identify which object should continue to have the duplicated value and which object should not.
1. Remove the duplicated value from the object that should *not* have that value. Make the change in the directory from where the object is sourced. In some cases, you might need to delete one of the objects in conflict.
1. If you made the change in on-premises Active Directory, let Azure AD Connect sync the change.

Sync error reports within Azure AD Connect Health for sync are updated every 30 minutes and include the errors from the latest synchronization attempt.

> [!NOTE]
> The **ImmutableId** attribute, by definition, shouldn't change in the lifetime of the object. But maybe Azure AD Connect wasn't configured with some of the scenarios in mind from the preceding list. In that case, Azure AD Connect might calculate a different value of the **sourceAnchor** attribute for the Active Directory object that represents the same entity (same user, group, or contact) that has an existing Azure AD object that you want to continue using.
>
>

#### Related article

[Duplicate or invalid attributes prevent directory synchronization in Microsoft 365](https://support.microsoft.com/kb/2647098)

### ObjectTypeMismatch

#### Description

When Azure AD attempts to soft match two objects, it's possible that two objects of different "object type," like user, group, or contact, have the same values for the attributes used to perform the soft match. Because duplication of these attributes isn't permitted in Azure AD, the operation can result in an ObjectTypeMismatch sync error.

#### Example scenario for an ObjectTypeMismatch error

A mail-enabled security group is created in Microsoft 365. The admin adds a new user or contact in on-premises Active Directory that isn't synced to Azure AD yet with the same value for the **proxyAddresses** attribute as that of the Microsoft 365 group.

#### Example case

1. An admin creates a new mail-enabled security group in Microsoft 365 for the Tax department and provides an email address as tax@contoso.com. This group is assigned the **proxyAddresses** attribute value of **smtp: tax\@contoso.com**.
1. A new user joins Contoso.com and an account is created for the user on-premises with the **proxyAddresses** attribute as **smtp: tax\@contoso.com**.
1. When Azure AD Connect syncs the new user account, it gets the ObjectTypeMismatch error.

#### Fix the ObjectTypeMismatch error

The most common reason for the ObjectTypeMismatch error is that two objects of different type, like user, group, or contact, have the same value for the **proxyAddresses** attribute. To fix the ObjectTypeMismatch error:

1. Identify the duplicated **proxyAddresses** (or other attribute) value that's causing the error. Also identify which two or more objects are involved in the conflict. The report generated by [Azure AD Connect Health for sync](./how-to-connect-health-sync.md) can help you identify the two objects.
1. Identify which object should continue to have the duplicated value and which object should not.
1. Remove the duplicated value from the object that should *not* have that value. Make the change in the directory where the object is sourced from. In some cases, you might need to delete one of the objects in conflict.
1. If you made the change in the on-premises AD, let Azure AD Connect sync the change. The sync error report in Azure AD Connect Health for sync is updated every 30 minutes. The report includes the errors from the latest synchronization attempt.

## Duplicate attributes

This section discusses duplicate attribute errors.

### AttributeValueMustBeUnique

#### Description

Azure AD schema doesn't allow two or more objects to have the same value of the following attributes. Each object in Azure AD is forced to have a unique value of these attributes at a given instance:

* mail
* proxyAddresses
* signInName
* userPrincipalName

If Azure AD Connect attempts to add a new object or update an existing object with a value for the preceding attributes that's already assigned to another object in Azure AD, the operation results in the AttributeValueMustBeUnique sync error.

#### Possible scenario

A duplicate value is assigned to an already synced object, which conflicts with another synced object.

#### Example case

1. Bob Smith is a synced user in Azure AD from the on-premises Active Directory of contoso.com.
1. Bob Smith's user principal name on-premises is set as bobs\@contoso.com.
1. Bob also has the following values for the **proxyAddresses** attribute:
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * smtp: bob\@contoso.com
1. A new user, Bob Taylor, is added to on-premises Active Directory.
1. Bob Taylor's user principal name is set as bobt\@contoso.com.
1. Bob Taylor has the following values for the **proxyAddresses** attribute:
    * smtp: bobt@contoso.com
    * smtp: bob.taylor@contoso.com
1. Bob Taylor's object is synced with Azure AD successfully.
1. The admin decided to update Bob Taylor's **proxyAddresses** attribute with the following value:
    * smtp: bob\@contoso.com
1. Azure AD attempts to update Bob Taylor's object in Azure AD with the preceding value, but that operation fails because that **proxyAddresses** value is already assigned to Bob Smith. The result is an AttributeValueMustBeUnique error.

#### Fix the AttributeValueMustBeUnique error

The most common reason for the AttributeValueMustBeUnique error is that two objects with different **sourceAnchor** \(**immutableId**\) attributes have the same value for the **proxyAddresses** or **userPrincipalName** attributes. To fix the AttributeValueMustBeUnique error:

1. Identify the duplicated **proxyAddresses**, **userPrincipalName**, or other attribute value that's causing the error. Also identify which two or more objects are involved in the conflict. The report generated by [Azure AD Connect Health for sync](./how-to-connect-health-sync.md) can help you identify the two objects.
1. Identify which object should continue to have the duplicated value and which object should not.
1. Remove the duplicated value from the object that should *not* have that value. Make the change in the directory from where the object is sourced. In some cases, you might need to delete one of the objects in conflict.
1. If you made the change in on-premises Active Directory, let Azure AD Connect sync the change for the error to get fixed.

#### Related article

[Duplicate or invalid attributes prevent directory synchronization in Microsoft 365](https://support.microsoft.com/kb/2647098)

## Data validation failures

This section discusses data validation failures.

### IdentityDataValidationFailed

#### Description

Azure AD enforces various restrictions on the data itself before allowing that data to be written into the directory. These restrictions are to ensure that end users get the best possible experiences while using the applications that depend on this data.

#### Scenarios

- The **userPrincipalName** attribute value has invalid or unsupported characters.
- The **userPrincipalName** attribute doesn't follow the required format.

The result of the preceding scenarios is an IdentityDataValidationFailed error.

#### Fix the IdentityDataValidationFailed error

Ensure that the **userPrincipalName** attribute has supported characters and the required format.

#### Related article

[Prepare to provision users through directory synchronization to Microsoft 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

## Deletion access violation and password access violation errors

Azure AD protects cloud-only objects from being updated through Azure AD Connect. While it isn't possible to update these objects through Azure AD Connect, calls can be made directly to the AADConnect cloud-side back end to attempt to change cloud-only objects. When doing so, the following errors can be returned:

* This synchronization operation, Delete, isn't valid. Contact Technical Support.
* Unable to process this update because one or more cloud-only users' credential update is included in the current request.
* Deleting a cloud-only object isn't supported. Contact Microsoft Customer Support.
* The password change request can't be executed because it contains changes to one or more cloud-only user objects, which isn't supported. Contact Microsoft Customer Support.

## LargeObject or ExceededAllowedLength

This section discusses LargeObject or ExceededAllowedLength errors.

### Description

When an attribute exceeds the allowed size limit, length limit, or count limit set by Azure AD schema, the synchronization operation results in a LargeObject or ExceededAllowedLength sync error. Typically, this error occurs for the following attributes:

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

Azure AD doesn't impose limits per attribute, except for a hard-coded limit of 15 certificates in the **userCertificate** attribute and up to 100 attributes for [Directory extensions](how-to-connect-sync-feature-directory-extensions.md) with a maximum of 250 characters for each directory extension. There's a size limit for the whole object. When Azure AD Connect tries to sync an object that exceeds this object size limit, an export error is thrown.

All attributes contribute to the object's final size. Some attributes have different weight multipliers because of additional processing overhead. An example is indexed values. Also, different cloud services, service plans, and licenses might be assigned to the account, which consume even more attributes and contribute to the overall size of the object.

It isn't possible to determine exactly how many entries an attribute can hold in Azure AD, for example, how many SMTP addresses can fit in the **proxyAddresses** attribute. The amount depends on the size and multiplying factors of all the attributes populated in the object.

### Possible scenarios

- Bob's **userCertificate** attribute is storing too many certificates assigned to Bob. These certificates might include older, expired certificates. The hard limit is 15 certificates. For more information on how to handle LargeObject errors with the **userCertificate** attribute, see [Handling LargeObject errors caused by userCertificate attribute](tshoot-connect-largeobjecterror-usercertificate.md).
- Bob's **userSMIMECertificate** attribute is storing too many certificates assigned to Bob. These certificates might include older, expired certificates. The hard limit is 15 certificates.
- Bob's **thumbnailPhoto** attribute set in Active Directory is too large to be synced in Azure AD.
- During automatic population of the **proxyAddresses** attribute in Active Directory, an object has too many **proxyAddresses** attributes assigned.

The following examples demonstrate the different weights of attributes like **userCertificate** and **proxyAddresses**:

- A synced user that doesn't have any attributes populated other than the mandatory Active Directory attributes and Mail might be able to sync up to 332 proxy addresses.
- For a similar synced user that has a **mailNickName** attribute, plus 10 user certificates, the maximum number of proxy addresses decreases to 329.
- If a similar synced user with 10 user certificates plus, for instance, 4 subscriptions assigned (with all service plans enabled), the maximum number of proxy addresses decreases to 311.
- Now let's take the preceding user, which already holds the maximum number of proxy addresses, and say you need to add one more SMTP address. To achieve 312 proxy addresses, you would need to remove at least three user certificates (depending on the size of the certificate).

>[!NOTE]
> These numbers can vary slightly. As a rule of thumb, it's safer to assume that the limit of SMTP addresses in the **proxyAddresses** attribute is approximately 300 addresses to leave room for future growth of the object and its populated attributes.

### Fix the LargeObject or ExceededAllowedLength error

Review the user properties and remove attribute values that might no longer be required. Examples include revoked or expired certificates and outdated or unnecessary addresses, such as SMTP, X.400, X.500, MSMail, and CcMail.

## Existing Admin Role Conflict

### Description

An Existing Admin Role Conflict sync error occurs on a user object during synchronization when that user object has:

- Administrative permissions.
- The same **userPrincipalName** attribute as an existing Azure AD object.

Azure AD Connect isn't allowed to soft match a user object from on-premises AD with a user object in Azure AD that has an administrative role assigned to it. For more information, see [Azure AD userPrincipalName population](plan-connect-userprincipalname.md).

![Screenshot that shows the number of Existing Admin Role Conflict sync errors.](media/tshoot-connect-sync-errors/existingadmin.png)

### Fix the Existing Admin Role Conflict error

To resolve this issue:

1. Remove the Azure AD account (owner) from all admin roles.
1. Hard delete the quarantined object in the cloud.
1. The next sync cycle will take care of soft-matching the on-premises user to the cloud account because the cloud user is now no longer a global admin.
1. Restore the role memberships for the owner.

>[!NOTE]
>You can assign the administrative role to the existing user object again after the soft match between the on-premises user object and the Azure AD user object has finished.

## Related links

* [Locate Active Directory objects in Active Directory Administrative Center](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Query Azure AD for an object by using Azure AD PowerShell](/previous-versions/azure/jj151815(v=azure.100))
* [End-to-end troubleshooting of Azure AD Connect objects and attributes](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
* [Azure AD Troubleshooting](https://docs.microsoft.com/troubleshoot/azure/active-directory/welcome-azure-ad) 
