---
title: Run Azure Automation runbooks on a Hybrid Runbook Worker
description: This article describes how to run runbooks on machines in your local datacenter or other cloud provider with the Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 11/17/2021
ms.topic: conceptual 
ms.custom: devx-track-azurepowershell
---

# Run Automation runbooks on a Hybrid Runbook Worker

Runbooks that run on a [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) typically manage resources on the local computer or against resources in the local environment where the worker is deployed. Runbooks in Azure Automation typically manage resources in the Azure cloud. Even though they are used differently, runbooks that run in Azure Automation and runbooks that run on a Hybrid Runbook Worker are identical in structure.

When you author a runbook to run on a Hybrid Runbook Worker, you should edit and test the runbook on the machine that hosts the worker. The host machine has all the PowerShell modules and network access required to manage the local resources. Once you test the runbook on the Hybrid Runbook Worker machine, you can then upload it to the Azure Automation environment, where it can be run on the worker.

## Plan for Azure services protected by firewall

Enabling the Azure Firewall on [Azure Storage](../storage/common/storage-network-security.md), [Azure Key Vault](../key-vault/general/network-security.md), or [Azure SQL](../azure-sql/database/firewall-configure.md) blocks access from Azure Automation runbooks for those services. Access will be blocked even when the firewall exception to allow trusted Microsoft services is enabled, as Automation is not a part of the trusted services list. With an enabled firewall, access can only be made by using a Hybrid Runbook Worker and a [virtual network service endpoint](../virtual-network/virtual-network-service-endpoints-overview.md).

## Plan runbook job behavior

Azure Automation handles jobs on Hybrid Runbook Workers differently from jobs run in Azure sandboxes. If you have a long-running runbook, make sure that it's resilient to possible restart. For details of the job behavior, see [Hybrid Runbook Worker jobs](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

## Service accounts

### Windows 

Jobs for Hybrid Runbook Workers run under the local **System** account.
>[!NOTE]
>  To run PowerShell 7.x on a Windows Hybrid Runbook Worker, see [Installing PowerShell on Windows](/powershell/scripting/install/installing-powershell-on-windows).
>  We support [Hybrid worker extension based](./extension-based-hybrid-runbook-worker-install.md) and [agent based](./automation-windows-hrw-install.md) onboarding. 
>  For agent based onboarding, ensure the Windows Hybrid Runbook worker version is 7.3.1296.0 or above.

Make sure the path where the *pwsh.exe* executable is located and is added to the PATH environment variable. Restart the Hybrid Runbook Worker after installation completes.

### Linux

>[!NOTE]
> To run PowerShell 7.x on a Linux Hybrid Runbook Worker, see [Installing PowerShell on Linux](/powershell/scripting/install/installing-powershell-on-linux).
>  We support [Hybrid worker extension based](./extension-based-hybrid-runbook-worker-install.md) and [agent based](./automation-linux-hrw-install.md) onboarding.
>  For agent based onboarding, ensure the Linux Hybrid Runbook worker version is 1.7.5.0 or above.


Service accounts **nxautomation** and **omsagent** are created. The creation and permission assignment script can be viewed at [https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/installer/datafiles/linux.data](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/installer/datafiles/linux.data). The accounts, with the corresponding sudo permissions, must be present during [installation of a Linux Hybrid Runbook worker](automation-linux-hrw-install.md). If you try to install the worker, and the account is not present or doesn't have the appropriate permissions, the installation fails. Do not change the permissions of the `sudoers.d` folder or its ownership. Sudo permission is required for the accounts and the permissions shouldn't be removed. Restricting this to certain folders or commands may result in a breaking change. The **nxautomation** user enabled as part of Update Management executes only signed runbooks.

To ensure the service accounts have access to the stored runbook modules:

- When you use `pip install`, `apt install` or other method for installing packages on Linux, ensure the package is installed for all users. For example `sudo -H pip install <package_name>`.
- If using [PowerShell on Linux](/powershell/scripting/whats-new/what-s-new-in-powershell-70), when you use the [Install-Module](/powershell/module/powershellget/install-module) cmdlet, be sure to specify `AllUsers` for the `Scope` parameter.

