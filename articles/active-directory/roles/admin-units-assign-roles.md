---
title: Assign or list Azure AD roles with administrative unit scope - Azure Active Directory | Microsoft Docs
description: Use administrative units to restrict the scope of role assignments in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: karenhoran
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 01/28/2022
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
---

# Assign Azure AD roles with administrative unit scope

In Azure Active Directory (Azure AD), for more granular administrative control, you can assign an Azure AD role with a scope that's limited to one or more administrative units.

## Prerequisites

- Azure AD Premium P1 or P2 license for each administrative unit administrator
- Azure AD Free licenses for administrative unit members
- Privileged Role Administrator or Global Administrator
- AzureAD module when using PowerShell
- Admin consent when using Graph explorer for Microsoft Graph API

For more information, see [Prerequisites to use PowerShell or Graph Explorer](prerequisites.md).

## Roles that can be assigned with administrative unit scope

The following Azure AD roles can be assigned with administrative unit scope:

| Role | Description |
| -----| ----------- |
| [Authentication Administrator](permissions-reference.md#authentication-administrator) | Has access to view, set, and reset authentication method information for any non-admin user in the assigned administrative unit only. |
| [Groups Administrator](permissions-reference.md#groups-administrator) | Can manage all aspects of groups and groups settings, such as naming and expiration policies, in the assigned administrative unit only. |
| [Helpdesk Administrator](permissions-reference.md#helpdesk-administrator) | Can reset passwords for non-administrators in the assigned administrative unit only. |
| [License Administrator](permissions-reference.md#license-administrator) | Can assign, remove, and update license assignments within the administrative unit only. |
| [Password Administrator](permissions-reference.md#password-administrator) | Can reset passwords for non-administrators within the assigned administrative unit only. |
| [SharePoint Administrator](permissions-reference.md#sharepoint-administrator) * | Can manage all aspects of the SharePoint service. |
| [Teams Administrator](permissions-reference.md#teams-administrator) * | Can manage the Microsoft Teams service. |
| [Teams Devices Administrator](permissions-reference.md#teams-devices-administrator) | Can perform management related tasks on Teams certified devices. |
| [User Administrator](permissions-reference.md#user-administrator) | Can manage all aspects of users and groups, including resetting passwords for limited admins within the assigned administrative unit only. |

(*) The SharePoint Administrator and Teams Administrator roles can only be used for managing properties in the Microsoft 365 admin center. Teams admin center and SharePoint admin center currently do not support administrative unit-scoped administration.

Certain role permissions apply only to non-administrator users when assigned with the scope of an administrative unit. In other words, administrative unit scoped [Helpdesk Administrators](permissions-reference.md#helpdesk-administrator) can reset passwords for users in the administrative unit only if those users do not have administrator roles. The following list of permissions are restricted when the target of an action is another administrator:

-	Read and modify user authentication methods, or reset user passwords
-	Modify sensitive user properties such as telephone numbers, alternate email addresses, or OAuth secret keys
- Delete or restore user accounts

## Security principals that can be assigned with administrative unit scope

The following security principals can be assigned to a role with an administrative unit scope:

* Users
* Azure AD role-assignable groups
* Service principals

## Assign a role with an administrative unit scope

You can assign an Azure AD role with an administrative unit scope by using the Azure portal, PowerShell, or Microsoft Graph.

### Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com) or [Azure AD admin center](https://aad.portal.azure.com).

1. Select **Azure Active Directory** > **Administrative units** and then select the administrative unit that you want to assign a user role scope to. 

1. On the left pane, select **Roles and administrators** to list all the available roles.

   ![Screenshot of the "Role and administrators" pane for selecting an administrative unit whose role scope you want to assign.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Select the role to be assigned, and then select **Add assignments**. 

1. On the **Add assignments** pane, select one or more users to be assigned to the role.

   ![Select the role to scope and then select Add assignments](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> To assign a role on an administrative unit by using Azure AD Privileged Identity Management (PIM), see [Assign Azure AD roles in PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

You can change the highlighted section as required for the specific environment.

### Microsoft Graph API

Request

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
Body

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## List role assignments with administrative unit scope

You can view a list of Azure AD role assignments with administrative unit scope by using the Azure portal, PowerShell, or Microsoft Graph.

### Azure portal

You can view all the role assignments created with an administrative unit scope in the [Administrative units section of Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. Sign in to the [Azure portal](https://portal.azure.com) or [Azure AD admin center](https://aad.portal.azure.com).

1. Select **Azure Active Directory** > **Administrative units** and then select the administrative unit for the list of role assignments you want to view. 

1. Select **Roles and administrators**, and then open a role to view the assignments in the administrative unit.

### PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

You can change the highlighted section as required for your specific environment.

### Microsoft Graph API

Request

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

Body

```http
{}
```

## Next steps

- [Use Azure AD groups to manage role assignments](groups-concept.md)
- [Troubleshoot Azure AD roles assigned to groups](groups-faq-troubleshooting.yml)
