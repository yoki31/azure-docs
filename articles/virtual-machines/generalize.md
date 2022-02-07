---
title: Generalize a VM before creating an image
description: Generalized a VM to remove machine specific information before creating an image. 
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2021
ms.author: cynthn
ms.custom: portal

---

# Remove machine specific information by generalizing a VM before creating an image

Generalizing a VM is not necessary for creating an image in an [Azure Compute Gallery](shared-image-galleries.md#generalized-and-specialized-images) unless you specifically want to create a generalized image. Generalizing is required when creating a managed image outside of a gallery.

Generalizing removes machine specific information so the image can be used to create multiple VMs. Once the VM has been generalized, you need to let the platform know that the VM has been generalized so that the boot sequence can be set correctly. Once a VM is generalized, it should not be restarted.


## Linux

First you'll deprovision the VM by using the Azure VM agent to delete machine-specific files and data. Use the `waagent` command with the `-deprovision+user` parameter on your source Linux VM. For more information, see the [Azure Linux Agent user guide](./extensions/agent-linux.md). This process can't be reversed.

1. Connect to your Linux VM with an SSH client.
2. In the SSH window, enter the following command:
   ```bash
    sudo waagent -deprovision+user
   ```
   > [!NOTE]
   > Only run this command on a VM that you'll capture as an image. This command does not guarantee that the image is cleared of all sensitive information or is suitable for redistribution. The `+user` parameter also removes the last provisioned user account. To keep user account credentials in the VM, use only `-deprovision`.
 
3. Enter **y** to continue. You can add the `-force` parameter to avoid this confirmation step.
4. After the command completes, enter **exit** to close the SSH client.  The VM will still be running at this point.


Deallocate the VM that you deprovisioned with `az vm deallocate` so that it can be generalized.

```azurecli-interactive
az vm deallocate \
   --resource-group myResourceGroup \
   --name myVM
```

Then the VM needs to be marked as generalized on the platform. 

```azurecli-interactive
az vm generalize \
   --resource-group myResourceGroup \
   --name myVM
```

## Windows 

Sysprep removes all your personal account and security information, and then prepares the machine to be used as an image. For information about Sysprep, see [Sysprep overview](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Make sure the server roles running on the machine are supported by Sysprep. For more information, see [Sysprep support for server roles](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) and [Unsupported scenarios](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> After you have run Sysprep on a VM, that VM is considered *generalized* and cannot be restarted. The process of generalizing a VM is not reversible. If you need to keep the original VM functioning, you should create a [copy of the VM](./windows/create-vm-specialized.md#option-3-copy-an-existing-azure-vm) and generalize its copy. 
>
> Sysprep requires the drives to be fully decrypted. If you have enabled encryption on your VM, disable encryption before you run Sysprep.
>
> If you plan to run Sysprep before uploading your virtual hard disk (VHD) to Azure for the first time, make sure you have [prepared your VM](./windows/prepare-for-upload-vhd-image.md).  
> 
> We do not support custom answer file in the sysprep step, hence you should not use the "/unattend:_answerfile_" switch with your sysprep command.
> 

To generalize your Windows VM, follow these steps:

1. Sign in to your Windows VM.
   
2. Open a Command Prompt window as an administrator. 

3. Delete the panther directory (C:\Windows\Panther). Then change the directory to %windir%\system32\sysprep, and then run `sysprep.exe`.
   
4. In the **System Preparation Tool** dialog box, select **Enter System Out-of-Box Experience (OOBE)** and select the **Generalize** check box.
   
5. For **Shutdown Options**, select **Shutdown**.
   
6. Select **OK**.
   
    :::image type="content" source="windows/media/upload-generalized-managed/sysprepgeneral.png" alt-text="![Start Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)":::

6. When Sysprep completes, it shuts down the VM. Do not restart the VM.

> [!TIP]
> **Optional** Use [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) to optimize your image and reduce your VM's first boot time.
>
> To optimize your image, mount your VHD by double-clicking on it in Windows explorer, and then run DISM with the `/optimize-image` parameter.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Where D: is the mounted VHD's path.
>
> Running `DISM /optimize-image` should be the last modification you make to your VHD. If you make any changes to your VHD prior to deployment, you'll have to run `DISM /optimize-image` again.

Once Sysprep has finished, set the status of the virtual machine to **Generalized**.
   
```azurepowershell-interactive
Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
```