The Automation worker log is located at `/var/opt/microsoft/omsagent/run/automationworker/worker.log`.

The service accounts are removed when the machine is removed as a Hybrid Runbook Worker.

## Configure runbook permissions

Define permissions for your runbook to run on the Hybrid Runbook Worker in the following ways:

* Have the runbook provide its own authentication to local resources.
* Configure authentication using [managed identities for Azure resources](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Specify a Run As account to provide a user context for all runbooks.

### Use runbook authentication to local resources

If preparing a runbook that provides its own authentication to resources, use [credential](./shared-resources/credentials.md) and [certificate](./shared-resources/certificates.md) assets in your runbook. There are several cmdlets that allow you to specify credentials so that the runbook can authenticate to different resources. The following example shows a portion of a runbook that restarts a computer. It retrieves credentials from a credential asset and the name of the computer from a variable asset and then uses these values with the `Restart-Computer` cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

You can also use an [InlineScript](automation-powershell-workflow.md#use-inlinescript) activity. `InlineScript` allows you to run blocks of code on another computer with credentials.

### <a name="runbook-auth-managed-identities"></a>Use runbook authentication with managed identities

Hybrid Runbook Workers on Azure virtual machines can use managed identities to authenticate to Azure resources. Using managed identities for Azure resources instead of Run As accounts provides benefits because you don't need to:

* Export the Run As certificate and then import it into the Hybrid Runbook Worker.
* Renew the certificate used by the Run As account.
* Handle the Run As connection object in your runbook code.

Follow the next steps to use a managed identity for Azure resources on a Hybrid Runbook Worker:

1. Create an Azure VM.
1. Configure managed identities for Azure resources on the VM. See [Configure managed identities for Azure resources on a VM using the Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
1. Give the VM access to a resource group in Resource Manager. Refer to [Use a Windows VM system-assigned managed identity to access Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
1. Install the Hybrid Runbook Worker on the VM. See [Deploy a Windows Hybrid Runbook Worker](automation-windows-hrw-install.md) or [Deploy a Linux Hybrid Runbook Worker](automation-linux-hrw-install.md).
1. Update the runbook to use the [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet with the `Identity` parameter to authenticate to Azure resources. This configuration reduces the need to use a Run As account and perform the associated account management.

    ```powershell
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

    # Get all VM names from the subscription
    Get-AzVM -DefaultProfile $AzureContext | Select Name
    ```

    If you want the runbook to execute with the system-assigned managed identity, leave the code as-is. If you prefer to use a user-assigned managed identity, then:
    1. From line 5, remove `$AzureContext = (Connect-AzAccount -Identity).context`,
    1. Replace it with `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`, and
    1. Enter the Client ID.

### Use runbook authentication with Run As account

Instead of having your runbook provide its own authentication to local resources, you can specify a Run As account for a Hybrid Runbook Worker group. To specify a Run As account, you must define a [credential asset](./shared-resources/credentials.md) that has access to local resources. These resources include certificate stores and all runbooks run under these credentials on a Hybrid Runbook Worker in the group.

- The user name for the credential must be in one of the following formats:

   * domain\username
   * username@domain
   * username (for accounts local to the on-premises computer)

- To use the PowerShell runbook **Export-RunAsCertificateToHybridWorker**, you need to install the Az modules for Azure Automation on the local machine.

#### Use a credential asset to specify a Run As account

Use the following procedure to specify a Run As account for a Hybrid Runbook Worker group:

1. Create a [credential asset](./shared-resources/credentials.md) with access to local resources.
1. Open the Automation account in the Azure portal.
1. Select **Hybrid Worker Groups**, and then select the specific group.
1. Select **All settings**, followed by **Hybrid worker group settings**.
1. Change the value of **Run As** from **Default** to **Custom**.
1. Select the credential and click **Save**.

## <a name="runas-script"></a>Install Run As account certificate

As part of your automated build process for deploying resources in Azure, you might require access to on-premises systems to support a task or set of steps in your deployment sequence. To provide authentication against Azure using the Run As account, you must install the Run As account certificate.

>[!NOTE]
>This PowerShell runbook currently does not run on Linux machines. It runs only on  Windows machines.
>

The following PowerShell runbook, called **Export-RunAsCertificateToHybridWorker**, exports the Run As certificate from your Azure Automation account. The runbook downloads and imports the certificate into the local machine certificate store on a Hybrid Runbook Worker that is connected to the same account. Once it completes that step, the runbook verifies that the worker can successfully authenticate to Azure using the Run As account.

>[!NOTE]
>This PowerShell runbook is not designed or intended to be run outside of your Automation account as a script on the target machine.
>

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword | Write-Verbose

Remove-Item -Path $CertPath -ErrorAction SilentlyContinue | Out-Null

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>For PowerShell runbooks, `Add-AzAccount` and `Add-AzureRMAccount` are aliases for `Connect-AzAccount`. When searching your library items, if you do not see `Connect-AzAccount`, you can use `Add-AzAccount`, or you can update your modules in your Automation account.

To finish preparing the Run As account:

1. Save the **Export-RunAsCertificateToHybridWorker** runbook to your computer with a **.ps1** extension.
1. Import it into your Automation account.
1. Edit the runbook, changing the value of the `Password` variable to your own password.
1. Publish the runbook.
1. Run the runbook, targeting the Hybrid Runbook Worker group that runs and authenticates runbooks using the Run As account. 
1. Examine the job stream to see that it reports the attempt to import the certificate into the local machine store, followed by multiple lines. This behavior depends on how many Automation accounts you define in your subscription and the degree of success of the authentication.

>[!NOTE]
>  In case of unrestricted access, a user with VM Contributor rights or having permissions to run commands against the hybrid worker machine can use the Automation Account Run As certificate from the hybrid worker machine, using other sources like Azure cmdlets which could potentially allow a malicious user access as a subscription contributor. This could jeopardize the security of your Azure environment. </br> </br>
>  We recommend that you divide the tasks within the team and grant the required permissions/access to users as per their job. Do not provide unrestricted permissions to the machine hosting the hybrid runbook worker role.


## Work with signed runbooks on a Windows Hybrid Runbook Worker

You can configure a Windows Hybrid Runbook Worker to run only signed runbooks.

> [!IMPORTANT]
> Once you've configured a Hybrid Runbook Worker to run only signed runbooks, unsigned runbooks fail to execute on the worker.

> [!NOTE]
>  PowerShell 7.x does not support signed runbooks for Windows and Linux Hybrid Runbook Worker.  

### Create signing certificate

The following example creates a self-signed certificate that can be used for signing runbooks. This code creates the certificate and exports it so that the Hybrid Runbook Worker can import it later. The thumbprint is also returned for later use in referencing the certificate.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
    -Subject "CN=contoso.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
    -KeyExportPolicy Exportable `
    -KeyUsage DigitalSignature `
    -Type CodeSigningCert

# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### Import certificate and configure workers for signature validation

Copy the certificate that you've created to each Hybrid Runbook Worker in a group. Run the following script to import the certificate and configure the workers to use signature validation on runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### Sign your runbooks using the certificate

With the Hybrid Runbook Workers configured to use only signed runbooks, you must sign runbooks that are to be used on the Hybrid Runbook Worker. Use the following sample PowerShell code to sign these runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

When a runbook has been signed, you must import it into your Automation account and publish it with the signature block. To learn how to import runbooks, see [Import a runbook](manage-runbooks.md#import-a-runbook).

>[!NOTE]
>Use only plaintext characters in your runbook code, including comments. Using characters with diacritical marks, like á or ñ, will result in an error. When Azure Automation downloads your code, the characters will be replaced by a question mark and the signing will fail with a "signature hash validation failure" message.

## Work with signed runbooks on a Linux Hybrid Runbook Worker

To be able to work with signed runbooks, a Linux Hybrid Runbook Worker must have the [GPG](https://gnupg.org/index.html) executable on the local machine.

> [!IMPORTANT]
> Once you've configured a Hybrid Runbook Worker to run only signed runbooks, unsigned runbooks fail to execute on the worker.

You will perform the following steps to complete this configuration:

* Create a GPG keyring and keypair
* Make the keyring available to the Hybrid Runbook Worker
* Verify that signature validation is on
* Sign a runbook

> [!NOTE]
>  PowerShell 7.x does not support signed runbooks for Windows and Linux Hybrid Runbook Worker.

### Create a GPG keyring and keypair

To create the GPG keyring and keypair, use the Hybrid Runbook Worker [nxautomation account](automation-runbook-execution.md#log-analytics-agent-for-linux).

1. Use the sudo application to sign in as the **nxautomation** account.

    ```bash
    sudo su - nxautomation
    ```

1. Once you are using **nxautomation**, generate the GPG keypair. GPG guides you through the steps. You must provide name, email address, expiration time, and passphrase. Then you wait until there is enough entropy on the machine for the key to be generated.

    ```bash
    sudo gpg --generate-key
    ```

1. Because the GPG directory was generated with sudo, you must change its owner to **nxautomation** using the following command.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### Make the keyring available to the Hybrid Runbook Worker

Once the keyring has been created, make it available to the Hybrid Runbook Worker. Modify the settings file **home/nxautomation/state/worker.conf** to include the following example code under the file section `[worker-optional]`.

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### Verify that signature validation is on

If signature validation has been disabled on the machine, you must turn it on by running the following sudo command. Replace `<LogAnalyticsworkspaceId>` with your workspace ID.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### Sign a runbook

Once you have configured signature validation, use the following GPG command to sign the runbook.

```bash
gpg --clear-sign <runbook name>
```

The signed runbook is called **\<runbook name>.asc**.

You can now upload the signed runbook to Azure Automation and execute it like a regular runbook.

## Start a runbook on a Hybrid Runbook Worker

[Start a runbook in Azure Automation](start-runbooks.md) describes different methods for starting a runbook. Starting a runbook on a Hybrid Runbook Worker uses a **Run on** option that allows you to specify the name of a Hybrid Runbook Worker group. When a group is specified, one of the workers in that group retrieves and runs the runbook. If your runbook does not specify this option, Azure Automation runs the runbook as usual.

When you start a runbook in the Azure portal, you're presented with the **Run on** option for which you can select **Azure** or **Hybrid Worker**. If you select **Hybrid Worker**, you can choose the Hybrid Runbook Worker group from a dropdown.

When starting a runbook using PowerShell, use the `RunOn` parameter with the [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet. The following example uses Windows PowerShell to start a runbook named **Test-Runbook** on a Hybrid Runbook Worker group named MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -RunOn "MyHybridGroup"
```
## Logging

To help troubleshoot issues with your runbooks running on a hybrid runbook worker, logs are stored locally in the following location:

* On Windows at `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` for detailed job runtime process logging. High-level runbook job status events are written to the **Application and Services Logs\Microsoft-Automation\Operations** event log.

* On Linux, the user hybrid worker logs can be found at `/home/nxautomation/run/worker.log`, and system runbook worker logs can be found at `/var/opt/microsoft/omsagent/run/automationworker/worker.log`.

## Next steps

* If your runbooks aren't completing successfully, review the troubleshooting guide for [runbook execution failures](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* For more information on PowerShell, including language reference and learning modules, see [PowerShell Docs](/powershell/scripting/overview).
* Learn about [using Azure Policy to manage runbook execution](enforce-job-execution-hybrid-worker.md) with Hybrid Runbook Workers.
* For a PowerShell cmdlet reference, see [Az.Automation](/powershell/module/az.automation).