---
title: Configure separation of duties for an access package in Azure AD entitlement management - Azure Active Directory
description: Learn how to configure separation of duties enforcement for requests for an access package in Azure Active Directory entitlement management.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: karenhoran
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/15/2021
ms.author: ajburnle
ms.reviewer: 
ms.collection: M365-identity-device-management

#Customer intent: As a global administrator or access package manager, I want to configure that a user cannot request an access package if they already have incompatible access.

---
# Configure separation of duties checks for an access package in Azure AD entitlement management (Preview)

In Azure AD entitlement management, you can configure multiple policies, with different settings for each user community that will need access through an access package.  For example, employees might only need manager approval to get access to certain apps, but guests coming in from other organizations may require both a sponsor and a resource team departmental manager to approve. In a policy for users already in the directory, you can specify a particular group of users for who can request access. However, you may have a requirement to avoid a user obtaining excessive access.  To meet this requirement, you will want to further restrict who can request access, based on the access the requestor already has.

With the separation of duties settings on an access package, you can configure that a user who is a member of a group or who already has an assignment to one access package cannot request an additional access package.

![myaccess experience for attempting to request incompatible access](./media/entitlement-management-access-package-incompatible/request-prevented.png)


## Scenarios for separation of duties checks

For example, you have an access package, *Marketing Campaign*, that people across your organization and other organizations can request access to, to work with your organization's marketing department while that campaign is going on. Since employees in the marketing department should already have access to that marketing campaign material, you don't want employees in the marketing department to request access to that access package.  Or, you may already have a dynamic group, *Marketing department employees*, with all of the marketing employees in it. You could indicate that the access package is incompatible with the membership of that dynamic group. Then, if a marketing department employee is looking for an access package to request, they couldn't request access to the *Marketing campaign* access package.

Similarly, you may have an application with two roles - **Western Sales** and **Eastern Sales** - and want to ensure that a user can only have one sales territory at a time.  If you have two access packages, one access package **Western Territory** giving the **Western Sales** role and the other access package **Eastern Territory** giving the **Eastern Sales** role, then you can configure
 - the **Western Territory** access package has the **Eastern Territory** package as incompatible, and
 - the **Eastern Territory** access package has the **Western Territory** package as incompatible.

If you’ve been using Microsoft Identity Manager or other on-premises identity management systems for automating access for on-premises apps, then you can integrate these systems with Azure AD entitlement management as well.  If you will be controlling access to Azure AD-integrated apps through entitlement management, and want to prevent users from having incompatible access, you can configure that an access package is incompatible with a group. That could be a group, which your on-premises identity management system sends into Azure AD through Azure AD Connect. This check ensures a user will be unable to request an access package, if that access package would give access that's incompatible with access the user has in on-premises apps.

## Prerequisites

To use Azure AD entitlement management and assign users to access packages, you must have one of the following licenses:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 license

## Configure another access package or group membership as incompatible for requesting access to an access package

**Prerequisite role**: Global administrator, Identity Governance administrator, User administrator, Catalog owner or Access package manager

Follow these steps to change the list of incompatible groups or other access packages for an existing access package:

1.	Sign in to the [Azure portal](https://portal.azure.com).

1.  Click **Azure Active Directory**, and then click **Identity Governance**.

1.	In the left menu, click **Access packages** and then open the access package which users will request.

1.	In the left menu, click **Separation of duties (preview)**.

1.  If you wish to prevent users who have another access package assignment already from requesting this access package, click on **Add access package** and select the access package that the user would already be assigned.


    ![configuration of incompatible access packages](./media/entitlement-management-access-package-incompatible/select-incompatible-ap.png)


1.  If you wish to prevent users who have an existing group membership from requesting this access package, then click on **Add group** and select the group that the user would already be in.

### Configure incompatible access packages programmatically

You can also configure the groups and other access packages that are incompatible with access package using Microsoft Graph.  A user in an appropriate role with an application that has the delegated `EntitlementManagement.ReadWrite.All` permission, or an application with that application permission, can call the API to add, remove, and list the incompatible groups and access packages [of an access package](/graph/api/resources/accesspackage).


## View other access packages that are configured as incompatible with this one

**Prerequisite role**: Global administrator, Identity Governance administrator, User administrator, Catalog owner or Access package manager

Follow these steps to view the list of other access packages that have indicated that they are incompatible with an existing access package:

1.	Sign in to the [Azure portal](https://portal.azure.com).

1.  Click **Azure Active Directory**, and then click **Identity Governance**.

1.	In the left menu, click **Access packages** and then open the access package.

1.	In the left menu, click **Separation of duties (preview)**.

1. Click on **Incompatible With**.

## Identifying users who already have incompatible access to another access package

If you are configuring incompatible access settings on an access package that already has users assigned to it, then any of those users who also have an assignment to the incompatible access package or groups will not be able to re-request access.

**Prerequisite role**: Global administrator, Identity Governance administrator, User administrator, Catalog owner or Access package manager

Follow these steps to view the list of users who have assignments to two access packages.

1.	Sign in to the [Azure portal](https://portal.azure.com).

1.  Click **Azure Active Directory**, and then click **Identity Governance**.

1.	In the left menu, click **Access packages** and then open the access package where you will be configuring incompatible assignments.

1.	In the left menu, click **Assignments**.

1.  In the **Status** field, ensure that **Delivered** status is selected.

1.  Click the **Download** button and save the resulting CSV file as the first file with a list of assignments.

1.  In the navigation bar, click **Identity Governance**.

1.	In the left menu, click **Access packages** and then open the access package which you plan to indicate as incompatible.

1.	In the left menu, click **Assignments**.

1.  In the **Status** field, ensure that the **Delivered** status is selected.

1.  Click the **Download** button and save the resulting CSV file as the second file with a list of assignments.

1.  Use a spreadsheet program such as Excel to open the two files.

1.  Users who are listed in both files will have already-existing incompatible assignments.

### Identifying users who already have incompatible access programmatically

You can also query the users who have assignments to an access package with the `Get-MgEntitlementManagementAccessPackageAssignment` cmdlet from the [Microsoft Graph PowerShell cmdlets for Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) module version 1.6.0 or later.

For example, if you have two access packages, one with ID `29be137f-b006-426c-b46a-0df3d4e25ccd` and the other with ID `cce10272-68d8-4482-8ba3-a5965c86cfe5`, then you could retrieve the users who have assignments to the first access package, and then compare them to the users who have assignments to the second access package. You can also report the users who have assignments delivered to both, using a PowerShell script similar to the following:

```powershell
$c = Connect-MgGraph -Scopes "EntitlementManagement.Read.All"
Select-MgProfile -Name "beta"
$ap_w_id = "29be137f-b006-426c-b46a-0df3d4e25ccd"
$ap_e_id = "cce10272-68d8-4482-8ba3-a5965c86cfe5"
$apa_w_filter = "accessPackage/id eq '" + $ap_w_id + "' and assignmentState eq 'Delivered'"
$apa_e_filter = "accessPackage/id eq '" + $ap_e_id + "' and assignmentState eq 'Delivered'"
$apa_w = Get-MgEntitlementManagementAccessPackageAssignment -Filter $apa_w_filter -ExpandProperty target -All
$apa_e = Get-MgEntitlementManagementAccessPackageAssignment -Filter $apa_e_filter -ExpandProperty target -All
$htt = @{}; foreach ($e in $apa_e) { if ($null -ne $e.Target -and $null -ne $e.Target.Id) {$htt[$e.Target.Id] = $e} }
foreach ($w in $apa_w) { if ($null -ne $w.Target -and $null -ne $w.Target.Id -and $htt.ContainsKey($w.Target.Id)) { write-output $w.Target.Email } }
```

## Configuring multiple access packages for override scenarios

If an access package has been configured as incompatible, then a user who has an assignment to that incompatible access package cannot request the access package, nor can an administrator make a new assignment that would be incompatible.

For example, if the **Production environment** access package has marked the **Development environment** package as incompatible, and a user has an assignment to the **Development environment** access package, then the access package manager for **Production environment** cannot create an assignment for that user to the **Production environment**.  In order to proceed with that assignment, the user's existing assignment to the **Development environment** access package must first be removed.

If there is an exceptional situation where separation of duties rules might need to be overridden, then configuring an additional access package to capture the users who have overlapping access rights will make it clear to the approvers, reviewers, and auditors the exceptional nature of those assignments.

For example, if there was a scenario that some users would need to have access to both production and deployment environments at the same time, you could create a new access package **Production and development environments**.  That access package could have as its resource roles some of the resource roles of the **Production environment** access package and some of the resource roles of the **Development environment** access package.  

If the motivation of the incompatible access is one resource's roles are particularly problematic, then that resource could be omitted from the combined access package, and require explicit administrator assignment of a user to the role.  If that is a third party application or your own application, then you can ensure oversight by monitoring those role assignments using the  *Application role assignment activity* workbook described in the next section.

Depending on your governance processes, that combined access package could have as its policy either:

 - a **direct assignments policy**, so that only an access package manager would be interacting with the access package, or
 - a **users can request access policy**, so that a user can request, with potentially an additional approval stage

This policy could have as its lifecycle settings a much shorter expiration number of days than a policy on other access packages, or require more frequent access reviews, with regular oversight so that users do not retain access longer than necessary.

## Monitor and report on access assignments

You can use Azure Monitor workbooks to get insights on how users have been receiving their access.

1. Configure Azure AD to [send audit events to Azure Monitor](entitlement-management-logs-and-reporting.md).

1. The workbook named *Access Package Activity* displays each event related to a particular access package.

    ![View access package events](./media/entitlement-management-logs-and-reporting/view-events-access-package.png)

1. To see if there have been changes to application role assignments for an application that were not created due to access package assignments, then you can select the workbook named *Application role assignment activity*.  If you select to omit entitlement activity, then only changes to application roles that were not made by entitlement management are shown. For example, you would see a row if a global administrator had directly assigned a user to an application role.

    ![View app role assignments](./media/entitlement-management-access-package-incompatible/workbook-ara.png)


## Next steps

- [View, add, and remove assignments for an access package](entitlement-management-access-package-assignments.md)
- [View reports and logs](entitlement-management-reports.md)
