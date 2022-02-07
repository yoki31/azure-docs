---
title: Default user permissions - Azure Active Directory | Microsoft Docs
description: Learn about the user permissions available in Azure Active Directory.
services: active-directory
author: ajburnle
manager: karenhoran

ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: "it-pro, seodec18, contperf-fy21q1"
ms.collection: M365-identity-device-management
---

# What are the default user permissions in Azure Active Directory?
In Azure Active Directory (Azure AD), all users are granted a set of default permissions. A user's access consists of the type of user, their [role assignments](active-directory-users-assign-role-azure-portal.md), and their ownership of individual objects. 

This article describes those default permissions and compares the member and guest user defaults. The default user permissions can be changed only in user settings in Azure AD.

## Member and guest users
The set of default permissions depends on whether the user is a native member of the tenant (member user) or whether the user is brought over from another directory as a business-to-business (B2B) collaboration guest (guest user). For more information about adding guest users, see [What is Azure AD B2B collaboration?](../external-identities/what-is-b2b.md). Here are the capabilities of the default permissions:

* *Member users* can register applications, manage their own profile photo and mobile phone number, change their own password, and invite B2B guests. These users can also read all directory information (with a few exceptions). 
* *Guest users* have restricted directory permissions. They can manage their own profile, change their own password, and retrieve some information about other users, groups, and apps. However, they can't read all directory information. 

  For example, guest users can't enumerate the list of all users, groups, and other directory objects. Guests can be added to administrator roles, which grant them full read and write permissions. Guests can also invite other guests.

## Compare member and guest default permissions

**Area** | **Member user permissions** | **Default guest user permissions** | **Restricted guest user permissions**
------------ | --------- | ---------- | ----------
Users and contacts | <ul><li>Enumerate the list of all users and contacts<li>Read all public properties of users and contacts</li><li>Invite guests<li>Change their own password<li>Manage their own mobile phone number<li>Manage their own photo<li>Invalidate their own refresh tokens</li></ul> | <ul><li>Read their own properties<li>Read display name, email, sign-in name, photo, user principal name, and user type properties of other users and contacts<li>Change their own password<li>Search for another user by object ID (if allowed)<li>Read manager and direct report information of other users</li></ul> | <ul><li>Read their own properties<li>Change their own password</li><li>Manage their own mobile phone number</li></ul>
Groups | <ul><li>Create security groups<li>Create Microsoft 365 groups<li>Enumerate the list of all groups<li>Read all properties of groups<li>Read non-hidden group memberships<li>Read hidden Microsoft 365 group memberships for joined groups<li>Manage properties, ownership, and membership of groups that the user owns<li>Add guests to owned groups<li>Manage dynamic membership settings<li>Delete owned groups<li>Restore owned Microsoft 365 groups</li></ul> | <ul><li>Read properties of non-hidden groups, including membership and ownership (even non-joined groups)<li>Read hidden Microsoft 365 group memberships for joined groups<li>Search for groups by display name or object ID (if allowed)</li></ul> | <ul><li>Read object ID for joined groups<li>Read membership and ownership of joined groups in some Microsoft 365 apps (if allowed)</li></ul>
Applications | <ul><li>Register (create) new applications<li>Enumerate the list of all applications<li>Read properties of registered and enterprise applications<li>Manage application properties, assignments, and credentials for owned applications<li>Create or delete application passwords for users<li>Delete owned applications<li>Restore owned applications</li></ul> | <ul><li>Read properties of registered and enterprise applications</li></ul> | <ul><li>Read properties of registered and enterprise applications
Devices</li></ul> | <ul><li>Enumerate the list of all devices<li>Read all properties of devices<li>Manage all properties of owned devices</li></ul> | No permissions | No permissions
Directory | <ul><li>Read all company information<li>Read all domains<li>Read all partner contracts</li></ul> | <ul><li>Read company display name<li>Read all domains</li></ul> | <ul><li>Read company display name<li>Read all domains</li></ul>
Roles and scopes | <ul><li>Read all administrative roles and memberships<li>Read all properties and membership of administrative units</li></ul> | No permissions | No permissions
Subscriptions | <ul><li>Read all subscriptions<li>Enable service plan memberships</li></ul> | No permissions | No permissions
Policies | <ul><li>Read all properties of policies<li>Manage all properties of owned policies</li></ul> | No permissions | No permissions

## Restrict member users' default permissions 

It's possible to add restrictions to users' default permissions. You can use this feature if you don't want all users in the directory to have access to the Azure AD admin portal/directory. 

For example, a university has many users in its directory. The admin might not want all of the students in the directory to be able to see the full directory and violate other students' privacy. The use of this feature is optional and at the discretion of the Azure AD administrator. 

