---
title: 'AADCloudSyncTools PowerShell module for Azure AD Connect cloud sync'
description: This article describes how to install the Azure AD Connect cloud provisioning agent.
services: active-directory
author: billmath
manager: karenhoran
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/03/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# AADCloudSyncTools PowerShell module for Azure AD Connect cloud sync

The AADCloudSyncTools module provides a set of useful tools that can help you manage your deployments of Azure Active Directory Connect (Azure AD Connect) cloud sync.

## Prerequisites

You can automatically install all the prerequisites for the AADCloudSyncTools module by using `Install-AADCloudSyncToolsPrerequisites`. You'll do that in the next section of this article.

Here are some details about what you need:

- The AADCloudSyncTools module uses Microsoft Authentication Library (MSAL) authentication, so it requires installation of the MSAL.PS module. To verify the installation, in a PowerShell window, run `Get-module MSAL.PS -ListAvailable`. If the module is installed correctly, you'll get a response. If necessary, you can use `Install-AADCloudSyncToolsPrerequisites` to install the latest version of MSAL.PS.
- Although the Azure AD PowerShell module is not required for any functionality of the AADCloudSyncTools module, it is useful. So it's automatically installed when you use `Install-AADCloudSyncToolsPrerequisites`. 
- Installing modules from the PowerShell Gallery requires Transport Layer Security (TLS) 1.2 enforcement. The cmdlet `Install-AADCloudSyncToolsPrerequisites` sets TLS 1.2 enforcement before installing all the prerequisites. To ensure that you can manually install modules, set the following in the PowerShell session before using the cmdlet:

  ```
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## Install the AADCloudSyncTools PowerShell module

1. Open Windows PowerShell with administrative privileges.
2. Run `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`.
3. To verify that the module was imported, run `Get-module AADCloudSyncTools`.

   You should now see information about the module.
4. To install the AADCloudSyncTools module prerequisites, run `Install-AADCloudSyncToolsPrerequisites`.
5. On the first run, the PowerShellGet module will be installed if it's not present. To load the new PowerShellGet module, close the PowerShell window and open a new PowerShell session with administrative privileges. 
6. Import the module again by running `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`.
7. Run `Install-AADCloudSyncToolsPrerequisites` again to install the MSAL and Azure AD modules.

   All prerequisites should now be installed.

   ![Screenshot of the notification in the PowerShell window that says the prerequisites were installed successfully.](media/reference-powershell/install-1.png)
8. Every time you want to use the AADCloudSyncTools module in a new PowerShell session, run the following command:

   ```
   Import-module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
   ```


## AADCloudSyncTools cmdlets

### Connect-AADCloudSyncTools

This cmdlet uses the MSAL.PS module to request a token for the Azure AD administrator to access Microsoft Graph.

### Export-AADCloudSyncToolsLogs

This cmdlet exports and packages all the troubleshooting data in a compressed file, as follows: 

1. Sets verbose tracing and starts collecting data from the provisioning agent (same as `Start-AADCloudSyncToolsVerboseLogs`). You can find these trace logs in the folder *C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace*.
2. Stops data collection after three minutes and disables verbose tracing (same as `Stop-AADCloudSyncToolsVerboseLogs`). You can specify a different duration by using `-TracingDurationMins` or completely skip verbose tracing by using `-SkipVerboseTrace`.
3. Collects Event Viewer logs for the last 24 hours. 
4. Compresses all the agent logs, verbose logs, and Event Viewer logs into a .zip file in the user's *Documents* folder. You can specify a different output folder by using `-OutputPath <folder path>`.

### Get-AADCloudSyncToolsInfo

This cmdlet shows Azure AD tenant details and the state of internal variables.

### Get-AADCloudSyncToolsJob

This cmdlet uses Microsoft Graph to get Azure AD service principals and returns the sync job's information. You can also call it by using the specific sync job ID as a parameter.

### Get-AADCloudSyncToolsJobSchedule

This cmdlet uses Microsoft Graph to get Azure AD service principals and returns the sync job's schedule. You can also call it by using the specific sync job ID as a parameter.

### Get-AADCloudSyncToolsJobSchema

This cmdlet uses Microsoft Graph to get Azure AD service principals and returns the sync job's schema.

### Get-AADCloudSyncToolsJobScope

This cmdlet uses Microsoft Graph to get the sync job's schema for the provided sync job ID and outputs all filter groups' scopes.

### Get-AADCloudSyncToolsJobSettings

This cmdlet uses Microsoft Graph to get Azure AD service principals and returns the sync job's settings. You can also call it by using the specific sync job ID as a parameter.

### Get-AADCloudSyncToolsJobStatus

This cmdlet uses Microsoft Graph to get Azure AD service principals and returns the sync job's status. You can also call it by using the specific sync job ID as a parameter.

### Get-AADCloudSyncToolsServicePrincipal

This cmdlet uses Microsoft Graph to get the service principals for Azure AD and/or Azure Service Fabric. Without parameters, it will return only Azure AD service principals.

### Install-AADCloudSyncToolsPrerequisites

This cmdlet checks for the presence of PowerShellGet v2.2.4.1 or later, the Azure AD module, and the MSAL.PS module. It installs these items if they're missing.

### Invoke-AADCloudSyncToolsGraphQuery

This cmdlet invokes a web request for the URI, method, and body specified as parameters.

### Repair-AADCloudSyncToolsAccount

This cmdlet uses Azure AD PowerShell to delete the current account (if present). It then resets the sync account authentication with a new sync account in Azure AD.

### Restart-AADCloudSyncToolsJob

This cmdlet restarts a full synchronization.

### Resume-AADCloudSyncToolsJob

This cmdlet continues synchronization from the previous watermark.

### Start-AADCloudSyncToolsVerboseLogs

This cmdlet modifies *AADConnectProvisioningAgent.exe.config* to enable verbose tracing and restarts the AADConnectProvisioningAgent service. You can use `-SkipServiceRestart` to prevent service restart, but any configuration changes will not take effect. You can find these trace logs in the folder *C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace*.

### Stop-AADCloudSyncToolsVerboseLogs

This cmdlet modifies *AADConnectProvisioningAgent.exe.config* to disable verbose tracing and restarts the AADConnectProvisioningAgent service. You can use `-SkipServiceRestart` to prevent service restart, but any configuration changes will not take effect.

### Suspend-AADCloudSyncToolsJob

This cmdlet pauses synchronization.

## Next steps 

- [What is provisioning?](what-is-provisioning.md)
- [What is Azure AD Connect cloud sync?](what-is-cloud-sync.md)
