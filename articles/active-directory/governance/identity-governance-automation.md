---
title: Automate Azure AD Identity Governance tasks with Azure Automation
description: Learn how to write PowerShell scripts in Azure Automation to interact with Azure Active Directory entitlement management and other features.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 1/20/2022
ms.author: ajburnle
ms.reviewer: 
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell


---
# Automate Azure AD Identity Governance tasks via Azure Automation and Microsoft Graph

[Azure Automation](/azure/automation/overview) is an Azure cloud service that allows you to automate common or repetitive systems management and processes.  Microsoft Graph is the Microsoft unified API endpoint for Azure AD features that manage users, groups, access packages, access reviews, and other resources in the directory.  You can manage Azure AD at scale from the PowerShell command line, using the [Microsoft Graph PowerShell SDK](/graph/powershell/get-started).  You can also include the Microsoft Graph PowerShell cmdlets from a [PowerShell-based runbook in Azure Automation](/azure/automation/automation-intro), so that you can automate Azure AD tasks from a simple script.

Azure Automation and the PowerShell Graph SDK supports certificate-based authentication and application permissions, so you can have Azure Automation runbooks authenticate to Azure AD without needing a user context.

This article will show you how to get started using Azure Automation for Azure AD Identity Governance, by creating a simple runbook that queries entitlement management via Microsoft Graph PowerShell.

## Create an Azure Automation account

Azure Automation provides a cloud-hosted environment for [runbook execution](/azure/automation/automation-runbook-execution).  Those runbooks can start automatically based on a schedule, or be triggered by webhooks or by Logic Apps.

Using Azure Automation requires you to have an Azure subscription.

**Prerequisite role**: Azure subscription or resource group owner

1. Sign in to the Azure portal. Make sure you have access to the subscription or resource group where the Azure Automation account will be located.

1. Select the subscription or resource group, and select **Create**.  Type **Automation**, select the **Automation** Azure service from Microsoft, then select **Create**.

1. After the Azure Automation account has been created, select **Access control (IAM)**. Then select **View** in **View access to this resource**. These users and service principals will subsequently be able to interact with the Microsoft service through the scripts to be created in that Azure Automation account.
1. Review the users and service principals who are listed there and ensure they are authorized.  Remove any users who are unauthorized.

## Create a self-signed key pair and certificate on your computer

So that it can operate without needing your personal credentials, the Azure Automation account you created will need to authenticate itself to Azure AD with a certificate.

If you already have a key pair for authenticating your service to Azure AD, and a certificate that you received from a certificate authority, skip to the next section.

To generate a self-signed certificate,

1. Follow the instructions in [how to create a self-signed certificate](../develop/howto-create-self-signed-certificate.md), option 2, to create and export a certificate with its private key.