You can restrict default permissions for member users in the following ways:

Permission | Setting explanation
---------- | ------------
**Register applications** | Setting this option to **No** prevents users from creating application registrations. You can the grant the ability back to specific individuals by adding them to the application developer role.
**Allow users to connect work or school account with LinkedIn** | Setting this option to **No** prevents users from connecting their work or school account with their LinkedIn account. For more information, see [LinkedIn account connections data sharing and consent](../enterprise-users/linkedin-user-consent.md).
**Create security groups** | Setting this option to **No** prevents users from creating security groups. Global administrators and user administrators can still create security groups. To learn how, see [Azure Active Directory cmdlets for configuring group settings](../enterprise-users/groups-settings-cmdlets.md).
**Create Microsoft 365 groups** | Setting this option to **No** prevents users from creating Microsoft 365 groups. Setting this option to **Some** allows a set of users to create Microsoft 365 groups. Global administrators and user administrators can still create Microsoft 365 groups. To learn how, see [Azure Active Directory cmdlets for configuring group settings](../enterprise-users/groups-settings-cmdlets.md).
**Access the Azure AD administration portal** | <p>Setting this option to **No** lets non-administrators use the Azure AD administration portal to read and manage Azure AD resources. **Yes** restricts all non-administrators from accessing any Azure AD data in the administration portal.</p><p>This setting does not restrict access to Azure AD data by using PowerShell or other clients such as Visual Studio. When you set this option to **Yes** to grant a specific non-admin user the ability to use the Azure AD administration portal, assign any administrative role such as the directory reader role.</p><p>The directory reader role allows reading basic directory information. Member users have it by default. Guests and service principals don't.</p><p>This settings blocks non-admin users who are owners of groups or applications from using the Azure portal to manage their owned resources. This setting does not restrict access as long as a user is assigned a custom role (or any role) and is not just a user.</p>
**Read other users** | This setting is available in Microsoft Graph and PowerShell only. Setting this flag to `$false` prevents all non-admins from reading user information from the directory. This flag does not prevent reading user information in other Microsoft services like Exchange Online.</p><p>This setting is meant for special circumstances, so we don't recommend setting the flag to `$false`.

>[!NOTE]
>It's assumed that the average user would only use the portal to access Azure AD, and not use PowerShell or the Azure CLI to access their resources. Currently, restricting access to users' default permissions occurs only when users try to access the directory within the Azure portal.

## Restrict guest users' default permissions

You can restrict default permissions for guest users in the following ways.

