---
title: Automatic VM Guest Patching for Azure VMs
description: Learn how to automatically patch virtual machines in Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: maintenance
ms.workload: infrastructure
ms.topic: how-to
ms.date: 10/20/2021
ms.author: mimckitt
ms.custom: devx-track-azurepowershell

---
# Automatic VM guest patching for Azure VMs

**Applies to:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Flexible scale sets

Enabling automatic VM guest patching for your Azure VMs helps ease update management by safely and automatically patching virtual machines to maintain security compliance.

Automatic VM guest patching has the following characteristics:
- Patches classified as *Critical* or *Security* are automatically downloaded and applied on the VM.
- Patches are applied during off-peak hours in the VM's time zone.
- Patch orchestration is managed by Azure and patches are applied following [availability-first principles](#availability-first-updates).
- Virtual machine health, as determined through platform health signals, is monitored to detect patching failures.
- Works for all VM sizes.

## How does automatic VM guest patching work?

If automatic VM guest patching is enabled on a VM, then the available *Critical* and *Security* patches are downloaded and applied automatically on the VM. This process kicks off automatically every month when new patches are released. Patch assessment and installation are automatic, and the process includes rebooting the VM as required.

The VM is assessed periodically every few days and multiple times within any 30-day period to determine the applicable patches for that VM. The patches can be installed any day on the VM during off-peak hours for the VM. This automatic assessment ensures that any missing patches are discovered at the earliest possible opportunity.

Patches are installed within 30 days of the monthly patch releases, following availability-first orchestration described below. Patches are installed only during off-peak hours for the VM, depending on the time zone of the VM. The VM must be running during the off-peak hours for patches to be automatically installed. If a VM is powered off during a periodic assessment, the VM will be automatically assessed and applicable patches will be installed automatically during the next periodic assessment (usually within a few days) when the VM is powered on.

Definition updates and other patches not classified as *Critical* or *Security* will not be installed through automatic VM guest patching. To install patches with other patch classifications or schedule patch installation within your own custom maintenance window, you can use [Update Management](./windows/tutorial-config-management.md#manage-windows-updates).

### Availability-first Updates

The patch installation process is orchestrated globally by Azure for all VMs that have automatic VM guest patching enabled. This orchestration follows availability-first principles across different levels of availability provided by Azure.

For a group of virtual machines undergoing an update, the Azure platform will orchestrate updates:

**Across regions:**
- A monthly update is orchestrated across Azure globally in a phased manner to prevent global deployment failures.
- A phase can have one or more regions, and an update moves to the next phases only if eligible VMs in a phase update successfully.
- Geo-paired regions are not updated concurrently and can't be in the same regional phase.
- The success of an update is measured by tracking the VM’s health post update. VM Health is tracked through platform health indicators for the VM.

**Within a region:**
- VMs in different Availability Zones are not updated concurrently with the same update.
- VMs that are not part of an availability set are batched on a best effort basis to avoid concurrent updates for all VMs in a subscription.

**Within an availability set:**
- All VMs in a common availability set are not updated concurrently.
-	VMs in a common availability set are updated within Update Domain boundaries and VMs across multiple Update Domains are not updated concurrently.

The patch installation date for a given VM may vary month-to-month, as a specific VM may be picked up in a different batch between monthly patching cycles.

### Which patches are installed?
The patches installed depend on the rollout stage for the VM. Every month, a new global rollout is started where all security and critical patches assessed for an individual VM are installed for that VM. The rollout is orchestrated across all Azure regions in batches (described in the availability-first patching section above).

The exact set of patches to be installed vary based on the VM configuration, including OS type, and assessment timing. It is possible for two identical VMs in different regions to get different patches installed if there are more or less patches available when the patch orchestration reaches different regions at different times. Similarly, but less frequently, VMs within the same region but assessed at different times (due to different Availability Zone or Availability Set batches) might get different patches.

As the Automatic VM Guest Patching does not configure the patch source, two similar VMs configured to different patch sources, such as public repository vs private repository, may also see a difference in the exact set of patches installed.

For OS types that release patches on a fixed cadence, VMs configured to the public repository for the OS can expect to receive the same set of patches across the different rollout phases in a month. For example, Windows VMs configured to the public Windows Update repository.

As a new rollout is triggered every month, a VM will receive at least one patch rollout every month if the VM is powered on during off-peak hours. This process ensures that the VM is patched with the latest available security and critical patches on a monthly basis. To ensure consistency in the set of patches installed, you can configure your VMs to assess and download patches from your own private repositories.

## Supported OS images
Only VMs created from certain OS platform images are currently supported. Custom images are currently not supported.

> [!NOTE]
> Automatic VM guest patching is only supported on Gen1 images. 

The following platform SKUs are currently supported (and more are added periodically):

| Publisher               | OS Offer      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Canonical  | 0001-com-ubuntu-pro-bionic | pro-18_04-lts |
| Canonical  | 0001-com-ubuntu-server-focal | 20_04-lts |
| Canonical  | 0001-com-ubuntu-pro-focal | pro-20_04-lts |
| Redhat  | RHEL | 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7.8, 7_9, 7-RAW, 7-LVM |
| Redhat  | RHEL | 8, 8.1, 8.2, 8_3, 8_4, 8-LVM |
| Redhat  | RHEL-RAW | 8-raw |
| OpenLogic  | CentOS | 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7_8, 7_9, 7-LVM |
| OpenLogic  | CentOS | 8.0, 8_1, 8_2, 8_3, 8-lvm |
| SUSE  | sles-12-sp5 | gen1, gen2 |
| SUSE  | sles-15-sp2 | gen1, gen2 |
| MicrosoftWindowsServer  | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

> [!NOTE]
>Automatic VM guest patching, on-demand patch assessment and on-demand patch installation are supported only on VMs created from images with the exact combination of publisher, offer and sku from the supported OS images list. Custom images or any other publisher, offer, sku combinations are not supported.

## Patch orchestration modes
VMs on Azure now support the following patch orchestration modes:

**AutomaticByPlatform (Azure-orchestrated patching):**
- This mode is supported for both Linux and Windows VMs.
- This mode enables automatic VM guest patching for the virtual machine and subsequent patch installation is orchestrated by Azure.
- This mode is required for availability-first patching.
- This mode is only supported for VMs that are created using the supported OS platform images above.
- For Windows VMs, setting this mode also disables the native Automatic Updates on the Windows virtual machine to avoid duplication.
- To use this mode on Linux VMs, set the property `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` in the VM template.
- To use this mode on Windows VMs, set the property  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` in the VM template.

**AutomaticByOS:**
- This mode is supported only for Windows VMs.
- This mode enables Automatic Updates on the Windows virtual machine, and patches are installed on the VM through Automatic Updates.
- This mode does not support availability-first patching.
- This mode is set by default if no other patch mode is specified for a Windows VM.
- To use this mode on Windows VMs, set the property `osProfile.windowsConfiguration.enableAutomaticUpdates=true`, and set the property  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` in the VM template.

**Manual:**
- This mode is supported only for Windows VMs.
- This mode disables Automatic Updates on the Windows virtual machine.
- This mode does not support availability-first patching.
- This mode should be set when using custom patching solutions.
- To use this mode on Windows VMs, set the property `osProfile.windowsConfiguration.enableAutomaticUpdates=false`, and set the property  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` in the VM template.

**ImageDefault:**
- This mode is supported only for Linux VMs.
- This mode does not support availability-first patching.
- This mode honors the default patching configuration in the image used to create the VM.
- This mode is set by default if no other patch mode is specified for a Linux VM.
- To use this mode on Linux VMs, set the property `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` in the VM template.

> [!NOTE]
>For Windows VMs, the property `osProfile.windowsConfiguration.enableAutomaticUpdates` can only be set when the VM is first created. This impacts certain patch mode transitions. Switching between AutomaticByPlatform and Manual modes is supported on VMs that have `osProfile.windowsConfiguration.enableAutomaticUpdates=false`. Similarly switching between AutomaticByPlatform and AutomaticByOS modes is supported on VMs that have `osProfile.windowsConfiguration.enableAutomaticUpdates=true`. Switching between AutomaticByOS and Manual modes is not supported.

## Requirements for enabling automatic VM guest patching

- The virtual machine must have the Azure VM Agent for [Windows](./extensions/agent-windows.md) or [Linux](./extensions/agent-linux.md) installed.
- For Linux VMs, the Azure Linux agent must be version 2.2.53.1 or higher. [Update the Linux agent](./extensions/update-linux-agent.md) if the current version is lower than the required version.
- For Windows VMs, the Windows Update service must be running on the virtual machine.
- The virtual machine must be able to access the configured update endpoints. If your virtual machine is configured to use private repositories for Linux or Windows Server Update Services (WSUS) for Windows VMs, the relevant update endpoints must be accessible.
- Use Compute API version 2021-03-01 or higher to access all functionality including on-demand assessment and on-demand patching.
- Custom images are not currently supported.

## Enable automatic VM guest patching
Automatic VM guest patching can be enabled on any Windows or Linux VM that is created from a supported platform image. To enable automatic VM guest patching on a Windows VM, ensure that the property *osProfile.windowsConfiguration.enableAutomaticUpdates* is set to *true* in the VM template definition. This property can only be set when creating the VM. This additional property is not applicable for Linux VMs.

### REST API for Linux VMs
The following example describes how to enable automatic VM guest patching:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### REST API for Windows VMs
The following example describes how to enable automatic VM guest patching:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### Azure PowerShell for Windows VMs
Use the [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) cmdlet to enable automatic VM guest patching when creating or updating a VM.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### Azure CLI for Windows VMs
Use [az vm create](/cli/azure/vm#az_vm_create) to enable automatic VM guest patching when creating a new VM. The following example configures automatic VM guest patching for a VM named *myVM* in the resource group named *myResourceGroup*:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

To modify an existing VM, use [az vm update](/cli/azure/vm#az_vm_update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## Enablement and assessment

> [!NOTE]
>It can take more than three hours to enable automatic VM guest updates on a VM, as the enablement is completed during the VM's off-peak hours. As assessment and patch installation occur only during off-peak hours, your VM must be also be running during off-peak hours to apply patches.

When automatic VM guest patching is enabled for a VM, a VM extension of type `Microsoft.CPlat.Core.LinuxPatchExtension` is installed on a Linux VM or a VM extension of type `Microsoft.CPlat.Core.WindowsPatchExtension` is installed on a Windows VM. This extension does not need to be manually installed or updated, as this extension is managed by the Azure platform as part of the automatic VM guest patching process.

It can take more than three hours to enable automatic VM guest updates on a VM, as the enablement is completed during the VM's off-peak hours. The extension is also installed and updated during off-peak hours for the VM. If the VM's off-peak hours end before enablement can be completed, the enablement process will resume during the next available off-peak time.

Automatic updates are disabled in most scenarios, and patch installation is done through the extension going forward. The following conditions apply.
- If a Windows VM previously had Automatic Windows Update turned on through the AutomaticByOS patch mode, then Automatic Windows Update is turned off for the VM when the extension is installed.
- For Ubuntu VMs, the default automatic updates are disabled automatically when Automatic VM Guest Patching completes enablement.
- For RHEL, automatic updates need to be manually disabled. Execute:

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

To verify whether automatic VM guest patching has completed and the patching extension is installed on the VM, you can review the VM's instance view. If the enablement process is complete, the extension will be installed and the assessment results for the VM will be available under `patchStatus`. The VM's instance view can be accessed through multiple ways as described below.

### REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### Azure PowerShell
Use the [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet with the `-Status` parameter to access the instance view for your VM.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell currently only provides information on the patch extension. Information about `patchStatus` will also be available soon through PowerShell.

### Azure CLI
Use [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) to access the instance view for your VM.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### Understanding the patch status for your VM

The `patchStatus` section of the instance view response provides details on the latest assessment and the last patch installation for your VM.

The assessment results for your VM can be reviewed under the `availablePatchSummary` section. An assessment is periodically conducted for a VM that has automatic VM guest patching enabled. The count of available patches after an assessment is provided under `criticalAndSecurityPatchCount` and `otherPatchCount` results. Automatic VM guest patching will install all patches assessed under the *Critical* and *Security* patch classifications. Any other assessed patch is skipped.

The patch installation results for your VM can be reviewed under the `lastPatchInstallationSummary` section. This section provides details on the last patch installation attempt on the VM, including the number of patches that were installed, pending, failed or skipped. Patches are installed only during the off-peak hours maintenance window for the VM. Pending and failed patches are automatically retried during the next off-peak hours maintenance window.

## Disable automatic VM guest patching
Automatic VM guest patching can be disabled by changing the [patch orchestration mode](#patch-orchestration-modes) for the VM.

To disable automatic VM guest patching on a Linux VM, change the patch mode to `ImageDefault`.

To enable automatic VM guest patching on a Windows VM, the property `osProfile.windowsConfiguration.enableAutomaticUpdates` determines which patch modes can be set on the VM and this property can only be set when the VM is first created. This impacts certain patch mode transitions:
- For VMs that have `osProfile.windowsConfiguration.enableAutomaticUpdates=false`, disable automatic VM guest patching by changing the patch mode to `Manual`.
- For VMs that have `osProfile.windowsConfiguration.enableAutomaticUpdates=true`, disable automatic VM guest patching by changing the patch mode to `AutomaticByOS`.
- Switching between AutomaticByOS and Manual modes is not supported.

Use the examples from the [enablement](#enable-automatic-vm-guest-patching) section above in this article for API, PowerShell and CLI usage examples to set the required patch mode.

## On-demand patch assessment
If automatic VM guest patching is already enabled for your VM, a periodic patch assessment is performed on the VM during the VM's off-peak hours. This process is automatic and the results of the latest assessment can be reviewed through the VM's instance view as described earlier in this document. You can also trigger an on-demand patch assessment for your VM at any time. Patch assessment can take a few minutes to complete and the status of the latest assessment is updated on the VM's instance view.

> [!NOTE]
>On-demand patch assessment does not automatically trigger patch installation. If you have enabled automatic VM guest patching then the assessed and applicable patches for the VM will be installed during the VM's off-peak hours, following the availability-first patching process described earlier in this document.

### REST API
Use the [Assess Patches](/rest/api/compute/virtual-machines/assess-patches) API to assess available patches for your virtual machine.
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### Azure PowerShell
Use the [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) cmdlet to assess available patches for your virtual machine.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### Azure CLI
Use [az vm assess-patches](/cli/azure/vm#az_vm_assess_patches) to assess available patches for your virtual machine.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## On-demand patch installation
If automatic VM guest patching is already enabled for your VM, a periodic patch installation of Security and Critical patches is performed on the VM during the VM's off-peak hours. This process is automatic and the results of the latest installation can be reviewed through the VM's instance view as described earlier in this document.

You can also trigger an on-demand patch installation for your VM at any time. Patch installation can take a few minutes to complete and the status of the latest installation is updated on the VM's instance view.

You can use on-demand patch installation to install all patches of one or more patch classifications. You can also choose to include or exclude specific packages for Linux or specific KB IDs for Windows. When triggering an on-demand patch installation, ensure that you specify at least one patch classification or at least one patch (package for Linux, KB ID for Windows) in the inclusion list.

### REST API
Use the [Install Patches](/rest/api/compute/virtual-machines/install-patches) API to install patches on your virtual machine.

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/installPatches?api-version=2020-12-01`
```

Example request body for Linux:
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "linuxParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

Example request body for Windows:
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "windowsParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

### Azure PowerShell
Use the [Invoke-AzVMInstallPatch](/powershell/module/az.compute/invoke-azvminstallpatch) cmdlet to install patches on your virtual machine.

Example to install certain packages on a Linux VM:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Linux -ClassificationToIncludeForLinux "Security" -PackageNameMaskToInclude ["package123"] -PackageNameMaskToExclude ["package567"]
```

Example to install all Critical patches on a Windows VM:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT2H" -RebootSetting "Never" -Windows   -ClassificationToIncludeForWindows Critical
```

Example to install all Security patches on a Windows VM, while including and excluding patches with specific KB IDs and excluding any patch that requires a reboot:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Windows -ClassificationToIncludeForWindows "Security" -KBNumberToInclude ["KB1234567", "KB123567"] -KBNumberToExclude ["KB1234702", "KB1234802"] -ExcludeKBsRequiringReboot
```

### Azure CLI
Use [az vm install-patches](/cli/azure/vm#az_vm_install_patches) to install patches on your virtual machine.

Example to install all Critical patches on a Linux VM:
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-linux Critical
```

Example to install all Critical and Security patches on a Windows VM, while excluding any patch that requires a reboot:
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-win Critical Security --exclude-kbs-requiring-reboot true
```

## Next steps
> [!div class="nextstepaction"]
> [Learn more about creating and managing Windows virtual machines](./windows/tutorial-manage-vm.md)
