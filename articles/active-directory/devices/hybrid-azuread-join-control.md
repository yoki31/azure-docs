---
title: Targeted deployments of hybrid Azure AD join
description: Learn how to do a targeted deployment of hybrid Azure AD join before enabling it across the entire organization all at once.

services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 01/20/2022

ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo

ms.collection: M365-identity-device-management
---
# Hybrid Azure AD join targeted deployment

You can validate your [planning and prerequisites](hybrid-azuread-join-plan.md) for hybrid Azure AD joining devices using a targeted deployment before enabling it across the entire organization. This article will explain how to accomplish a targeted deployment of hybrid Azure AD join.

## Targeted deployment of hybrid Azure AD join on Windows current devices

For devices running Windows 10, the minimum supported version is Windows 10 (version 1607) to do hybrid join. As a best practice, upgrade to the latest version of Windows 10 or 11. If you need to support previous operating systems, see the section [Supporting down-level devices](#supporting-down-level-devices)

To do a targeted deployment of hybrid Azure AD join on Windows current devices, you need to:

1. Clear the Service Connection Point (SCP) entry from Active Directory (AD) if it exists.
1. Configure client-side registry setting for SCP on your domain-joined computers using a Group Policy Object (GPO).
1. If you're using Active Directory Federation Services (AD FS), you must also configure the client-side registry setting for SCP on your AD FS server using a GPO.
1. You may also need to [customize synchronization options](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) in Azure AD Connect to enable device synchronization. 

### Clear the SCP from AD

Use the Active Directory Services Interfaces Editor (ADSI Edit) to modify the SCP objects in AD.

1. Launch the **ADSI Edit** desktop application from and administrative workstation or a domain controller as an Enterprise Administrator.
1. Connect to the **Configuration Naming Context** of your domain.
1. Browse to **CN=Configuration,DC=contoso,DC=com** > **CN=Services** > **CN=Device Registration Configuration**.
1. Right-click on the leaf object **CN=62a0ff2e-97b9-4513-943f-0d221bd30080** and select **Properties**.
   1. Select **keywords** from the **Attribute Editor** window and select **Edit**.
   1. Select the values of **azureADId** and **azureADName** (one at a time) and select **Remove**.
1. Close **ADSI Edit**.

### Configure client-side registry setting for SCP

Use the following example to create a Group Policy Object (GPO) to deploy a registry setting configuring an SCP entry in the registry of your devices.

1. Open a Group Policy Management console and create a new Group Policy Object in your domain.
   1. Provide your newly created GPO a name (for example, ClientSideSCP).
1. Edit the GPO and locate the following path: **Computer Configuration** > **Preferences** > **Windows Settings** > **Registry**.
1. Right-click on the Registry and select **New** > **Registry Item**.
   1. On the **General** tab, configure the following.
      1. Action: **Update**.
      1. Hive: **HKEY_LOCAL_MACHINE**.
      1. Key Path: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**.
      1. Value name: **TenantId**.
      1. Value type: **REG_SZ**.
      1. Value data: The GUID or **Tenant ID** of your Azure AD instance (This value can be found in the **Azure portal** > **Azure Active Directory** > **Properties** > **Tenant ID**).
   1. Select **OK**.
1. Right-click on the Registry and select **New** > **Registry Item**.
   1. On the **General** tab, configure the following.
      1. Action: **Update**.
      1. Hive: **HKEY_LOCAL_MACHINE**.
      1. Key Path: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**.
      1. Value name: **TenantName**.
      1. Value type: **REG_SZ**.
      1. Value data: Your verified **domain name** if you're using federated environment such as AD FS. Your verified **domain name** or your onmicrosoft.com domain name, for example `contoso.onmicrosoft.com` if you're using managed environment.
   1. Select **OK**.
1. Close the editor for the newly created GPO.
1. Link the newly created GPO to the correct OU containing domain-joined computers that belong to your controlled rollout population.

### Configure AD FS settings

If you're using AD FS, you first need to configure client-side SCP using the instructions mentioned earlier by linking the GPO to your AD FS servers. The SCP object defines the source of authority for device objects. It can be on-premises or Azure AD. When client-side SCP is configured for AD FS, the source for device objects is established as Azure AD.

> [!NOTE]
> If you failed to configure client-side SCP on your AD FS servers, the source for device identities would be considered as on-premises. ADFS will then start deleting device objects from on-premises directory after the stipulated period defined in the ADFS Device Registration's attribute "MaximumInactiveDays". ADFS Device Registration objects can be found using the [Get-AdfsDeviceRegistration cmdlet](/powershell/module/adfs/get-adfsdeviceregistration).

## Supporting down-level devices

To register Windows down-level devices, organizations must install [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554) available on the Microsoft Download Center.

You can deploy the package by using a software distribution system like [Microsoft Endpoint Configuration Manager](/configmgr/). The package supports the standard silent installation options with the quiet parameter. The current branch of Configuration Manager offers benefits over earlier versions, like the ability to track completed registrations.

The installer creates a scheduled task on the system that runs in the user context. The task is triggered when the user signs in to Windows. The task silently joins the device with Azure AD with the user credentials after authenticating with Azure AD.

To control the device registration, you should deploy the Windows Installer package to your selected group of Windows down-level devices.

> [!NOTE]
> If a SCP is not configured in AD, then you should follow the same approach as described to [Configure client-side registry setting for SCP](#configure-client-side-registry-setting-for-scp)) on your domain-joined computers using a Group Policy Object (GPO).

## Post validation

After you verify that everything works as expected, you can automatically register the rest of your Windows current and down-level devices with Azure AD by [configuring the SCP using Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## Next steps

- [Plan your hybrid Azure Active Directory join implementation](hybrid-azuread-join-plan.md)
- [Configure hybrid Azure AD join](howto-hybrid-azure-ad-join.md)
- [Configure hybrid Azure Active Directory join manually](hybrid-azuread-join-manual.md)
- [Use Conditional Access to require compliant or hybrid Azure AD joined device](../conditional-access/howto-conditional-access-policy-compliant-device.md)
