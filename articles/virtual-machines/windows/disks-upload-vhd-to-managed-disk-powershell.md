---
title: Upload a VHD to Azure or copy a disk across regions - Azure PowerShell
description: Learn how to upload a VHD to an Azure managed disk and copy a managed disk across regions, using Azure PowerShell, via direct upload.    
author: roygara
ms.author: rogarana
ms.date: 02/01/2022
ms.topic: how-to
ms.service: storage
ms.tgt_pltfrm: linux
ms.subservice: disks 
ms.custom: devx-track-azurepowershell
---

# Upload a VHD to Azure or copy a managed disk to another region - Azure PowerShell

**Applies to:** :heavy_check_mark: Windows VMs 

This article explains how to either upload a VHD from your local machine to an Azure managed disk or copy a managed disk to another region, using the Azure PowerShell module. The process of uploading a managed disk, also known as direct upload, enables you to upload a VHD up to 32 TiB in size directly into a managed disk. Currently, direct upload is supported for standard HDD, standard SSD, and premium SSDs. It isn't supported for ultra disks, yet.

If you're providing a backup solution for IaaS VMs in Azure, you should use direct upload to restore customer backups to managed disks. When uploading a VHD from a source external to Azure, speeds depend on your local bandwidth. When uploading or copying from an Azure VM, your bandwidth would be the same as standard HDDs.

## Getting started

There are two ways you can upload a VHD with the Azure PowerShell module: You can either use the [Add-AzVHD](/powershell/module/az.compute/add-azvhd?view=azps-7.1.0&viewFallbackFrom=azps-5.4.0) command, which will automate most of the process for you, or you can perform the upload manually with AzCopy.

Generally, you should use [Add-AzVHD](#use-add-azvhd). However, if you need to upload a VHD that is larger than 50 GiB, consider [uploading the VHD manually with AzCopy](#manual-upload). VHDs 50 GiB and larger will upload faster using AzCopy.

For guidance on how to copy a managed disk from one region to another, see [Copy a managed disk](#copy-a-managed-disk).

## Use Add-AzVHD

### Prerequisites

- [Install the Azure PowerShell module](/powershell/azure/install-Az-ps).
- A VHD [has been prepared for Azure](prepare-for-upload-vhd-image.md), stored locally.
    - On Windows: You don't need to convert your VHD to VHDx, convert it a fixed size, or resize it to include the 512-byte offset. `Add-AZVHD` performs these functions for you.
        - [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) must be enabled for Add-AzVHD to perform these functions.
    - On Linux: You must perform these actions manually. See [Resizing VHDs](/azure/virtual-machines/linux/create-upload-generic?branch=pr-en-us-185925) for details.

### Upload a VHD

The following example uploads a VHD from your local machine to a new Azure managed disk using [Add-AzVHD](/powershell/module/az.compute/add-azvhd?view=azps-7.1.0&viewFallbackFrom=azps-5.4.0). Replace `<your-filepath-here>`, `<your-resource-group-name>`,`<desired-region>`, and `<desired-managed-disk-name>` with your parameters:

```azurepowershell
# Required parameters
$path = <your-filepath-here>.vhd
$resourceGroup = <your-resource-group-name>
$location = <desired-region>
$name = <desired-managed-disk-name>

# Optional parameters
# $Zone = <desired-zone>
# $sku=<desired-SKU>

# To use $Zone or #sku, add -Zone or -DiskSKU parameters to the command
Add-AzVhd -LocalFilePath $path -ResourceGroupName $resourceGroup -Location $location -DiskName $name
```

## Manual upload

### Prerequisites

- Download the latest [version of AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Install the Azure PowerShell module](/powershell/azure/install-Az-ps).
- A fixed size VHD that [has been prepared for Azure](prepare-for-upload-vhd-image.md), stored locally.

To upload your VHD to Azure, you'll need to create an empty managed disk that is configured for this upload process. Before you create one, there's some additional information you should know about these disks.

This kind of managed disk has two unique states:

- ReadyToUpload, which means the disk is ready to receive an upload but, no [secure access signature](../../storage/common/storage-sas-overview.md) (SAS) has been generated.
- ActiveUpload, which means that the disk is ready to receive an upload and the SAS has been generated.

> [!NOTE]
> While in either of these states, the managed disk will be billed at [standard HDD pricing](https://azure.microsoft.com/pricing/details/managed-disks/), regardless of the actual type of disk. For example, a P10 will be billed as an S10. This will be true until `revoke-access` is called on the managed disk, which is required in order to attach the disk to a VM.

### Create an empty managed disk

Before you can create an empty standard HDD for uploading, you'll need the file size of the VHD you want to upload, in bytes. The example code will get that for you but, to do it yourself you can use: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. This value is used when specifying the **-UploadSizeInBytes** parameter.

Now, on your local shell, create an empty standard HDD for uploading by specifying the **Upload** setting in the **-CreateOption** parameter as well as the **-UploadSizeInBytes** parameter in the [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) cmdlet. Then call [New-AzDisk](/powershell/module/az.compute/new-azdisk) to create the disk.

Replace `<yourdiskname>`, `<yourresourcegroupname>`, and `<yourregion>` then run the following commands:

> [!TIP]
> If you are creating an OS disk, add `-HyperVGeneration '<yourGeneration>'` to `New-AzDiskConfig`.

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

If you would like to upload either a premium SSD or a standard SSD, replace **Standard_LRS** with either **Premium_LRS** or **StandardSSD_LRS**. Ultra disks are not yet supported.

Now that you've created an empty managed disk that is configured for the upload process, you can upload a VHD to it. To upload a VHD to the disk, you'll need a writeable SAS, so that you can reference it as the destination for your upload.

To generate a writable SAS of your empty managed disk, replace `<yourdiskname>`and `<yourresourcegroupname>`, then use the following commands:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

### Upload a VHD

Now that you have a SAS for your empty managed disk, you can use it to set your managed disk as the destination for your upload command.

Use AzCopy v10 to upload your local VHD file to a managed disk by specifying the SAS URI you generated.

This upload has the same throughput as the equivalent [standard HDD](../disks-types.md#standard-hdds). For example, if you have a size that equates to S4, you will have a throughput of up to 60 MiB/s. But, if you have a size that equates to S70, you will have a throughput of up to 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

After the upload is complete, and you no longer need to write any more data to the disk, revoke the SAS. Revoking the SAS will change the state of the managed disk and allow you to attach the disk to a VM.

Replace `<yourdiskname>`and `<yourresourcegroupname>`, then run the following command:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## Copy a managed disk

Direct upload also simplifies the process of copying a managed disk. You can either copy within the same region or copy your managed disk to another region.

The following script will do this for you, the process is similar to the steps described earlier, with some differences, since you're working with an existing disk.

> [!IMPORTANT]
> You must add an offset of 512 when you're providing the disk size in bytes of a managed disk from Azure. This is because Azure omits the footer when returning the disk size. The copy will fail if you don't do this. The following script already does this for you.

Replace the `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` and `<yourTargetLocationHere>` (an example of a location value would be uswest2) with your values, then run the following script in order to copy a managed disk.

> [!TIP]
> If you are creating an OS disk, add `-HyperVGeneration '<yourGeneration>'` to `New-AzDiskConfig`.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## Next steps

Now that you've successfully uploaded a VHD to a managed disk, you can attach your disk to a VM and begin using it.

To learn how to attach a data disk to a VM, see our article on the subject: [Attach a data disk to a Windows VM with PowerShell](attach-disk-ps.md). To use the disk as the OS disk, see [Create a Windows VM from a specialized disk](create-vm-specialized.md#create-the-new-vm).
