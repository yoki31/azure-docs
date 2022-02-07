---
title: Configure SAP system parameters for automation
description: Define the SAP system properties for the SAP deployment automation framework on Azure using a parameters JSON file.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
---

# Configure SAP system parameters 

Configuration for the [SAP deployment automation framework on Azure](automation-deployment-framework.md)] happens through parameters files. You provide information about your SAP system properties in a tfvars file, which the automation framework uses for deployment. 

The configuration of the SAP system is done via a Terraform tfvars variable file.

## Terraform Parameters

The table below contains the Terraform parameters, these parameters need to be entered manually if not using the deployment scripts.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                  | Description                                                                                                      | Type       |
> | ------------------------- | ---------------------------------------------------------------------------------------------------------------- | ---------- | 
> | `tfstate_resource_id`     | Azure resource identifier for the Storage account in the SAP Library that will contain the Terraform state files | Required * |
> | `deployer_tfstate_key`    | The name of the state file for the Deployer                                                                      | Required * |
> | `landscaper_tfstate_key`  | The name of the state file for the workload zone                                                                 | Required * |

\* = required for manual deployments
## Generic Parameters

The table below contains the parameters that define the resource group and the resource naming.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                | Description                           | Type       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `environment`           | A five-character identifier for the workload zone. For example, `PROD` for a production environment and `NP` for a non-production environment. | Mandatory |
> | `location`              | The Azure region in which to deploy.     | Required   | 
> | `resource_group_name`   | Name of the resource group to be created | Optional   |
> | `resource_group_arm_id` | Azure resource identifier for an existing resource group | Optional   | 
> | `custom_prefix`         | Specifies the custom prefix used in the resource naming  | Optional   | 
> | `use_prefix`            | Controls if the resource naming includes the prefix, DEV-WEEU-SAP01-X00_xxxx | Optional   | 

## Network Parameters

If the subnets are not deployed using the workload zone deployment, they can be added in the system's tfvars file.

The automation framework supports both creating the virtual network and the subnets for new environment deployments (Green field) or using an existing virtual network and existing subnets for existing environment deployments (Brown field) or a combination of for new environment deployments  and for existing environment deployments.
 - For the green field scenario, the virtual network address space and the subnet address prefixes must be specified 
 - For the brown field scenario, the Azure resource identifier for the virtual network and the subnets must be specified

Ensure that the virtual network address space is large enough to host all the resources

The table below contains the networking parameters.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                         | Description                                                          | Type      | Notes  |
> | -------------------------------- | -------------------------------------------------------------------- | --------- | ------ |
> | `network_logical_name`           | The logical name of the network                                      | Required  |        |       
> | `network_address_space`          | The address range for the virtual network                            | Mandatory | For new environment deployments   |
> | `admin_subnet_name`              | The name of the 'admin' subnet                                       | Optional  |         |
> | `admin_subnet_address_prefix`    | The address range for the 'admin' subnet                             | Mandatory | For new environment deployments  |
> | `admin_subnet_arm_id`  	         | The Azure resource identifier for the 'admin' subnet                 | Mandatory | For existing environment deployments |
> | `admin_subnet_nsg_name`          | The name of the 'admin' Network Security Group name                  | Optional	|         |
> | `admin_subnet_nsg_arm_id`        | The Azure resource identifier for the 'admin' Network Security Group | Mandatory | For existing environment deployments |
> | `db_subnet_name`                 | The name of the 'db' subnet                                          | Optional  |         |
> | `db_subnet_address_prefix`       | The address range for the 'db' subnet                                | Mandatory | For new environment deployments  |
> | `db_subnet_arm_id`	             | The Azure resource identifier for the 'db' subnet                    | Mandatory | For existing environment deployments |
> | `db_subnet_nsg_name`             | The name of the 'db' Network Security Group name                     | Optional	|          |
> | `db_subnet_nsg_arm_id`           | The Azure resource identifier for the 'db' Network Security Group    | Mandatory | For existing environment deployments |
> | `app_subnet_name`                | The name of the 'app' subnet                                         | Optional  |          |
> | `app_subnet_address_prefix`      | The address range for the 'app' subnet                               | Mandatory | For new environment deployments  |
> | `app_subnet_arm_id`	             | The Azure resource identifier for the 'app' subnet                   | Mandatory | For existing environment deployments |
> | `app_subnet_nsg_name`            | The name of the 'app' Network Security Group name                    | Optional	|          |
> | `app_subnet_nsg_arm_id`          | The Azure resource identifier for the 'app' Network Security Group   | Mandatory | For existing environment deployments |
> | `web_subnet_name`                | The name of the 'web' subnet                                         | Optional  |          |
> | `web_subnet_address_prefix`      | The address range for the 'web' subnet                               | Mandatory | For new environment deployments  |
> | `web_subnet_arm_id`	             | The Azure resource identifier for the 'web' subnet                   | Mandatory | For existing environment deployments |
> | `web_subnet_nsg_name`            | The name of the 'web' Network Security Group name                    | Optional	|          |
> | `web_subnet_nsg_arm_id`          | The Azure resource identifier for the 'web' Network Security Group   | Mandatory | For existing environment deployments |

