---
title: Deploy Azure Site Recovery replication appliance - Preview
description: This article describes support and requirements when deploying the replication appliance for VMware disaster recovery to Azure with Azure Site Recovery - Preview
ms.service: site-recovery
ms.topic: article
ms.date: 09/01/2021
---

# Deploy Azure Site Recovery replication appliance - Preview

>[!NOTE]
> The information in this article applies to Azure Site Recovery - Preview. For information about configuration server requirements in Classic releases, [see this article](vmware-azure-configuration-server-requirements.md).

>[!NOTE]
> Ensure you create a new and exclusive Recovery Services vault for setting up the preview appliance. Don't use an existing vault.

>[!NOTE]
> Enabling replication for physical machines is not supported with this preview. 

You deploy an on-premises replication appliance when you use [Azure Site Recovery](site-recovery-overview.md) for disaster recovery of VMware VMs to Azure.

- The replication appliance coordinates communications between on-premises VMware and Azure. It also manages data replication.
- [Learn more](vmware-azure-architecture-preview.md) about the Azure Site Recovery replication appliance components and processes.

## Hardware requirements

**Component** | **Requirement**
--- | ---
CPU cores | 8
RAM | 32 GB
Number of disks | 3, including the OS disk - 80 GB, data disk 1 - 620 GB, data disk 2 - 620 GB

## Software requirements

