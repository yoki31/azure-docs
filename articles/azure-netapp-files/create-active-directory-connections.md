---
title: Create and manage Active Directory connections for Azure NetApp Files | Microsoft Docs
description: This article shows you how to create and manage Active Directory connections for Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.topic: how-to
ms.date: 01/21/2022
ms.author: anfdocs
---
# Create and manage Active Directory connections for Azure NetApp Files

Several features of Azure NetApp Files require that you have an Active Directory connection.  For example, you need to have an Active Directory connection before you can create an [SMB volume](azure-netapp-files-create-volumes-smb.md), a [NFSv4.1 Kerberos volume](configure-kerberos-encryption.md), or a [dual-protocol volume](create-volumes-dual-protocol.md).  This article shows you how to create and manage Active Directory connections for Azure NetApp Files.

## Before you begin  

* You must have already set up a capacity pool. See [Create a capacity pool](azure-netapp-files-set-up-capacity-pool.md).   
* A subnet must be delegated to Azure NetApp Files. See [Delegate a subnet to Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="requirements-for-active-directory-connections"></a>Requirements and considerations for Active Directory connections

* You can configure only one Active Directory (AD) connection per subscription and per region.   

    Azure NetApp Files does not support multiple AD connections in a single *region*, even if the AD connections are in different NetApp accounts. However, you can have multiple AD connections in a single subscription if the AD connections are in different regions. If you need multiple AD connections in a single region, you can use separate subscriptions to do so.  

    The AD connection is visible only through the NetApp account it is created in. However, you can enable the Shared AD feature to allow NetApp accounts that are under the same subscription and same region to use an AD server created in one of the NetApp accounts. See [Map multiple NetApp accounts in the same subscription and region to an AD connection](#shared_ad). When you enable this feature, the AD connection becomes visible in all NetApp accounts that are under the same subscription and same region. 

* The admin account you use must have the capability to create machine accounts in the organizational unit (OU) path that you will specify.  

* The admin account you use must have the capability to create machine accounts in the organizational unit (OU) path that you will specify. In some cases, `msDS-SupportedEncryptionTypes` write permission is required to set account attributes within AD.

* If you change the password of the Active Directory user account that is used in Azure NetApp Files, be sure to update the password configured in the [Active Directory Connections](#create-an-active-directory-connection). Otherwise, you will not be able to create new volumes, and your access to existing volumes might also be affected depending on the setup.  

* Proper ports must be open on the applicable Windows Active Directory (AD) server.  
    The required ports are as follows: 

    |     Service           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD Web Services    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    Echo Reply    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS name       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* The site topology for the targeted Active Directory Domain Services must adhere to the guidelines, in particular the Azure VNet where Azure NetApp Files is deployed.  

    The address space for the virtual network where Azure NetApp Files is deployed must be added to a new or existing Active Directory site (where a domain controller reachable by Azure NetApp Files is). 

* The specified DNS servers must be reachable from the [delegated subnet](./azure-netapp-files-delegate-subnet.md) of Azure NetApp Files.  

    See [Guidelines for Azure NetApp Files network planning](./azure-netapp-files-network-topologies.md) for supported network topologies.

    The Network Security Groups (NSGs) and firewalls must have appropriately configured rules to allow for Active Directory and DNS traffic requests. 

* The Azure NetApp Files delegated subnet must be able to reach all Active Directory Domain Services (ADDS) domain controllers in the domain, including all local and remote domain controllers. Otherwise, service interruption can occur.  

    If you have domain controllers that are unreachable by the Azure NetApp Files delegated subnet, you can specify an Active Directory site during creation of the Active Directory connection.  Azure NetApp Files needs to communicate only with domain controllers in the site where the Azure NetApp Files delegated subnet address space is.

    See [Designing the site topology](/windows-server/identity/ad-ds/plan/designing-the-site-topology) about AD sites and services. 

* Avoid configuring overlapping subnets in the AD machine. Even if the site name is defined in the Active Directory connections, overlapping subnets might result in the wrong site being discovered, thus affecting the service. It might also affect new volume creation or AD modification. 
    
* You can enable AES encryption for AD Authentication by checking the **AES Encryption** box in the [Join Active Directory](#create-an-active-directory-connection) window. Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). If you enable AES encryption, the user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.    

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers Microsoft Management Console (MMC):   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files supports [LDAP signing](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), which enables secure transmission of LDAP traffic between the Azure NetApp Files service and the targeted [Active Directory domain controllers](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). If you are following the guidance of Microsoft Advisory [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) for LDAP signing, then you should enable the LDAP signing feature in Azure NetApp Files by checking the **LDAP Signing** box in the [Join Active Directory](#create-an-active-directory-connection) window. 

    [LDAP channel binding](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) configuration alone has no effect on the Azure NetApp Files service. However, if you use both LDAP channel binding and secure LDAP (for example, LDAPS or `start_tls`), then the SMB volume creation will fail.

* Azure NetApp Files will attempt to add an A/PTR record in DNS for AD integrated DNS servers. Add a reverse lookup zone if one is missing under Reverse Lookup Zones on AD server. For non-AD integrated DNS, you should add a DNS A/PTR record to enable Azure NetApp Files to function by using a “friendly name".

* The following table describes the Time to Live (TTL) settings for the LDAP cache. You need to wait until the cache is refreshed before trying to access a file or directory through a client. Otherwise, an access or permission denied message appears on the client. 

    |     Error condition    |     Resolution    |
    |-|-|
    | Cache |  Default Timeout |
    | Group membership list  | 24-hour TTL  |
    | Unix groups  | 24-hour TTL, 1-minute negative TTL  |
    | Unix users  | 24-hour TTL, 1-minute negative TTL  |

    Caches have a specific timeout period called *Time to Live*. After the timeout period, entries age out so that stale entries do not linger. The *negative TTL* value is where a lookup that has failed resides to help avoid performance issues due to LDAP queries for objects that might not exist.
    
* Azure NetApp Files does not support the use of Active Directory Domain Services Read-Only Domain Controllers (RODC). To ensure that Azure NetApp Files does not try to use an RODC domain controller, configure the **AD Site** field of the Azure NetApp Files Active Directory connection with an Active Directory site that does not contain any RODC domain controllers.

## Decide which Domain Services to use 

Azure NetApp Files supports both [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) and Azure Active Directory Domain Services (AADDS) for AD connections.  Before you create an AD connection, you need to decide whether to use ADDS or AADDS.  

For more information, see [Compare self-managed Active Directory Domain Services, Azure Active Directory, and managed Azure Active Directory Domain Services](../active-directory-domain-services/compare-identity-solutions.md). 

### Active Directory Domain Services

You can use your preferred [Active Directory Sites and Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) scope for Azure NetApp Files. This option enables reads and writes to Active Directory Domain Services (ADDS) domain controllers that are [accessible by Azure NetApp Files](azure-netapp-files-network-topologies.md). It also prevents the service from communicating with domain controllers that are not in the specified Active Directory Sites and Services site. 

To find your site name when you use ADDS, you can contact the administrative group in your organization that is responsible for Active Directory Domain Services. The example below shows the Active Directory Sites and Services plugin where the site name is displayed: 

![Active Directory Sites and Services](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

When you configure an AD connection for Azure NetApp Files, you specify the site name in scope for the **AD Site Name** field.

### Azure Active Directory Domain Services 

For Azure Active Directory Domain Services (AADDS) configuration and guidance, see [Azure AD Domain Services documentation](../active-directory-domain-services/index.yml).

Additional AADDS considerations apply for Azure NetApp Files: 

* Ensure the VNet or subnet where AADDS is deployed is in the same Azure region as the Azure NetApp Files deployment.
* If you use another VNet in the region where Azure NetApp Files is deployed, you should create a peering between the two VNets.
* Azure NetApp Files supports `user` and `resource forest` types.
* For synchronization type, you can select `All` or `Scoped`.   
    If you select `Scoped`, ensure the correct Azure AD group is selected for accessing SMB shares.  If you are uncertain, you can use the `All` synchronization type.
* If you use AADDS with a dual-protocol volume, you must be in a custom OU in order to apply POSIX attributes. See [Manage LDAP POSIX Attributes](create-volumes-dual-protocol.md#manage-ldap-posix-attributes) for details.

When you create an Active Directory connection, note the following specifics for AADDS:

* You can find information for **Primary DNS**, **Secondary DNS**, and **AD DNS Domain Name** in the AADDS menu.  
For DNS servers, two IP addresses will be used for configuring the Active Directory connection. 
* The **organizational unit path** is `OU=AADDC Computers`.  
This setting is configured in the **Active Directory Connections** under **NetApp Account**:

  ![Organizational unit path](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Username** credentials can be any user that is a member of the Azure AD group **Azure AD DC Administrators**.


## Create an Active Directory connection

1. From your NetApp account, click **Active Directory connections**, then click **Join**.  

    Azure NetApp Files supports only one Active Directory connection within the same region and the same subscription. If Active Directory is already configured by another NetApp account in the same subscription and region, you cannot configure and join a different Active Directory from your NetApp account. However, you can enable the Shared AD feature to allow an Active Directory configuration to be shared by multiple NetApp accounts within the same subscription and the same region. See [Map multiple NetApp accounts in the same subscription and region to an AD connection](#shared_ad).

    ![Active Directory Connections](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. In the Join Active Directory window, provide the following information, based on the Domain Services you want to use:  

    For information specific to the Domain Services you use, see [Decide which Domain Services to use](#decide-which-domain-services-to-use). 

    * **Primary DNS**  
        This is the DNS that is required for the Active Directory domain join and SMB authentication operations. 
    * **Secondary DNS**   
        This is the secondary DNS server for ensuring redundant name services. 
    * **AD DNS Domain Name**  
        This is the domain name of your Active Directory Domain Services that you want to join.
    * **AD Site Name**  
        This is the site name that the domain controller discovery will be limited to. This should match the site name in Active Directory Sites and Services.
    * **SMB server (computer account) prefix**  
        This is the naming prefix for the machine account in Active Directory that Azure NetApp Files will use for creation of new accounts.

        For example, if the naming standard that your organization uses for file servers is NAS-01, NAS-02..., NAS-045, then you would enter "NAS" for the prefix. 

        The service will create additional machine accounts in Active Directory as needed.

        > [!IMPORTANT] 
        > Renaming the SMB server prefix after you create the Active Directory connection is disruptive. You will need to re-mount existing SMB shares after renaming the SMB server prefix.

    * **Organizational unit path**  
        This is the LDAP path for the organizational unit (OU) where SMB server machine accounts will be created. That is, OU=second level, OU=first level. 

        If you are using Azure NetApp Files with Azure Active Directory Domain Services, the organizational unit path is `OU=AADDC Computers` when you configure Active Directory for your NetApp account.

        ![Join Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES Encryption**   
        Select this checkbox if you want to enable AES encryption for AD authentication or if you require [encryption for SMB volumes](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume).   
        
        See [Requirements for Active Directory connections](#requirements-for-active-directory-connections) for requirements.  
  
        ![Active Directory AES encryption](../media/azure-netapp-files/active-directory-aes-encryption.png)

        The **AES Encryption** feature is currently in preview. If this is your first time using this feature, register the feature before using it: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature) `az feature register` and `az feature show` to register the feature and display the registration status. 

    * **LDAP Signing**   
        Select this checkbox to enable LDAP signing. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Active Directory LDAP signing](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        The **LDAP Signing** feature is currently in preview. If this is your first time using this feature, register the feature before using it: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature) `az feature register` and `az feature show` to register the feature and display the registration status. 

    * **LDAP over TLS**   
        See [Configure ADDS LDAP over TLS](configure-ldap-over-tls.md) for information about this option.

    * **LDAP Search Scope**, **User DN**, **Group DN**, and **Group Membership Filter**   
        See [Configure ADDS LDAP with extended groups for NFS volume access](configure-ldap-extended-groups.md#ldap-search-scope) for information about these options.

     * **Security privilege users**   <!-- SMB CA share feature -->   
        You can grant security privilege (`SeSecurityPrivilege`) to AD users or groups that require elevated privilege to access the Azure NetApp Files volumes. The specified AD users or groups will be allowed to perform certain actions on Azure NetApp Files SMB shares that require security privilege not assigned by default to domain users.   

        The following privilege applies when you use the **Security privilege users** setting:

        |  Privilege  |  Description  |
        |---|---|
        |  `SeSecurityPrivilege`  |  Manage log operations.  |

        For example, user accounts used for installing SQL Server in certain scenarios must (temporarily) be granted elevated security privilege. If you are using a non-administrator (domain) account to install SQL Server and the account does not have the security privilege assigned, you should add security privilege to the account.  

        > [!IMPORTANT]
        > Using the **Security privilege users** feature requires that you submit a waitlist request through the **[Azure NetApp Files SMB Continuous Availability Shares Public Preview waitlist submission page](https://aka.ms/anfsmbcasharespreviewsignup)**. Wait for an official confirmation email from the Azure NetApp Files team before using this feature.        
        > 
        > Using this feature is optional and supported only for SQL Server. The domain account used for installing SQL Server must already exist before you add it to the **Security privilege users** field. When you add the SQL Server installer's account to **Security privilege users**, the Azure NetApp Files service might validate the account by contacting the domain controller. The command might fail if it cannot contact the domain controller.  

        For more information about `SeSecurityPrivilege` and SQL Server, see [SQL Server installation fails if the Setup account doesn't have certain user rights](/troubleshoot/sql/install/installation-fails-if-remove-user-right).

        ![Screenshot showing the Security privilege users box of Active Directory connections window.](../media/azure-netapp-files/security-privilege-users.png) 

     * **Backup policy users**  
        You can grant additional security privileges to AD users or groups that require elevated backup privileges to access the Azure NetApp Files volumes. The specified AD user accounts or groups will have elevated NTFS permissions at the file or folder level. For example, you can specify a non-privileged service account used for backing up, restoring, or migrating data to an SMB file share in Azure NetApp Files.

        The following privileges apply when you use the **Backup policy users**  setting:

        |  Privilege  |  Description  |
        |---|---|
        |  `SeBackupPrivilege`  |  Back up files and directories, overriding any ACLs.  |
        |  `SeRestorePrivilege`  |  Restore files and directories, overriding any ACLs. <br> Set any valid user or group SID as the file owner.    |
        |  `SeChangeNotifyPrivilege`  |  Bypass traverse checking. <br> Users with this privilege are not required to have traverse (`x`) permissions to traverse folders or symlinks.  |

        ![Active Directory backup policy users](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        The **Backup policy users** feature is currently in preview. If this is your first time using this feature, register the feature before using it: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature) `az feature register` and `az feature show` to register the feature and display the registration status.  

    * **Administrators privilege users** 

        You can grant additional security privileges to AD users or groups that require even more elevated privileges to access the Azure NetApp Files volumes. The specified accounts will have further elevated permissions at the file or folder level.

        The following privileges apply when you use the **Administrators privilege users** setting:

        |  Privilege  |  Description  |
        |---|---|
        |  `SeBackupPrivilege`  |  Back up files and directories, overriding any ACLs. |  
        |  `SeRestorePrivilege`  |  Restore files and directories, overriding any ACLs. <br> Set any valid user or group SID as the file owner.  |  
        |  `SeChangeNotifyPrivilege`  |  Bypass traverse checking. <br> Users with this privilege are not required to have traverse (`x`) permissions to traverse folders or symlinks.  |  
        |  `SeTakeOwnershipPrivilege`  |  Take ownership of files or other objects. |  
        |  `SeSecurityPrivilege`  |  Manage log operations. |  
        |  `SeChangeNotifyPrivilege`  |  Bypass traverse checking. <br> Users with this privilege are not required to have traverse (`x`) permissions to traverse folders or symlinks.  |  

        ![Screenshot that shows the Administrators box of Active Directory connections window.](../media/azure-netapp-files/active-directory-administrators.png) 
        
        The **Administrators** feature is currently in preview. If this is your first time using this feature, register the feature before using it: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAdAdministrators
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAdAdministrators
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature) `az feature register` and `az feature show` to register the feature and display the registration status. 

    * Credentials, including your **username** and **password**

        ![Active Directory credentials](../media/azure-netapp-files/active-directory-credentials.png)

3. Click **Join**.  

    The Active Directory connection you created appears.

    ![Created Active Directory connections](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="shared_ad"></a>Map multiple NetApp accounts in the same subscription and region to an AD connection  

The Shared AD feature enables all NetApp accounts to share an Active Directory (AD) connection created by one of the NetApp accounts that belong to the same subscription and the same region. For example, using this feature, all NetApp accounts in the same subscription and region can use the common AD configuration to create an [SMB volume](azure-netapp-files-create-volumes-smb.md), a [NFSv4.1 Kerberos volume](configure-kerberos-encryption.md), or a [dual-protocol volume](create-volumes-dual-protocol.md). When you use this feature, the AD connection will be visible in all NetApp accounts that are under the same subscription and same region.   

This feature is currently in preview. You need to register the feature before using it for the first time. After registration, the feature is enabled and works in the background. No UI control is required. 

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```
You can also use [Azure CLI commands](/cli/azure/feature) `az feature register` and `az feature show` to register the feature and display the registration status. 
 
## Next steps  

* [Create an SMB volume](azure-netapp-files-create-volumes-smb.md)
* [Create a dual-protocol volume](create-volumes-dual-protocol.md)
* [Configure NFSv4.1 Kerberos encryption](configure-kerberos-encryption.md)
* [Install a new Active Directory forest using Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
* [Configure ADDS LDAP over TLS](configure-ldap-over-tls.md)
* [ADDS LDAP with extended groups for NFS volume access](configure-ldap-extended-groups.md)