\* = Required for for existing environment deployments deployments

### Database Tier Parameters

The database tier defines the infrastructure for the database tier, supported database back ends are:

- `HANA`
- `DB2`
- `ORACLE`
- `ORACLE-ASM`
- `ASE`
- `SQLSERVER`
- `NONE` (in this case no database tier is deployed)


> [!div class="mx-tdCol2BreakAll "]
> | Variable                          | Description                                                                              | Type         | Notes              |
> | --------------------------------  | -----------------------------------------------------------------------------------------| -----------  | ------------------ |
> | `database_platform`               | Defines the database backend                                                             | Required     |                    |
> | `database_high_availability`      | Defines if the database tier is deployed highly available                                | Optional     | See [High availability configuration](automation-configure-system.md#high-availability-configuration) |
> | `database_server_count`           | Defines the number of database servers                                                   | Optional     | Default value is 1 |
> | `database_vm_names`               | Defines the database server virtual machine names if the default naming is not acceptable | Optional    |                    |
> | `database_size`                   | Defines the database sizing information                                                  | Required     | See [Custom Sizing](automation-configure-extra-disks.md) |
> | `database_sid`                    | Defines the database SID                                                                 | Required     |                    |
> | `db_disk_sizes_filename`          | Defines the custom database sizing                                                       | Optional     | See [Custom Sizing](automation-configure-extra-disks.md) |
> | `database_sid`                    | Defines the database SID                                                                 | Required     |                    |
> | `database_vm_image`	              | Defines the Virtual machine image to use, see below                                      | Optional	    |                    |
> | `database_vm_use_DHCP`            | Controls if Azure subnet provided IP addresses should be used (dynamic) true             | Optional     |                    |
> | `database_vm_db_nic_ips`          | Defines the static IP addresses for the database servers (database subnet)               | Optional     |                    |
> | `database_vm_admin_nic_ips`       | Defines the static IP addresses for the database servers (admin subnet)                  | Optional     |                    |
> | `database_vm_authentication_type` | Defines the authentication type for the database virtual machines (key/password)         | Optional	    |                    | 
> | `database_vm_zones`               | Defines the Availability Zones                                                           | Optional	    |                    |
> | `database_vm_avset_arm_ids`       | Defines the existing availability sets Azure resource IDs                                | Optional	    | Primarily used together with ANF pinning| 
> | `database_no_avset`               | Controls if the database virtual machines are deployed without availability sets         | Optional	    | default is false   |
> | `database_no_ppg`                 | Controls if the database servers will not be placed in a proximity placement group       | Optional	    | default is false   |
> | `hana_dual_nics`                 | Controls if the HANA database servers will have dual network interfaces  | Optional	    | default is true   |

The Virtual Machine and the operating system image is defined using the following structure: 

```python 
{
  os_type="linux"
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="8.2.2021040902"
}
```

### Common Application Tier Parameters

The application tier defines the infrastructure for the application tier, which can consist of application servers, central services servers and web dispatch servers


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Description                                                                 | Type       | Notes  |
> | ---------------------------------- | --------------------------------------------------------------------------- | -----------| ------ |
> | `enable_app_tier_deployment`	     | Defines if the application tier is deployed                                 | Optional	  |        |
> | `sid`	                             |	Defines the SAP application SID                                            | Required	  |        | 
> | `app_disk_sizes_filename`	         | Defines the custom disk size file for the application tier servers          | Optional 	| See [Custom Sizing](automation-configure-extra-disks.md) |
> | `app_tier_authentication_type`     | Defines the authentication type for the application tier virtual machine(s) | Optional	  |       |
> | `app_tier_use_DHCP`	               | Controls if Azure subnet provided IP addresses should be used (dynamic)     | Optional	  |       |
> | `app_tier_dual_nics`	             | Defines if the application tier server will have two network interfaces     | Optional	  |       |
> | `app_tier_vm_sizing`	             | Lookup value defining the VM SKU and the disk layout for tha application tier servers | Optional |

### Application Server Parameters


> [!div class="mx-tdCol2BreakAll "]
> | Variable                               | Description                                                                  | Type       | Notes  |
> | -------------------------------------- | ---------------------------------------------------------------------------- | -----------| ------ |
> | `application_server_count`	           | Defines the number of application servers                                    | Required	 | |
> | `application_server_sku`	             | Defines the Virtual machine SKU to use                                       | Optional   | | 
> | `application_server_image`	           | Defines the Virtual machine image to use                                     | Required   | | 
> | `application_server_zones`	           | Defines the availability zones to which the application servers are deployed | Optional   | | 
> | `application_server_app_nic_ips[]`     | List of IP addresses for the application server (app subnet)                 | Optional   | Ignored if `app_tier_use_DHCP` is used |
> | `application_server_app_admin_nic_ips` | List of IP addresses for the application server (admin subnet)               | Optional   | Ignored if `app_tier_use_DHCP` is used |
> | `application_server_no_ppg`            | Controls application server proximity placement group                        | Optional   | |
> | `application_server_no_avset`	         | Controls application server availability set placement                       | Optional   | |
> | `application_server_tags`	             | Defines a list of tags to be applied to the application servers              | Optional   | |

### SAP Central Services Parameters


> [!div class="mx-tdCol2BreakAll "]
> | Variable                               | Description                                                          | Type      | Notes  |
> | -------------------------------------- | -------------------------------------------------------------------- | ----------| ------ |
> | `scs_high_availability`	               | Defines if the Central Services is highly available                  | Optional	| See [High availability configuration](automation-configure-system.md#high-availability-configuration) |
> | `scs_instance_number`	                 | The instance number of SCS                                           | Optional  |        |
> | `ers_instance_number`	                 | The instance number of ERS                                           | Optional	|        |
> | `scs_server_count`	                   | Defines the number of scs servers                                    | Required	|        |
> | `scs_server_sku`	                     | Defines the Virtual machine SKU to use                               | Optional  |        | 
> | `scs_server_image`	                   | Defines the Virtual machine image to use                             | Required  |        | 
> | `scs_server_zones`	                   | Defines the availability zones to which the scs servers are deployed | Optional  |        | 
> | `scs_server_app_nic_ips[]`             | List of IP addresses for the scs server (app subnet)                 | Optional  | Ignored if `app_tier_use_DHCP` is used |
> | `scs_server_app_admin_nic_ips`         | List of IP addresses for the scs server (admin subnet)               | Optional  | Ignored if `app_tier_use_DHCP` is used |
> | `scs_server_no_ppg`                    | Controls scs server proximity placement group                        | Optional  |         |
> | `scs_server_no_avset`	                 | Controls scs server availability set placement                       | Optional  |         |
> | `scs_server_tags`	                     | Defines a list of tags to be applied to the scs servers              | Optional  |         |


### Web Dispatcher Parameters


> [!div class="mx-tdCol2BreakAll "]
> | Variable                                | Description                                                              | Type      | Notes  |
> | --------------------------------------- | ------------------------------------------------------------------------ | --------- | ------ |
> | `webdispatcher_server_count`	          | Defines the number of web dispatcher servers                             | Required  |        |
> | `webdispatcher_server_sku`	            | Defines the Virtual machine SKU to use                                   | Optional  |        | 
> | `webdispatcher_server_image`	          | Defines the Virtual machine image to use                                 | Optional  |        | 
> | `webdispatcher_server_zones`	          | Defines the availability zones to which the web dispatchers are deployed | Optional  |        |
> | `webdispatcher_server_app_nic_ips[]`    | List of IP addresses for the web dispatcher server (app subnet)          | Optional  | Ignored if `app_tier_use_DHCP` is used |
> | `webdispatcher_server_app_admin_nic_ips`| List of IP addresses for the web dispatcher server (admin subnet)        | Optional  | Ignored if `app_tier_use_DHCP` is used |
> | `webdispatcher_server_no_ppg`           | Controls web proximity placement group placement                         | Optional  | |
> | `webdispatcher_server_no_avset`	        | Defines web dispatcher availability set placement                        | Optional  | |
> | `webdispatcher_server_tags`	            | Defines a list of tags to be applied to the web dispatcher servers       | Optional  | |

### Anchor Virtual Machine Parameters

The SAP deployment automation framework supports having an Anchor Virtual Machine. The anchor Virtual machine will be the first virtual machine to be deployed and is used to anchor the proximity placement group.

The table below contains the parameters related to the anchor virtual machine. 


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Description                                                                       | Type        | 
> | ---------------------------------- | --------------------------------------------------------------------------------- | ----------- | 
> | `deploy_anchor_vm`                 | Defines if the anchor Virtual Machine is used                                     | Optional	   | 
> | `anchor_vm_sku`                    | Defines the VM SKU to use. For example, Standard_D4s_v3.              | Optional    | 
> | `anchor_vm_image`	                 | Defines the VM image to use. See the following code sample.                              | Optional	   | 
> | `anchor_vm_use_DHCP`               | Controls whether to use dynamic IP addresses provided by Azure subnet.           | Optional    | 
> | `anchor_vm_accelerated_networking` | Defines if the Anchor VM is configured to use accelerated networking | Optional    | 
> | `anchor_vm_authentication_type`    | Defines the authentication type for the anchor VM key and password       | Optional	   | 

The Virtual Machine and the operating system image is defined using the following structure: 
```python 
{ 
os_type=""
source_image_id=""
publisher="Canonical"
offer="0001-com-ubuntu-server-focal"
sku="20_04-lts"
version="latest"
}
```

### Authentication Parameters

By default the SAP System deployment uses the credentials from the SAP Workload zone. If the SAP system needs unique credentials, you can provide them using these parameters.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Description                          | Type        | 
> | ---------------------------------- | -------------------------------------| ----------- | 
> | `automation_username`              | Administrator account name           | Optional	|
> | `automation_password`              | Administrator password               | Optional    |
> | `automation_path_to_public_key`    | Path to existing public key          | Optional    |
> | `automation_path_to_private_key`   | Path to existing private key         | Optional    |


## Other Parameters


> [!div class="mx-tdCol2BreakAll "]
> | Variable                                       | Description                           | Type        | 
> | ---------------------------------------------- | ------------------------------------- | ----------- | 
> | `resource_offset`                              | Provides and offset for resource naming. The offset number for resource naming when creating multiple resources. The default value is 0, which creates a naming pattern of disk0, disk1, and so on. An offset of 1 creates a naming pattern of disk1, disk2, and so on. | Optional    |
> | `disk_encryption_set_id`                       | The disk encryption key to use for encrypting managed disks using customer provided keys | Optional   |
> | `use_loadbalancers_for_standalone_deployments` | Controls if load balancers are deployed for standalone installations | Optional |
> | `license_type`                                 | Specifies the license type for the virtual machines. | Possible values are `RHEL_BYOS` and `SLES_BYOS`. For Windows the possible values are `None`, `Windows_Client` and `Windows_Server`. |
> | `use_zonal_markers`                            | Specifies if zonal Virtual Machines will include a zonal identifier. 'xooscs_z1_00l###' vs  'xooscs00l###'| Default value is true. |


## NFS Support

> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Description                                                             | Type        |
> | ---------------------------------- | ----------------------------------------------------------------------- | ----------- |
> | `NFS_Provider`                     | Defines what NFS backend to use, the options are 'AFS' for Azure Files NFS or 'ANF' for Azure NetApp files.  | 
> | `sapmnt_volume_size`               | Defines the size (in GB) for the 'sapmnt' volume                        | Optional    |

### Azure Files NFS Support


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Description                                                            | Type         |
> | ---------------------------------- | ----------------------------------------------------------------------- | ----------- |
> | `azure_files_storage_account_id`   | If provided the Azure resource ID of the storage account for Azure Files | Optional    |  


## High availability configuration

The high availability configuration for the database tier and the SCS tier is configured using the `database_high_availability` and `scs_high_availability`	flags. 

High availability configurations use Pacemaker with Azure fencing agents. The fencing agents should be configured to use a unique service principal with permissions to stop and start virtual machines. For more information see [Create Fencing Agent](high-availability-guide-suse-pacemaker.md#create-an-azure-fence-agent-stonith-device)

```azurecli-interactive
az ad sp create-for-rbac --role="Linux Fence Agent Role" --scopes="/subscriptions/<subscriptionID>" --name="<prefix>-Fencing-Agent"
```

Replace `<prefix>` with the name prefix of your environment, such as `DEV-WEEU-SAP01` and `<subscriptionID>` with the workload zone subscription ID.
  
> [!IMPORTANT]
> The name of the Fencing Agent Service Principal must be unique in the tenant. The script assumes that a role 'Linux Fence Agent Role' has already been created
>
> Record the values from the Fencing Agent SPN.
   > - appId
   > - password
   > - tenant

The fencing agent details must be stored in the workload zone key vault using a predefined naming convention. Replace `<prefix>` with the name prefix of your environment, such as `DEV-WEEU-SAP01`, `<workload_kv_name>` with the name of the key vault from the workload zone resource group and for the other values use the values recorded from the previous step and run the script.


```azurecli

    ```azurecli-interactive
    az keyvault secret set --name "<prefix>-fencing-spn-id" --vault-name "<workload_kv_name>" --value "<appId>";
    az keyvault secret set --name "<prefix>-fencing-spn-pwd" --vault-name "<workload_kv_name>" --value "<password>";
    az keyvault secret set --name "<prefix>-fencing-spn-tenant" --vault-name "<workload_kv_name>" --value "<tenant>";
    ```
```

## Next steps

> [!div class="nextstepaction"]
> [Deploy SAP System](automation-deploy-system.md)