**Component** | **Requirement**
--- | ---
Operating system | Windows Server 2016
Operating system locale | English (en-*)
Windows Server roles | Don't enable these roles: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Group policies | Don't enable these group policies: <br> - Prevent access to the command prompt. <br> - Prevent access to registry editing tools. <br> - Trust logic for file attachments. <br> - Turn on Script Execution. <br> [Learn more](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - No pre-existing default website <br> - No pre-existing website/application listening on port 443 <br>- Enable  [anonymous authentication](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Enable [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) setting
FIPS (Federal Information Processing Standards) | Do not enable FIPS mode|

## Network requirements

|**Component** | **Requirement**|
|--- | ---|
|Fully qualified domain name (FQDN) | Static|
|Ports | 443 (Control channel orchestration)<br>9443 (Data transport)|
|NIC type | VMXNET3 (if the appliance is a VMware VM)|


### Allow URLs

Ensure the following URLs are allowed and reachable from the Azure Site Recovery replication appliance for continuous connectivity:

  | **URL**                  | **Details**                             |
  | ------------------------- | -------------------------------------------|
  | portal.azure.com          | Navigate to the Azure portal.              |
  | `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | To sign-in to your Azure subscription.  |
  |`*.microsoftonline.com `|Create Azure Active  Directory (AD) apps for the appliance to communicate with Azure Site Recovery. |
  |management.azure.com |Create Azure AD apps for the appliance to communicate with the Azure Site Recovery service. |
  |`*.services.visualstudio.com `|Upload app logs used for internal monitoring. |
  |`*.vault.azure.net `|Manage secrets in the Azure Key Vault. Note: Ensure machines to replicate have access to this. |
  |aka.ms |Allow access to also known as links. Used for Azure Site Recovery appliance updates. |
  |download.microsoft.com/download |Allow downloads from Microsoft download. |
  |`*.servicebus.windows.net `|Communication between the appliance and the Azure Site Recovery service. |
  |`*.discoverysrv.windowsazure.com `<br><br>`*.hypervrecoverymanager.windowsazure.com `<br><br> `*.backup.windowsazure.com ` |Connect to Azure Site Recovery micro-service URLs.
  |`*.blob.core.windows.net `|Upload data to Azure storage which is used to create target disks. |


> [!NOTE]
> Private links are not supported with the preview release.

## Folder exclusions from Antivirus program

### If Antivirus Software is active on appliance

Exclude following folders from Antivirus software for smooth replication and to avoid connectivity issues.

C:\ProgramData\Microsoft Azure <br>
C:\ProgramData\ASRLogs <br>
C:\Windows\Temp\MicrosoftAzure 
C:\Program Files\Microsoft Azure Appliance Auto Update <br>
C:\Program Files\Microsoft Azure Appliance Configuration Manager <br>
C:\Program Files\Microsoft Azure Push Install Agent <br>
C:\Program Files\Microsoft Azure RCM Proxy Agent <br>
C:\Program Files\Microsoft Azure Recovery Services Agent <br>
C:\Program Files\Microsoft Azure Server Discovery Service <br>
C:\Program Files\Microsoft Azure Site Recovery Process Server <br>
C:\Program Files\Microsoft Azure Site Recovery Provider <br>
C:\Program Files\Microsoft Azure to On-Premise Reprotect agent <br>
C:\Program Files\Microsoft Azure VMware Discovery Service <br>
C:\Program Files\Microsoft On-Premise to Azure Replication agent <br>
E:\ <br>

### If Antivirus software is active on Source machine

If source machine has an Antivirus software active, installation folder should be excluded. So, exclude folder C:\ProgramData\ASR\agent for smooth replication.

## Prepare Azure account

To create and register the Azure Site Recovery replication appliance, you need an Azure account with:

- Contributor or Owner permissions on the Azure subscription.
- Permissions to register Azure Active Directory (AAD) apps.
- Owner or Contributor plus User Access Administrator permissions on the Azure subscription to create a Key Vault, used during registration of the Azure Site Recovery replication appliance with Azure.

If you just created a free Azure account, you're the owner of your subscription. If you're not the subscription owner, work with the owner for the required permissions.

## Prerequisites

**Here are the required key vault permissions**:

- Microsoft.OffAzure/*
- Microsoft.KeyVault/register/action
- Microsoft.KeyVault/vaults/read
- Microsoft.KeyVault/vaults/keys/read
- Microsoft.KeyVault/vaults/secrets/read
- Microsoft.Recoveryservices/*

**Follow these  steps to assign the required permissions**:

1. In the Azure portal, search for **Subscriptions**, and under **Services**, select **Subscriptions** search box to search for the Azure subscription.

2. In the **Subscriptions** page, select the subscription in which you created the Recovery Services vault.

3. In the selected subscription, select **Access control** (IAM) > **Check access**. In **Check access**, search for the relevant user account.

4. In **Add a role assignment**, select **Add,** select the Contributor or Owner role, and select the account. Then Select **Save**.

  To register the appliance, your Azure account needs permissions to register AAD apps.

  **Follow these steps to assign required permissions**:

  - In Azure portal, navigate to **Azure Active Directory** > **Users** > **User Settings**. In **User settings**, verify that Azure AD users can register applications (set to *Yes* by default).

  - In case the **App registrations** settings is set to *No*, request the tenant/global admin to assign the required permission. Alternately, the tenant/global admin can assign the Application Developer role to an account to allow the registration of AAD App.


## Prepare infrastructure

You need to set up an Azure Site Recovery replication appliance in the on-premises environment to enable recovery on your on premises machine. For detailed information on the operations performed by the appliance [see this section](vmware-azure-architecture-preview.md)

Go to **Recovery Services Vault** > **Getting Started**. In VMware machines to Azure, select
**Prepare Infrastructure** and proceed with the sections detailed below:

![Recovery Services Vault Preview](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

To set up a new appliance, you can use an OVF template (recommended) or PowerShell. Ensure you meet all the [hardware ](#hardware-requirements) and [software requirements](#software-requirements), and any other prerequisites.

## Create Azure Site Recovery replication appliance

You can create the Site Recovery replication appliance by using the OVF template or through PowerShell.

>[!NOTE]
> The appliance setup needs to be performed in a sequential manner. Parallel registration of multiple appliances cannot be executed.


### Create replication appliance through OVF template

We recommend this approach as Azure Site Recovery ensures all prerequisite configurations are handled by the template.
The OVF template spins up a machine with the required specifications.

![Prepare infrastructure for appliance creation](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

**Follow these steps:**

1. Download the OVF template to set up an appliance on your on-premises environment.
2. After the deployment is complete, power on the appliance VM to accept Microsoft Evaluation license.
3. In the next screen, provide password for the administrator user.
4. Select **Finalize,** the system reboots and you can login with the administrator user account.

### Set up the appliance through PowerShell

>[!NOTE]
> Enabling replication for physical machines is not supported with this preview. 

In case of any organizational restrictions, you can manually set up the Site Recovery replication appliance through PowerShell. Follow these steps:

1. Download the installers from [here](https://aka.ms/V2ARcmApplianceCreationPowershellZip) and place this folder on the Azure Site Recovery replication appliance.
2. After successfully copying the zip folder, unzip and extract the components of the folder.
3. Go to the path in which the folder is extracted to and execute the following PowerShell script as an administrator:

    **DRInstaller.ps1**  

## Register appliance
  Once you create the appliance, Microsoft Azure appliance configuration manager is launched automatically. Prerequisites such as internet connectivity, Time sync, system configurations and group policies (listed below) are validated.

  - CheckRegistryAccessPolicy - Prevents access to registry editing tools.
      - Key: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
      - DisableRegistryTools value should not be equal 0.

  - CheckCommandPromptPolicy - Prevents access to the command prompt.

      - Key: HKLM\SOFTWARE\Policies\Microsoft\Windows\System
      - DisableCMD value should not be equal 0.

  - CheckTrustLogicAttachmentsPolicy - Trust logic for file attachments.

      - Key: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Attachments
      - UseTrustedHandlers value should not be equal 3.

  - CheckPowershellExecutionPolicy - Turn on Script Execution.

      - PowerShell execution policy shouldn't be AllSigned or Restricted
      - Ensure the group policy 'Turn on Script Execution Attachment Manager' is not set to Disabled or 'Allow only signed scripts'


  **Use the following steps to register the appliance**:.

1. Configure the proxy settings by toggling on the **use proxy to connect to internet** option.

    All Azure Site Recovery services will use these settings to connect to the internet. Only HTTP proxy is supported.

2. Ensure the [required URLs](#allow-urls) are allowed and are reachable from the Azure Site Recovery replication appliance for continuous connectivity.

3. Once the prerequisites have been checked, in the next step information about all the appliance components will be fetched. Review the status of all components and then click **Continue**. After saving the details, proceed to choose the appliance connectivity.

4. After saving connectivity details, Select **Continue** to proceed to registration with Microsoft Azure.

5. Ensure the [prerequisites](#prerequisites) are met, proceed with registration.

    ![Register appliance](./media/deploy-vmware-azure-replication-appliance-preview/app-setup-register.png)

  - **Friendly name of appliance** : Provide a friendly name with which you want to track this appliance in the Azure portal under recovery services vault infrastructure.

  - **Azure Site Recovery replication appliance key** : Copy the key from the portal by navigating to **Recovery Services vault** > **Getting started** > **VMware to Azure Prepare Infrastructure**.

  - After pasting the key, select **Login.**
    You will be redirected to a new authentication tab.

      By default, an authentication code will be generated as highlighted below, in the authentication manager page. Use this code in the authentication tab.

  - Enter your Microsoft Azure credentials to complete registration.

      After successful registration, you can close the tab and move to configuration manager to continue the set up.

      ![authentication code](./media/deploy-vmware-azure-replication-appliance-preview/enter-code.png)

      > [!NOTE]
      > An authentication code expires within 5 minutes of generation. In case of inactivity for more than this duration, you will be prompted to login again to Azure.


6. Select **Login** to reconnect with the session. For authentication code, refer to the section *Summary* or *Register with Azure Recovery Services vault* in the configuration manger.

7. After successful login, Subscription, Resource Group and Recovery Services vault details are displayed. You can logout in case you want to change the vault. Else, Select **Continue** to proceed.

    ![Appliance registered](./media/deploy-vmware-azure-replication-appliance-preview/app-setup.png)

    After successful registration, proceed to configure vCenter details.

    ![Configuration of vCenter](./media/deploy-vmware-azure-replication-appliance-preview/vcenter-information.png)

8. Select **Add vCenter Server** to add vCenter information. Enter the server name or IP address of the vCenter and port information. Post that, provide username, password and friendly name and is used to fetch details of [virtual machine managed through the vCenter](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery). The user account details will be encrypted and stored locally in the machine.

>[!NOTE]
> iF  you're trying to add the same vCenter Server to multiple appliances, then ensure that the same friendly name is used in both the appliances.

9. After successfully saving the vCenter information, select **Add virtual machine credentials** to provide user details of the VMs discovered through the vCenter.

   >[!NOTE]
   > - For Linux OS, ensure to provide root credentials and for Windows OS, a user account with admin privileges should be added, these credentials will be used to push mobility agent on to the source VM during enable replication operation. The credentials can be chosen per VM in the Azure portal during enable replication workflow.
   > - Visit the appliance configurator to edit or add credentials to access your machines.

10. After successfully adding the details, select **Continue** to install all Azure Site Recovery replication appliance components and register with Azure services. This activity can take up to 30 minutes.

    Ensure you do not close the browser while configuration is in progress.

    >[!NOTE]
    > Appliance cloning is not supported with this preview. If you attempt to clone, it might disrupt the recovery flow.


## View Azure Site Recovery replication appliance in Azure portal

After successful configuration of Azure Site Recovery replication appliance, navigate to Azure portal, **Recovery Services Vault**.

Select **Prepare infrastructure (Preview)** under **Getting started**, you can see that an Azure Site Recovery replication appliance is already registered with this vault. Now you are all set! Start protecting your source machines through this replication appliance.

When you click  *Select 1 appliance(s)*, you will be re-directed to Azure Site Recovery replication appliance view, where the list of appliances registered to this vault is displayed.

You will also be able to see a tab for **Discovered items** that lists all of the discovered vCenter Servers/vSphere hosts."

![Replication appliance preview](./media/deploy-vmware-azure-replication-appliance-preview/discovered-items.png)

## Sizing and capacity
An appliance that uses an inbuilt process server to protect the workload can handle up to 200 virtual machines, based on the following configurations:

  |CPU |    Memory |    Cache disk size |    Data change rate |    Protected machines |
  |---|-------|--------|------|-------|
  |16 vCPUs (2 sockets * 8 cores @ 2.5 GHz)    | 32 GB |    1 TB |    >1 TB to 2 TB    | Use to replicate 151 to 200 machines.|

- You can perform discovery of all the machines in a vCenter server, using any of the replication appliances in the vault.

- You can [switch a protected machine](switch-replication-appliance-preview.md), between different appliances in the same vault, given the selected appliance is healthy.

For detailed information about how to use multiple appliances and failover a replication appliance, see [this article](switch-replication-appliance-preview.md)

## Next steps
Set up disaster recovery of [VMware VMs](vmware-azure-set-up-replication-tutorial-preview.md) to Azure.
