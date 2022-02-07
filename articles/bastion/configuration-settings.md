---
title: 'About Azure Bastion configuration settings'
description: Learn about the available configuration settings for Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 01/14/2022
ms.author: cherylmc
ms.custom: ignite-fall-2021
---

# About Bastion configuration settings

The sections in this article discuss the resources and settings for Azure Bastion.

## <a name="skus"></a>SKUs

A SKU is also known as a Tier. Azure Bastion supports two SKU types: Basic and Standard. The SKU is configured in the Azure portal during the workflow when you configure Bastion. You can [upgrade a Basic SKU to a Standard SKU](#upgradesku).

* The **Basic SKU** provides base functionality, enabling Azure Bastion to manage RDP/SSH connectivity to virtual machines (VMs) without exposing public IP addresses on the target application VMs.
* The **Standard SKU** enables premium features that allow Azure Bastion to manage remote connectivity at a larger scale.

The following table shows features and corresponding SKUs.

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

Currently, you must use the Azure portal if you want to specify the Standard SKU. If you use the Azure CLI or Azure PowerShell to configure Bastion, the SKU can't be specified and defaults to the Basic SKU.

| Method | Value | Links |
| --- | --- | --- |
| Azure portal | Tier - Basic or <br>Standard | [Quickstart - Configure Bastion from VM settings](quickstart-host-portal.md)<br>[Tutorial - Configure Bastion](tutorial-create-host-portal.md) |
| Azure PowerShell | Basic only - no settings |[Configure Bastion - PowerShell](bastion-create-host-powershell.md) |
| Azure CLI |  Basic only - no settings | [Configure Bastion - CLI](create-host-cli.md) |

### <a name="upgradesku"></a>Upgrade a SKU

Azure Bastion supports upgrading from a Basic to a Standard SKU.

> [!NOTE]
> Downgrading from a Standard SKU to a Basic SKU is not supported. To downgrade, you must delete and recreate Azure Bastion.
>

You can configure this setting using the following method:

| Method | Value | Links |
| --- | --- | --- |
| Azure portal |Tier  | [Upgrade a SKU](upgrade-sku.md)|

## <a name="subnet"></a>Azure Bastion subnet

 >[!IMPORTANT]
 >For Azure Bastion resources deployed on or after November 2, 2021, the minimum AzureBastionSubnet size is /26 or larger (/25, /24, etc.). All Azure Bastion resources deployed in subnets of size /27 prior to this date are unaffected by this change and will continue to work, but we highly recommend increasing the size of any existing AzureBastionSubnet to /26 in case you choose to take advantage of [host scaling](./configure-host-scaling.md) in the future.
 >

Azure Bastion requires a dedicated subnet: **AzureBastionSubnet**. You must create this subnet in the same virtual network that you want to deploy Azure Bastion to. The subnet must have the following configuration:

* Subnet name must be *AzureBastionSubnet*.
* Subnet size must be /26 or larger (/25, /24 etc.).
* For host scaling, a /26 or larger subnet is recommended. Using a smaller subnet space limits the number of scale units. For more information, see the [Host scaling](#instance) section of this article.
* The subnet must be in the same VNet and resource group as the bastion host.
* The subnet cannot contain additional resources.

You can configure this setting using the following methods:

| Method | Value | Links |
| --- | --- |--- |
| Azure portal | Subnet  |[Quickstart - Configure Bastion from VM settings](quickstart-host-portal.md)<br>[Tutorial - Configure Bastion](tutorial-create-host-portal.md)|
| Azure PowerShell | -subnetName|[cmdlet](/powershell/module/az.network/new-azbastion#parameters) |
| Azure CLI |  --subnet-name | [command](/cli/azure/network/vnet#az_network_vnet_create) |

## <a name="public-ip"></a>Public IP address

Azure Bastion requires a Public IP address. The Public IP must have the following configuration:

* The Public IP address SKU must be **Standard**.
* The Public IP address assignment/allocation method must be **Static**.
* The Public IP address name is the resource name by which you want to refer to this public IP address.
* You can choose to use a public IP address that you already created, as long as it meets the criteria required by Azure Bastion and is not already in use.

You can configure this setting using the following methods:

| Method | Value | Links |
| --- | --- |--- |
| Azure portal | Public IP address |[Azure portal](https://portal.azure.com)|
| Azure PowerShell | -PublicIpAddress| [cmdlet](/powershell/module/az.network/new-azbastion#parameters)  |
| Azure CLI | --public-ip create |[command](/cli/azure/network/public-ip) |

## <a name="instance"></a>Instances and host scaling

An instance is an optimized Azure VM that is created when you configure Azure Bastion. It's fully managed by Azure and runs all of the processes needed for Azure Bastion. An instance is also referred to as a scale unit. You connect to client VMs via an Azure Bastion instance. When you configure Azure Bastion using the Basic SKU, two instances are created. If you use the Standard SKU, you can specify the number of instances. This is called **host scaling**.

Each instance can support 10 concurrent RDP connections and 50 concurrent SSH connections. The number of connections per instances depends on what actions you are taking when connected to the client VM. For example, if you are doing something data intensive, it creates a larger load for the instance to process. Once the concurrent sessions are exceeded, an additional scale unit (instance) is required.

Instances are created in the AzureBastionSubnet. To allow for host scaling, the AzureBastionSubnet should be /26 or larger. Using a smaller subnet limits the number of instances you can create. For more information about the AzureBastionSubnet, see the [subnets](#subnet) section in this article.

You can configure this setting using the following methods:

| Method | Value | Links |
| --- | --- | --- |
| Azure portal |Instance count  | [Azure portal steps](configure-host-scaling.md)|
| Azure PowerShell | ScaleUnit | [PowerShell steps](configure-host-scaling-powershell.md) |

## <a name="ports"></a>Custom ports

You can specify the port that you want to use to connect to your VMs. By default, the inbound ports used to connect are 3389 for RDP and 22 for SSH. If you configure a custom port value, you need to specify that value when you connect to the VM.

Custom port values are supported for the Standard SKU only. If your Bastion deployment uses the Basic SKU, you can easily [upgrade a Basic SKU to a Standard SKU](#upgradesku).

## Next steps

For frequently asked questions, see the [Azure Bastion FAQ](bastion-faq.md).
