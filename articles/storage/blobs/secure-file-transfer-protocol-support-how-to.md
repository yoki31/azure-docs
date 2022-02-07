---
title: Connect to Azure Blob Storage using SFTP (preview) | Microsoft Docs
description: Learn how to enable SFTP support for your Azure Blob Storage account so that you can directly connect to your Azure Storage account by using an SFTP client.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/03/2022
ms.author: normesta
ms.reviewer: ylunagaria

---

# Connect to Azure Blob Storage by using the SSH File Transfer Protocol (SFTP) (preview)

You can securely connect to the Blob Storage endpoint of an Azure Storage account by using an SFTP client, and then upload and download files. This article shows you how to enable SFTP, and then connect to Blob Storage by using an SFTP client. 

To learn more about SFTP support in Azure Blob Storage, see [SSH File Transfer Protocol (SFTP) in Azure Blob Storage](secure-file-transfer-protocol-support.md).

> [!IMPORTANT]
> SFTP support is currently in PREVIEW and is available in [these regions](secure-file-transfer-protocol-support.md#regional-availability).
>
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>
> To enroll in the preview, see [this form](https://forms.office.com/r/gZguN0j65Y).

## Prerequisites

- A standard general-purpose v2 or premium block blob storage account. You can also enable SFTP as create the account. For more information on these types of storage accounts, see [Storage account overview](../common/storage-account-overview.md).

- The account redundancy option of the storage account is set to either locally-redundant storage (LRS) or zone-redundant storage (ZRS).

- The hierarchical namespace feature of the account must be enabled. To enable the hierarchical namespace feature, see [Upgrade Azure Blob Storage with Azure Data Lake Storage Gen2 capabilities](upgrade-to-data-lake-storage-gen2-how-to.md).

- If you're connecting from an on-premises network, make sure that your client allows outgoing communication through port 22. The SFTP uses that port.

## Register the feature

Before you can enable SFTP support, you must register the SFTP feature with your subscription.

### [Portal](#tab/azure-portal)

1. Sign in to the [Azure portal](https://portal.azure.com/).

2. Open the configuration page of your subscription.

3. Under **Settings**, select **Preview features**.

   > [!div class="mx-imgBorder"]
   > ![Preview setting](./media/secure-file-transfer-protocol-support-how-to/preview-features-setting.png)

4. In the **Preview features** page, select the **AllowSFTP** feature, and then select **Register**.

### [PowerShell](#tab/powershell)

1. Open a Windows PowerShell command window.
 
2.	Install **Az.Storage** preview module.

    ```powershell
    Install-Module -Name Az.Storage -AllowPrerelease 
    ```

    For more information about how to install PowerShell modules, see [Install the Azure PowerShell module](/powershell/azure/install-az-ps)

3. Sign in to your Azure subscription with the `Connect-AzAccount` command and follow the on-screen directions.

   ```powershell
   Connect-AzAccount
   ```

4. If your identity is associated with more than one subscription, then set your active subscription.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Replace the `<subscription-id>` placeholder value with the ID of your subscription.

5. Register the `AllowSFTP` feature by using the [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) command.

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowSFTP
   ```

   > [!NOTE]
   > The registration process might not complete immediately. Make sure to verify that the feature is registered before using it.

### [Azure CLI](#tab/azure-cli)

1. Open the [Azure Cloud Shell](../../cloud-shell/overview.md), or if you've [installed](/cli/azure/install-azure-cli) the Azure CLI locally, open a command console application such as Windows PowerShell.

2. Install the `storage-preview` extension.

   ```azurecli
   az extension add -n storage-preview
   ```

2. If you're using Azure CLI locally, run the login command.

   ```azurecli
   az login
   ```

   If the CLI can open your default browser, it will do so and load an Azure sign-in page.

   Otherwise, open a browser page at [https://aka.ms/devicelogin](https://aka.ms/devicelogin) and enter the authorization code displayed in your terminal. Then, sign in with your account credentials in the browser.

1. If your identity is associated with more than one subscription, then set your active subscription to subscription of the storage account.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Replace the `<subscription-id>` placeholder value with the ID of your subscription.

4. Register the `AllowSFTP` feature by using the [az feature register](/cli/azure/feature#az_feature_register) command.

   ```azurecli
   az feature register --namespace Microsoft.Storage --name AllowSFTP
   ```

   > [!NOTE]
   > The registration process might not complete immediately. Make sure to verify that the feature is registered before using it.

---

### Verify feature registration

Verify that the feature is registered before continuing with the other steps in this article. 

#### [Portal](#tab/azure-portal)

1. Open the **Preview features** page of your subscription. 

2. Locate the **AllowSFTP** feature and make sure that **Registered** appears in the **State** column.

#### [PowerShell](#tab/powershell)

To verify that the registration is complete, use the [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) command.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowSFTP
```

#### [Azure CLI](#tab/azure-cli)

To verify that the registration is complete, use the [az feature](/cli/azure/feature#az_feature_show) command.

```azurecli
az feature show --namespace Microsoft.Storage --name AllowSFTP
```

---

## Enable SFTP support

### [Portal](#tab/azure-portal)

1. In the [Azure portal](https://portal.azure.com/), navigate to your storage account.

2. Under **Settings**, select **SFTP**.

   > [!NOTE]
   > This option appears only if the hierarchical namespace feature of the account has been enabled. To enable the hierarchical namespace feature, see [Upgrade Azure Blob Storage with Azure Data Lake Storage Gen2 capabilities](upgrade-to-data-lake-storage-gen2-how-to.md).

3. Select **Enable SFTP**. 

   > [!div class="mx-imgBorder"]
   > ![Enable SFTP button](./media/secure-file-transfer-protocol-support-how-to/sftp-enable-option.png)

   >[!NOTE]
   > If no local users appear in the SFTP configuration page, you'll need to add at least one of them. To add local users, see the next section.

### [PowerShell](#tab/powershell)

To enable SFTP support, call the [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) command and set the `-EnableSftp` parameter to true. Remember to replace the values in angle brackets with your own values:

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

Set-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName -EnableSftp $true 
```

### [Azure CLI](#tab/azure-cli)

To enable SFTP support, call the [az storage account update](/cli/azure/storage/account#az-storage-account-update) command and set the `--enable-sftp` parameter to true. Remember to replace the values in angle brackets with your own values:

```azurecli
az storage account update -g <resource-group> -n <storage-account> --enable-sftp=true
```

---


## Configure permissions

Azure Storage doesn't support shared access signature (SAS), or Azure Active directory (Azure AD) authentication for accessing the SFTP endpoint. Instead, you must use an identity called local user that can be secured with an Azure generated password or a secure shell (SSH) key pair. To grant access to a connecting client, the storage account must have an identity associated with the password or key pair. That identity is called a *local user*. 

In this section, you'll learn how to create a local user, choose an authentication method, and assign permissions for that local user. 

To learn more about the SFTP permissions model, see [SFTP Permissions model](secure-file-transfer-protocol-support.md#sftp-permissions-model).

### [Portal](#tab/azure-portal)

1. In the [Azure portal](https://portal.azure.com/), navigate to your storage account.

2. Under **Settings**, select **SFTP**, and then select **Add local user**. 

   > [!div class="mx-imgBorder"]
   > ![Add local users button](./media/secure-file-transfer-protocol-support-how-to/sftp-local-user.png)

3. In the **Add local user** configuration pane, add the name of a user, and then select which methods of authentication you'd like associate with this local user. You can associate a password and / or an SSH key. 

   > [!IMPORTANT]
   > While you can enable both forms of authentication, SFTP clients can connect by using only one of them. Multifactor authentication, whereby both a valid password and a valid public and private key pair are required for successful authentication is not supported.

   If you select **Secure with a password**, then your password will appear when you've completed all of the steps in the **Add local user** configuration pane.

   If you select **Secure with SSH public key**, then select **Add key source** to specify a key source. 

   > [!div class="mx-imgBorder"]
   > ![Local user configuration pane](./media/secure-file-transfer-protocol-support-how-to/add-local-user-config-page.png)

   The following table describes each key source option:

   | Option | Guidance |
   |----|----|
   | Generate a new key pair | Use this option to create a new public / private key pair. The public key is stored in Azure with the key name that you provide. The private key can be downloaded after the local user has been successfully added. |
   | Use existing key stored in Azure | Use this option if you want to use a public key that is already stored in Azure. To find existing keys in Azure, see [List keys](../../virtual-machines/ssh-keys-portal.md#list-keys). When SFTP clients connect to Azure Blob Storage, those clients need to provide the private key associated with this public key. |
   | Use existing public key | Use this option if you want to upload a public key that is stored outside of Azure. If you don't have a public key, but would like to generate one outside of Azure, see [Generate keys with ssh-keygen](../../virtual-machines/linux/create-ssh-keys-detailed.md#generate-keys-with-ssh-keygen). |

4. Select **Next** to open the **Container permissions** tab of the configuration pane.

5. In the **Container permissions** tab, select the containers that you want to make available to this local user. Then, select which types of operations you want to enable this local user to perform.

   > [!div class="mx-imgBorder"]
   > ![Container permissions tab](./media/secure-file-transfer-protocol-support-how-to/container-perm-tab.png)

6. In the **Home directory** edit box, type the name of the container or the directory path (including the container name) that will be the default location associated with this this local user. 

   To learn more about the home directory, see [Home directory](secure-file-transfer-protocol-support.md#home-directory).

7. Select the **Add button** to add the local user.

   If you enabled password authentication, then the Azure generated password appears in a dialog box after the local user has been added. 

   > [!IMPORTANT]
   > You can't retrieve this password later, so make sure to copy the password, and then store it in a place where you can find it.

   If you chose to generate a new key pair, then you'll be prompted to download the private key of that key pair after the local user has been added.

### [PowerShell](#tab/powershell)

1. Decide which containers you want to make available to the local user and the types of operations that you want to enable this local user to perform. Create a permission scope object by using the the **New-AzStorageLocalUserPermissionScope** command, and setting the `-Permission` parameter of that command to one or more letters that correspond to access permission levels. Possible values are Read(r), Write (w), Delete (d), List (l), and Create (c).
  
   The following example sets creates a permission scope object that gives read and write permission to the `mycontainer` container.  

   ```powershell
   $permissionScope = New-AzStorageLocalUserPermissionScope -Permission rw -Service blob -ResourceName mycontainer 
   ``` 

2. Decide which methods of authentication you'd like associate with this local user. You can associate a password and / or an SSH key. 

   > [!IMPORTANT]
   > While you can enable both forms of authentication, SFTP clients can connect by using only one of them. Multifactor authentication, whereby both a valid password and a valid public and private key pair are required for successful authentication is not supported.

   If you want to use an SSH key, you'll need to public key of the public / private key pair. You can use existing public keys stored in Azure or use any existing public keys outside of Azure.
   
   To find existing keys in Azure, see [List keys](../../virtual-machines/ssh-keys-portal.md#list-keys). When SFTP clients connect to Azure Blob Storage, those clients need to provide the private key associated with this public key.

   If you want to use a public key outside of Azure, but you don't yet have one, then see [Generate keys with ssh-keygen](../../virtual-machines/linux/create-ssh-keys-detailed.md#generate-keys-with-ssh-keygen) for guidance about how to create one.   

   If you want to use a password to authenticate the local user, you can generate one after the local user is created.  

3. If want to use an SSH key, create a public key object by using the **New-AzStorageLocalUserSshPublicKey** command. Set the `-Key` parameter to a string that contains the key type and public key. In the following example, the key type is `ssh-rsa` and the key is `ssh-rsa a2V5...`.

   ```powershell
   $sshkey = "ssh-rsa a2V5..."
   $sshkey = New-AzStorageLocalUserSshPublicKey -Key $sshkey -Description "description for ssh public key"
   ```

4. Create a local user by using the **Set-AzStorageLocalUser** command. Set the `-PermissionScope` parameter to the permission scope object that you created earlier. If you are using an SSH key, then set the `SshAuthorization` parameter to the public key object that you created in the previous step. If you want to use a password to authenticate this local user, then set the `-HasSshPassword` parameter to `$true`.

   The following example creates a local user and then prints the key and permission scopes to the console.
  
   ```powershell
   $UserName = "mylocalusername"
   $localuser = Set-AzStorageLocalUser -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -UserName $UserName -HomeDirectory "mycontainer" -SshAuthorizedKey $sshkey -PermissionScope $permissionScope -HasSharedKey $true -HasSshKey $true -HasSshPassword $true

	$localuser
	$localuser.SshAuthorizedKeys | ft
	$localuser.PermissionScopes | ft
   ```

5. If you want to use a password to authenticate the user, you can create a password by using the **New-AzStorageLocalUserSshPassword** command. Set the `-UserName` parameter to the user name.

   The following example generates a password for the user.

   ```powershell
   $password = New-AzStorageLocalUserSshPassword -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -UserName $UserName
   $password 
   ```
   > [!IMPORTANT]
   > You can't retrieve this password later, so make sure to copy the password, and then store it in a place where you can find it. If you lose this password, you'll have to generate a new one.

### [Azure CLI](#tab/azure-cli)

1. First, decide which methods of authentication you'd like associate with this local user. You can associate a password and / or an SSH key. 

   > [!IMPORTANT]
   > While you can enable both forms of authentication, SFTP clients can connect by using only one of them. Multifactor authentication, whereby both a valid password and a valid public and private key pair are required for successful authentication is not supported.

   If you want to use an SSH key, you'll need to public key of the public / private key pair. You can use existing public keys stored in Azure or use any existing public keys outside of Azure.
   
   To find existing keys in Azure, see [List keys](../../virtual-machines/ssh-keys-portal.md#list-keys). When SFTP clients connect to Azure Blob Storage, those clients need to provide the private key associated with this public key.

   If you want to use a public key outside of Azure, but you don't yet have one, then see [Generate keys with ssh-keygen](../../virtual-machines/linux/create-ssh-keys-detailed.md#generate-keys-with-ssh-keygen) for guidance about how to create one.   

   If you want to use a password to authenticate the local user, you can generate one after the local user is created.

2. Create a local user by using the [az storage account local-user create](/cli/azure/storage/account/local-user#az-storage-account-local-user-create) command. Use the parameters of this command to specify the container and permission level. If you want to use an SSH key, then set the `--has-ssh-key` parameter to a string that contains the key type and public key. If you want to use a password to authenticate this local user, then set the `--has-ssh-password` parameter to `true`.

   The following example gives a local user name `contosouser` read and write access to a container named `contosocontainer`. An ssh-rsa key with a key value of `ssh-rsa a2V5...` is used for authentication.
  
   ```azurecli
   az storage account local-user create --account-name contosoaccount -g contoso-resource-group -n contosouser --home-directory contosocontainer --permission-scope permissions=rw service=blob resource-name=contosocontainer --ssh-authorized-key key="ssh-rsa ssh-rsa a2V5..." --has-ssh-key true --has-ssh-password true
   ```
3. If you want to use a password to authenticate the user, you can create a password by using the [az storage account local-user regenerate-password](/cli/azure/storage/account/local-user#az-storage-account-local-user-regenerate-password) command. Set the `-n` parameter to the local user name.

   The following example generates a password for the user.

   ```azurecli
   az storage account local-user regenerate-password --account-name contosoaccount -g contoso-resource-group -n contosouser  
   ```
   > [!IMPORTANT]
   > You can't retrieve this password later, so make sure to copy the password, and then store it in a place where you can find it. If you lose this password, you'll have to generate a new one.

--- 

## Connect an SFTP client

You can use any SFTP client to securely connect and then transfer files. The following screenshot shows a Windows PowerShell session that uses [Open SSH](/windows-server/administration/openssh/openssh_overview) and password authentication to connect and then upload a file named `logfile.txt`.  

> [!div class="mx-imgBorder"]
> ![Connect with Open SSH](./media/secure-file-transfer-protocol-support-how-to/ssh-connect-and-transfer.png)

> [!NOTE]
> You might be prompted to trust a host key. During the public preview, valid host keys are published [here](secure-file-transfer-protocol-host-keys.md).  

After the transfer is complete, you can view and manage the file in the Azure portal. 

> [!div class="mx-imgBorder"]
> ![Uploaded file appears in storage account](./media/secure-file-transfer-protocol-support-how-to/uploaded-file-in-storage-account.png)

> [!NOTE]
> The Azure portal uses the Blob REST API and Data Lake Storage Gen2 REST API. Being able to interact with an uploaded file in the Azure portal demonstrates the interoperability between SFTP and REST.

See the documentation of your SFTP client for guidance about how to connect and transfer files.

## See also

- [SSH File Transfer Protocol (SFTP) support in Azure Blob Storage](secure-file-transfer-protocol-support.md)
- [Known issues with SSH File Transfer Protocol (SFTP) support in Azure Blob Storage](secure-file-transfer-protocol-known-issues.md)
