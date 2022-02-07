---
title: Add or deactivate custom security attributes in Azure AD (Preview) - Azure Active Directory
description: Learn how to add new custom security attributes or deactivate custom security attributes in Azure Active Directory.
services: active-directory
author: rolyon
ms.author: rolyon
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 02/03/2022
ms.collection: M365-identity-device-management
---

# Add or deactivate custom security attributes in Azure AD (Preview)

> [!IMPORTANT]
> Custom security attributes are currently in PREVIEW.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

[Custom security attributes](custom-security-attributes-overview.md) in Azure Active Directory (Azure AD) are business-specific attributes (key-value pairs) that you can define and assign to Azure AD objects. This article describes how to add, edit, or deactivate custom security attributes.

## Prerequisites

To add or deactivate custom security attributes, you must have:

- Azure AD Premium P1 or P2 license
- [Attribute Definition Administrator](../roles/permissions-reference.md#attribute-definition-administrator)
- [AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview) version 2.0.2.138 or later when using PowerShell

> [!IMPORTANT]
> By default, [Global Administrator](../roles/permissions-reference.md#global-administrator) and other administrator roles do not have permissions to read, define, or assign custom security attributes.

## Add an attribute set

An attribute set is a collection of related attributes. All custom security attributes must be part of an attribute set. Attribute sets cannot be renamed or deleted.

1. Sign in to the [Azure portal](https://portal.azure.com) or [Azure AD admin center](https://aad.portal.azure.com).

1. Click **Azure Active Directory** > **Custom security attributes (Preview)**.

1. Click **Add attribute set** to add a new attribute set.

    If Add attribute set is disabled, make sure you are assigned the Attribute Definition Administrator role. For more information, see [Troubleshoot custom security attributes](custom-security-attributes-troubleshoot.md).

1. Enter a name, description, and maximum number of attributes.

    An attribute set name can be 32 characters with no spaces or special characters. Once you've specified a name, you can't rename it. For more information, see [Limits and constraints](custom-security-attributes-overview.md#limits-and-constraints).

    ![Screenshot of New attribute set pane in Azure portal.](./media/custom-security-attributes-add/attribute-set-add.png)

1. When finished, click **Add**.

    The new attribute set appears in the list of attribute sets.

## Add a custom security attribute

1. Sign in to the [Azure portal](https://portal.azure.com) or [Azure AD admin center](https://aad.portal.azure.com).

1. Click **Azure Active Directory** > **Custom security attributes (Preview)**.

1. On the Custom security attributes page, find an existing attribute set or click **Add attribute set** to add a new attribute set.

    All custom security attributes must be part of an attribute set.

1. Click to open the selected attribute set.
 
1. Click **Add attribute** to add a new custom security attribute to the attribute set.

    ![Screenshot of New attribute pane in Azure portal.](./media/custom-security-attributes-add/attribute-new.png)

1. In the **Attribute name** box, enter a custom security attribute name.

    A custom security attribute name can be 32 characters with no spaces or special characters. Once you've specified a name, you can't rename it. For more information, see [Limits and constraints](custom-security-attributes-overview.md#limits-and-constraints).

1. In the **Description** box, enter an optional description.

    A description can be 128 characters long. If necessary, you can later change the description.

1. From the **Data type** list, select the data type for the custom security attribute.

    | Data type | Description |
    | --- | --- |
    | Boolean | A Boolean value that can be true, True, false, or False. |
    | Integer | A 32-bit integer. |
    | String | A string that can be X characters long. |
    
1. For **Allow multiple values to be assigned**, select **Yes** or **No**.

    Select **Yes** to allow multiple values to be assigned to this custom security attribute. Select **No** to only allow a single value to be assigned to this custom security attribute.

1. For **Only allow predefined values to be assigned**, select **Yes** or **No**.

    Select **Yes** to require that this custom security attribute be assigned values from a predefined values list. Select **No** to allow this custom security attribute to be assigned user-defined values or potentially predefined values.

1. If **Only allow predefined values to be assigned** is **Yes**, click **Add value** to add predefined values.

    An active value is available for assignment to objects. A value that is not active is defined, but not yet available for assignment.

    ![Screenshot of New attribute pane with Add predefined value pane in Azure portal.](./media/custom-security-attributes-add/attribute-new-value-add.png)


1. When finished, click **Save**.

    The new custom security attribute appears in the list of custom security attributes.

1. If you want to include predefined values, follow the steps in the next section.

## Edit a custom security attribute

Once you add a new custom security attribute, you can later edit some of the properties. Some properties are immutable and cannot be changed.

1. Sign in to the [Azure portal](https://portal.azure.com) or [Azure AD admin center](https://aad.portal.azure.com).

1. Click **Azure Active Directory** > **Custom security attributes (Preview)**.

1. Click the attribute set that includes the custom security attribute you want to edit.

1. In the list of custom security attributes, click the ellipsis for the custom security attribute you want to edit and then click **Edit attribute**.
  
1. Edit the properties that are enabled.
  
1. If **Only allow predefined values to be assigned** is **Yes**, click **Add value** to add predefined values. Click an existing predefined value to change the **Is active?** setting.

    ![Screenshot of Add predefined value pane in Azure portal.](./media/custom-security-attributes-add/attribute-predefined-value-add.png)

## Deactivate a custom security attribute

Once you add a custom security attribute, you can't delete it. However, you can deactivate a custom security attribute.

1. Sign in to the [Azure portal](https://portal.azure.com) or [Azure AD admin center](https://aad.portal.azure.com).

1. Click **Azure Active Directory** > **Custom security attributes (Preview)**.

1. Click the attribute set that includes the custom security attribute you want to deactivate.

1. In the list of custom security attributes, add a check mark next to the custom security attribute you want to deactivate.

1. Click **Deactivate attribute**.
  
1. In the Deactivate attribute dialog that appears, click **Yes**.

    The custom security attribute is deactivated and moved to the Deactivated attributes list.

## PowerShell

To manage custom security attributes in your Azure AD organization, you can also use the PowerShell. The following command can manage attribute sets and custom security attributes.
 
#### Get all attribute sets

Use the [Get-AzureADMSAttributeSet](/powershell/module/azuread/get-azureadmsattributeset) command without any parameters to get all attribute sets.

```powershell
Get-AzureADMSAttributeSet
```

#### Get an attribute set

Use the [Get-AzureADMSAttributeSet](/powershell/module/azuread/get-azureadmsattributeset) command to get an attribute set.

- Attribute set: `Engineering`

```powershell
Get-AzureADMSAttributeSet -Id "Engineering"
```
 
#### Add an attribute set

Use the [New-AzureADMSAttributeSet](/powershell/module/azuread/new-azureadmsattributeset) command to add a new attribute set.

- Attribute set: `Engineering`

```powershell
New-AzureADMSAttributeSet -Id "Engineering" -Description "Attributes for engineering team" -MaxAttributesPerSet 10 
```

#### Update an attribute set

Use the [Set-AzureADMSAttributeSet](/powershell/module/azuread/set-azureadmsattributeset) command to update an attribute set.

- Attribute set: `Engineering`

```powershell
Set-AzureADMSAttributeSet -Id "Engineering" -Description "Attributes for cloud engineering team"
Set-AzureADMSAttributeSet -Id "Engineering" -MaxAttributesPerSet 20
```

#### Get all custom security attributes

Use the [Get-AzureADMSCustomSecurityAttributeDefinition](/powershell/module/azuread/get-azureadmscustomsecurityattributedefinition) command without any parameters to get all custom security attribute definitions.

```powershell
Get-AzureADMSCustomSecurityAttributeDefinition
```

#### Get a custom security attribute

Use the [Get-AzureADMSCustomSecurityAttributeDefinition](/powershell/module/azuread/get-azureadmscustomsecurityattributedefinition) command to get a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `ProjectDate`

```powershell
Get-AzureADMSCustomSecurityAttributeDefinition -Id "Engineering_ProjectDate"
```
 
#### Add a custom security attribute

Use the [New-AzureADMSCustomSecurityAttributeDefinition](/powershell/module/azuread/new-azureadmscustomsecurityattributedefinition) command to add a new custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `ProjectDate`
- Attribute data type: String

```powershell
New-AzureADMSCustomSecurityAttributeDefinition -AttributeSet "Engineering" -Name "ProjectDate" -Description "Target completion date" -Type "String" -Status "Available" -IsCollection $false -IsSearchable $true -UsePreDefinedValuesOnly $true
```
 
#### Update a custom security attribute

Use the [Set-AzureADMSCustomSecurityAttributeDefinition](/powershell/module/azuread/set-azureadmscustomsecurityattributedefinition) command to update a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `ProjectDate`

```powershell
Set-AzureADMSCustomSecurityAttributeDefinition -Id "Engineering_ProjectDate" -Description "Target completion date (YYYY/MM/DD)"
```

#### Deactivate a custom security attribute

Use the [Set-AzureADMSCustomSecurityAttributeDefinition](/powershell/module/azuread/set-azureadmscustomsecurityattributedefinition) command to deactivate a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`

```powershell
Set-AzureADMSCustomSecurityAttributeDefinition -Id "Engineering_Project" -Status "Deprecated"
```

#### Get all predefined values

Use the [Get-AzureADMSCustomSecurityAttributeDefinitionAllowedValue](/powershell/module/azuread/get-azureadmscustomsecurityattributedefinitionallowedvalue) command to get all predefined values for a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`

```powershell
Get-AzureADMSCustomSecurityAttributeDefinitionAllowedValue -CustomSecurityAttributeDefinitionId "Engineering_Project"
```
 
#### Get a predefined value

Use the [Get-AzureADMSCustomSecurityAttributeDefinitionAllowedValue](/powershell/module/azuread/get-azureadmscustomsecurityattributedefinitionallowedvalue) command to get a predefined value for a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`
- Predefined value: `Alpine`

```powershell
Get-AzureADMSCustomSecurityAttributeDefinitionAllowedValue -CustomSecurityAttributeDefinitionId "Engineering_Project" -Id "Alpine" 
```
 
#### Add a predefined value

Use the [Add-AzureADMScustomSecurityAttributeDefinitionAllowedValues](/powershell/module/azuread/add-azureadmscustomsecurityattributedefinitionallowedvalues) command to add a predefined value for a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`
- Predefined value: `Alpine`

```powershell
Add-AzureADMScustomSecurityAttributeDefinitionAllowedValues -CustomSecurityAttributeDefinitionId "Engineering_Project" -Id "Alpine" -IsActive $true
```
 
#### Deactivate a predefined value

Use the [Set-AzureADMSCustomSecurityAttributeDefinitionAllowedValue](/powershell/module/azuread/set-azureadmscustomsecurityattributedefinitionallowedvalue) command to deactivate a predefined value for a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`
- Predefined value: `Alpine`

```powershell
Set-AzureADMSCustomSecurityAttributeDefinitionAllowedValue -CustomSecurityAttributeDefinitionId "Engineering_Project" -Id "Alpine" -IsActive $false
```

## Microsoft Graph API

To manage custom security attributes in your Azure AD organization, you can also use the Microsoft Graph API. The following API calls can be made to manage attribute sets and custom security attributes.

#### Get all attribute sets

Use the [List attributeSets](/graph/api/directory-list-attributesets) API to get all attribute sets.

```http
GET https://graph.microsoft.com/beta/directory/attributeSets
```

#### Get top attribute sets

Use the [List attributeSets](/graph/api/directory-list-attributesets) API to get the top attribute sets.

```http
GET https://graph.microsoft.com/beta/directory/attributeSets?$top=10
```

#### Get attribute sets in order

Use the [List attributeSets](/graph/api/directory-list-attributesets) API to get attribute sets in order.

```http
GET https://graph.microsoft.com/beta/directory/attributeSets?$orderBy=id
```

#### Get an attribute set

Use the [Get attributeSet](/graph/api/attributeset-get) API to get an attribute set.

- Attribute set: `Engineering`

```http
GET https://graph.microsoft.com/beta/directory/attributeSets/Engineering
```

#### Add an attribute set

Use the [Create attributeSet](/graph/api/directory-post-attributesets) API to add a new attribute set.

- Attribute set: `Engineering`

```http
POST https://graph.microsoft.com/beta/directory/attributeSets 
{
    "id":"Engineering",
    "description":"Attributes for engineering team",
    "maxAttributesPerSet":25
}
```

#### Update an attribute set

Use the [Update attributeSet](/graph/api/attributeset-update) API to update an attribute set.

- Attribute set: `Engineering`

```http
PATCH https://graph.microsoft.com/beta/directory/attributeSets/Engineering
{
    "description":"Attributes for engineering team",
    "maxAttributesPerSet":20
}
```

#### Get all custom security attributes

Use the [List customSecurityAttributeDefinitions](/graph/api/directory-list-customsecurityattributedefinitions) API to get all custom security attribute definitions.

```http
GET https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions
```

#### Filter custom security attributes

Use the [List customSecurityAttributeDefinitions](/graph/api/directory-list-customsecurityattributedefinitions) API to filter custom security attribute definitions.

- Filter: Attribute name eq 'Project' and status eq 'Available'

```http
GET https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions?$filter=name+eq+'Project'%20and%20status+eq+'Available'
```

- Filter: Attribute set eq 'Engineering' and status eq 'Available' and data type eq 'String'

```http
GET https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions?$filter=attributeSet+eq+'Engineering'%20and%20status+eq+'Available'%20and%20type+eq+'String'
```

#### Get a custom security attribute

Use the [Get customSecurityAttributeDefinition](/graph/api/customsecurityattributedefinition-get) API to get a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `ProjectDate`

```http
GET https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions/Engineering_ProjectDate
```

#### Add a custom security attribute

Use the [Create customSecurityAttributeDefinition](/graph/api/directory-post-customsecurityattributedefinitions) API to add a new custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `ProjectDate`
- Attribute data type: String

```http
POST https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions
{
    "attributeSet":"Engineering",
    "description":"Target completion date",
    "isCollection":false,
    "isSearchable":true,
    "name":"ProjectDate",
    "status":"Available",
    "type":"String",
    "usePreDefinedValuesOnly": false
}
```

#### Add a custom security attribute that supports multiple predefined values

Use the [Create customSecurityAttributeDefinition](/graph/api/directory-post-customsecurityattributedefinitions) API to add a new custom security attribute definition that supports multiple predefined values.

- Attribute set: `Engineering`
- Attribute: `Project`
- Attribute data type: Collection of Strings

```http
POST https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions
{
    "attributeSet":"Engineering",
    "description":"Active projects for user",
    "isCollection":true,
    "isSearchable":true,
    "name":"Project",
    "status":"Available",
    "type":"String",
    "usePreDefinedValuesOnly": true
}
```

#### Add a custom security attribute with a list of predefined values

Use the [Create customSecurityAttributeDefinition](/graph/api/directory-post-customsecurityattributedefinitions) API to add a new custom security attribute definition with a list of predefined values.

- Attribute set: `Engineering`
- Attribute: `Project`
- Attribute data type: Collection of Strings
- Predefined values: `Alpine`, `Baker`, `Cascade`

```http
POST https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions
{
    "attributeSet": "Engineering",
    "description": "Active projects for user",
    "isCollection": true,
    "isSearchable": true,
    "name": "Project",
    "status": "Available",
    "type": "String",
    "usePreDefinedValuesOnly": true,
    "allowedValues": [
        {
            "id": "Alpine",
            "isActive": true
        },
        {
            "id": "Baker",
            "isActive": true
        },
        {
            "id": "Cascade",
            "isActive": true
        }
    ]
}
```

#### Update a custom security attribute

Use the [Update customSecurityAttributeDefinition](/graph/api/customsecurityattributedefinition-update) API to update a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `ProjectDate`

```http
PATCH https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions/Engineering_ProjectDate
{
  "description": "Target completion date (YYYY/MM/DD)",
}
```

#### Update the predefined values for a custom security attribute

Use the [Update customSecurityAttributeDefinition](/graph/api/customsecurityattributedefinition-update) API to update the predefined values for a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`
- Attribute data type: Collection of Strings
- Update predefined value: `Baker`
- New predefined value: `Skagit`

> [!NOTE]
> For this request, you must add the **OData-Version** header and assign it the value `4.01`.

```http
PATCH https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions/Engineering_Project
{
    "allowedValues@delta": [
        {
            "id": "Baker",
            "isActive": false
        },
        {
            "id": "Skagit",
            "isActive": true
        }
    ]
}
```

#### Deactivate a custom security attribute

Use the [Update customSecurityAttributeDefinition](/graph/api/customsecurityattributedefinition-update) API to deactivate a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`

```http
PATCH https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions/Engineering_Project
{
  "status": "Deprecated"
}
```

#### Get all predefined values

Use the [List allowedValues](/graph/api/customsecurityattributedefinition-list-allowedvalues) API to get all predefined values for a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`

```http
GET https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions/Engineering_Project/allowedValues
```

#### Get a predefined value

Use the [Get allowedValue](/graph/api/allowedvalue-get) API to get a predefined value for a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`
- Predefined value: `Alpine`

```http
GET https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions/Engineering_Project/allowedValues/Alpine
```

#### Add a predefined value

Use the [Create allowedValue](/graph/api/customsecurityattributedefinition-post-allowedvalues) API to add a predefined value for a custom security attribute definition.

You can add predefined values for custom security attributes that have `usePreDefinedValuesOnly` set to `true`.

- Attribute set: `Engineering`
- Attribute: `Project`
- Predefined value: `Alpine`

```http
POST https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions/Engineering_Project/allowedValues
{
    "id":"Alpine",
    "isActive":"true"
}
```

#### Deactivate a predefined value

Use the [Update allowedValue](/graph/api/allowedvalue-update) API to deactivate a predefined value for a custom security attribute definition.

- Attribute set: `Engineering`
- Attribute: `Project`
- Predefined value: `Alpine`

```http
PATCH https://graph.microsoft.com/beta/directory/customSecurityAttributeDefinitions/Engineering_Project/allowedValues/Alpine
{
    "isActive":"false"
}
```

## Frequently asked questions

**Can you delete custom security attribute definitions?**

No, you can't delete custom security attribute definitions. You can only [deactivate custom security attribute definitions](#deactivate-a-custom-security-attribute). Once you deactivate a custom security attribute, it can no longer be applied to the Azure AD objects. Custom security attribute assignments for the deactivated custom security attribute definition are not automatically removed. There is no limit to the number of deactivated custom security attributes. You can have 500 active custom security attribute definitions per tenant with 100 allowed predefined values per custom security attribute definition.

## Next steps

- [Manage access to custom security attributes in Azure AD](custom-security-attributes-manage.md)
- [Assign or remove custom security attributes for a user](../enterprise-users/users-custom-security-attributes.md)
- [Assign or remove custom security attributes for an application](../manage-apps/custom-security-attributes-apps.md)