1. Display the thumbprint of the certificate.

   ```powershell
    $cert | ft Thumbprint

1. After you have exported the files, you can remove the certificate and key pair from your local user certificate store.  In subsequent steps you will remove the `.pfx` and `.crt` files as well, once the certificate and private key have been uploaded to the Azure Automation and Azure AD services.

## Upload the key pair to Azure Automation

Your runbook in Azure Automation will retrieve the private key from the `.pfx` file, and use it for authenticating to Microsoft Graph.

1. In the Azure portal for the Azure Automation account, select **Certificates** and **Add a certificate**.

1. Upload the `.pfx` file created earlier, and type the password you provided when you created the file.

1. After the private key is uploaded, record the certificate expiration date.

1. You can now delete the `.pfx` file from your local computer.  However, do not delete the `.crt` file yet, as you will need this file in a subsequent step.

## Add modules for Microsoft Graph to your Azure Automation account

By default, Azure Automation does not have any PowerShell modules preloaded for Microsoft Graph.  You will need to add **Microsoft.Graph.Authentication**, and then additional modules, from the gallery to your Automation account.  Note that you will need to choose whether to use the beta or v1.0 APIs through those modules, as you cannot mix both in a single runbook.

1. In the Azure portal for the Azure Automation account, select **Modules** and then **Browse gallery**.

1. In the Search bar, type **Microsoft.Graph.Authentication**. Select the module, select **Import**, and select **OK** to have Azure AD begin importing the module.  After clicking OK, importing a module may take several minutes.  Don't attempt to add more Microsoft Graph modules until the Microsoft.Graph.Authentication module import has completed, since those other modules have Microsoft.Graph.Authentication as a prerequisite.

1. Return to the **Modules** list and select **Refresh**.  Once the Status of the **Microsoft.Graph.Authentication** module has changed to **Available**, you can import the next module.

1. If you are using the cmdlets for Azure AD identity governance features, such as entitlement management, then repeat the import process for the module **Microsoft.Graph.Identity.Governance**.

1. Import other modules that your script may require.  For example, if you are using Identity Protection, then you may wish to import the **Microsoft.Graph.Identity.SignIns** module.

## Create an app registration and assign permissions

Next, you will create an app registration in Azure AD, so that Azure AD will recognize your Azure Automation runbook's certificate for authentication.

**Prerequisite role**: Global Administrator or other administrator who can consent applications to application permissions

1. In the Azure portal, browse to **Azure Active Directory** > **App registrations**.

1. Select **New registration**.

1. Type a name for the application and select **Register**.

1. Once the application registration is created, take note of the **Application (client) ID** and **Directory (tenant) ID** as you will need these items later.

1. Select **Certificates and Secrets** and **Upload certificate**.

1. Upload the `.crt` file created earlier.

1. Select **API permissions** and **Add a permission**.

1. Select **Microsoft Graph** and **Application permissions**.

1. Select each of the permissions that your Azure Automation account will require, then select **Add permissions**.

 * If your runbook is only performing queries for entitlement management, then it can use the **EntitlementManagement.Read.All** permission.
 * If your runbook is making changes to entitlement management, for example to create assignments, then use the **EntitlementManagement.ReadWrite.All** permission.
 * For other APIs, ensure that the necessary permission is added.  For example, for identity protection, the **IdentityRiskyUser.Read.All** permission should be added.

10. Select **Grant admin permissions** to give your app those permissions.

## Create Azure Automation variables

In this step, you will create in the Azure automation account three variables that the runbook will use to determine how to authenticate to Azure AD.

1. In the Azure portal, return to the Azure Automation account.

1. Select **Variables**, and **Add variable**.

1. Create a variable named **Thumbprint**.  Type, as the value of the variable, the certificate thumbprint that was generated earlier.

1. Create a variable named **ClientId**.  Type, as the value of the variable, the client ID for the application registered in Azure AD.

1. Create a variable named **TenantId**.  Type, as the value of the variable, the tenant ID of the directory where the application was registered.

## Create an Azure Automation PowerShell runbook that can use Graph

In this step, you will create an initial runbook.  You can trigger this runbook to verify the authentication using the certificate created earlier is successful.

1. Select **Runbooks** and **Create a runbook**.

1. Type the name of the runbook, select **PowerShell** as the type of runbook to create, and select **Create**.

1. Once the runbook is created, a text editing pane will appear for you to type in the PowerShell source code of the runbook.

1. Type the following PowerShell into the text editor.

```powershell
Import-Module Microsoft.Graph.Authentication
$ClientId = Get-AutomationVariable -Name 'ClientId'
$TenantId = Get-AutomationVariable -Name 'TenantId'
$Thumbprint = Get-AutomationVariable -Name 'Thumbprint'
Connect-MgGraph -clientId $ClientId -tenantid $TenantId -certificatethumbprint $Thumbprint
```

5. Select **Test pane**, and select **Start**.  Wait a few seconds for the Azure Automation processing of your runbook script to complete.

1. If the run of your runbook is successful, then the message **Welcome to Microsoft Graph!** will appear.

Now that you have verified that your runbook can authenticate to Microsoft Graph, extend your runbook by adding cmdlets for interacting with Azure AD features.

## Extend the runbook to use Entitlement Management

If the app registration for your runbook has the **EntitlementManagement.Read.All** or **EntitlementManagement.ReadWrite.All** permissions, then it can use the entitlement management APIs.

1. For example, to get a list of Azure AD entitlement management access packages, you can update the above-created runbook, and replace the text with the following PowerShell.

```powershell
Import-Module Microsoft.Graph.Authentication
$ClientId = Get-AutomationVariable -Name 'ClientId'
$TenantId = Get-AutomationVariable -Name 'TenantId'
$Thumbprint = Get-AutomationVariable -Name 'Thumbprint'
$auth = Connect-MgGraph -clientId $ClientId -tenantid $TenantId -certificatethumbprint $Thumbprint
Select-MgProfile -Name beta
Import-Module Microsoft.Graph.Identity.Governance
$ap = Get-MgEntitlementManagementAccessPackage -All -ErrorAction Stop
$ap | Select-Object -Property Id,DisplayName | ConvertTo-Json
```

2. Select **Test pane**, and select **Start**.  Wait a few seconds for the Azure Automation processing of your runbook script to complete.

3. If the run was successful, the output instead of the welcome message will be a JSON array. The JSON array will include the ID and display name of each access package returned from the query.

## Parse the output of an Azure Automation account in Logic Apps (optional)

Once your runbook is published, your can create a schedule in Azure Automation, and link your runbook to that schedule to run automatically.  Scheduling runbooks from Azure Automation is suitable for runbooks that do not need to interact with other Azure or Office 365 services.

If you wish to send the output of your runbook to another service, then you may wish to consider using [Azure Logic Apps](/azure/logic-apps/logic-apps-overview) to start your Azure Automation runbook, as Logic Apps can also parse the results.

1. In Azure Logic Apps, create a Logic App in the Logic Apps Designer starting with **Recurrence**.

1. Add the operation **Create job** from **Azure Automation**.  Authenticate to Azure AD, and select the Subscription, Resource Group, Automation Account created earlier.  Select **Wait for Job**.

1. Add the parameter **Runbook name** and type the name of the runbook to be started.

1. Select **New step** and add the operation **Get job output**.  Select the same Subscription, Resource Group, Automation Account as the previous step, and select the Dynamic value of the **Job ID** from the previous step.

1. You can then add more operations to the Logic App, such as the [**Parse JSON** action](/azure/logic-apps/logic-apps-perform-data-operations#parse-json-action), that use the **Content** returned when the runbook completes.

Note that in Azure Automation, a PowerShell runbook can fail to complete if it tries to write a large amount of data to the output stream at once. You can typically work around this issue by having the runbook output just the information needed by the Logic App, such as by using the `Select-Object -Property` cmdlet to exclude unneeded properties.

## Plan to keep the certificate up to date

If you created a self-signed certificate following the steps above for authentication, keep in mind that the certificate will have a limited lifetime before it will expire. You will need to regenerate the certificate and upload the new certificate before its expiration date.

There are two places where you can see the expiration date in the Azure portal.

* In Azure Automation, the **Certificates** screen displays the expiration date of the certificate.
* In Azure AD, on the app registration, the **Certificates & secrets** screen displays the expiration date of the certificate used for the Azure Automation account.

## Next steps

- [Create an Automation account using the Azure portal](/azure/automation/quickstarts/create-account-portal)
- [Manage access to resources in Active Directory entitlement management using Microsoft Graph PowerShell](/powershell/microsoftgraph/tutorial-entitlement-management?view=graph-powershell-beta)