>[!NOTE]
>The **Guest user access restrictions** setting replaced the **Guest users permissions are limited** setting. For guidance on using this feature, see [Restrict guest access permissions in Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Permission | Setting explanation
---------- | ------------
**Guest user access restrictions** | Setting this option to **Guest users have the same access as members** grants all member user permissions to guest users by default.<p>Setting this option to **Guest user access is restricted to properties and memberships of their own directory objects** restricts guest access to only their own user profile by default. Access to other users is no longer allowed, even when they're searching by user principal name, object ID, or display name. Access to group information, including groups memberships, is also no longer allowed.<p>This setting does not prevent access to joined groups in some Microsoft 365 services like Microsoft Teams. To learn more, see [Microsoft Teams guest access](/MicrosoftTeams/guest-access).<p>Guest users can still be added to administrator roles regardless of this permission setting.
**Guests can invite** | Setting this option to **Yes** allows guests to invite other guests. To learn more, see [Configure external collaboration settings](../external-identities/external-collaboration-settings-configure.md).
**Members can invite** | Setting this option to **Yes** allows non-admin members of your directory to invite guests. To learn more, see [Configure external collaboration settings](../external-identities/external-collaboration-settings-configure.md).
**Admins and users in the guest inviter role can invite** | Setting this option to **Yes** allows admins and users in the guest inviter role to invite guests. When you set this option to **Yes**, users in the guest inviter role will still be able to invite guests, regardless of the **Members can invite** setting. To learn more, see [Configure external collaboration settings](../external-identities/external-collaboration-settings-configure.md).

## Object ownership

### Application registration owner permissions
When a user registers an application, they're automatically added as an owner for the application. As an owner, they can manage the metadata of the application, such as the name and permissions that the app requests. They can also manage the tenant-specific configuration of the application, such as the single sign-on (SSO) configuration and user assignments. 

An owner can also add or remove other owners. Unlike global administrators, owners can manage only the applications that they own.

### Enterprise application owner permissions
When a user adds a new enterprise application, they're automatically added as an owner. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. 

An owner can also add or remove other owners. Unlike global administrators, owners can manage only the applications that they own.

### Group owner permissions
When a user creates a group, they're automatically added as an owner for that group. As an owner, they can manage properties of the group (such as the name) and manage group membership. 

An owner can also add or remove other owners. Unlike global administrators and user administrators, owners can manage only the groups that they own. 

To assign a group owner, see [Managing owners for a group](active-directory-accessmanagement-managing-group-owners.md).

### Ownership permissions
The following tables describe the specific permissions in Azure AD that member users have over owned objects. Users have these permissions only on objects that they own.

#### Owned application registrations
Users can perform the following actions on owned application registrations:

| **Action** | **Description** |
| --- | --- |
| microsoft.directory/applications/audience/update | Update the `applications.audience` property in Azure AD. |
| microsoft.directory/applications/authentication/update | Update the `applications.authentication` property in Azure AD. |
| microsoft.directory/applications/basic/update | Update basic properties on applications in Azure AD. |
| microsoft.directory/applications/credentials/update | Update the `applications.credentials` property in Azure AD. |
| microsoft.directory/applications/delete | Delete applications in Azure AD. |
| microsoft.directory/applications/owners/update | Update the `applications.owners` property in Azure AD. |
| microsoft.directory/applications/permissions/update | Update the `applications.permissions` property in Azure AD. |
| microsoft.directory/applications/policies/update | Update the `applications.policies` property in Azure AD. |
| microsoft.directory/applications/restore | Restore applications in Azure AD. |

#### Owned enterprise applications
Users can perform the following actions on owned enterprise applications. An enterprise application consists of a service principal, one or more application policies, and sometimes an application object in the same tenant as the service principal.

| **Action** | **Description** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Read all properties (including privileged properties) on audit logs in Azure AD. |
| microsoft.directory/policies/basic/update | Update basic properties on policies in Azure AD. |
| microsoft.directory/policies/delete | Delete policies in Azure AD. |
| microsoft.directory/policies/owners/update | Update the `policies.owners` property in Azure AD. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Update the `servicePrincipals.appRoleAssignedTo` property in Azure AD. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Update the `users.appRoleAssignments` property in Azure AD. |
| microsoft.directory/servicePrincipals/audience/update | Update the `servicePrincipals.audience` property in Azure AD. |
| microsoft.directory/servicePrincipals/authentication/update | Update the `servicePrincipals.authentication` property in Azure AD. |
| microsoft.directory/servicePrincipals/basic/update | Update basic properties on service principals in Azure AD. |
| microsoft.directory/servicePrincipals/credentials/update | Update the `servicePrincipals.credentials` property in Azure AD. |
| microsoft.directory/servicePrincipals/delete | Delete service principals in Azure AD. |
| microsoft.directory/servicePrincipals/owners/update | Update the `servicePrincipals.owners` property in Azure AD. |
| microsoft.directory/servicePrincipals/permissions/update | Update the `servicePrincipals.permissions` property in Azure AD. |
| microsoft.directory/servicePrincipals/policies/update | Update the `servicePrincipals.policies` property in Azure AD. |
| microsoft.directory/signInReports/allProperties/read | Read all properties (including privileged properties) on sign-in reports in Azure AD. |

#### Owned devices
Users can perform the following actions on owned devices:

| **Action** | **Description** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Read the `devices.bitLockerRecoveryKeys` property in Azure AD. |
| microsoft.directory/devices/disable | Disable devices in Azure AD. |

#### Owned groups
Users can perform the following actions on owned groups.

> [!NOTE]
> Owners of dynamic groups must have a global administrator, group administrator, Intune administrator, or user administrator role to edit group membership rules. For more information, see [Create or update a dynamic group in Azure Active Directory](../enterprise-users/groups-create-rule.md).

| **Action** | **Description** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Update the `groups.appRoleAssignments` property in Azure AD. |
| microsoft.directory/groups/basic/update | Update basic properties on groups in Azure AD. |
| microsoft.directory/groups/delete | Delete groups in Azure AD. |
| microsoft.directory/groups/members/update | Update the `groups.members` property in Azure AD. |
| microsoft.directory/groups/owners/update | Update the `groups.owners` property in Azure AD. |
| microsoft.directory/groups/restore | Restore groups in Azure AD. |
| microsoft.directory/groups/settings/update | Update the `groups.settings` property in Azure AD. |

## Next steps

* To learn more about the **Guest user access restrictions** setting, see [Restrict guest access permissions in Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* To learn more about how to assign Azure AD administrator roles, see [Assign a user to administrator roles in Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
* To learn more about how resource access is controlled in Microsoft Azure, see [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).
* For more information on how Azure AD relates to your Azure subscription, see [How Azure subscriptions are associated with Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Manage users](add-users-azure-active-directory.md).
