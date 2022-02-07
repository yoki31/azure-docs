---
title: 'CLI (v2) compute instance YAML schema'
titleSuffix: Azure Machine Learning
description: Reference documentation for the CLI (v2) compute instance YAML schema.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference

author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
---

# CLI (v2) compute instance YAML schema

The source JSON schema can be found at https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

[!INCLUDE [schema note](../../includes/machine-learning-preview-old-json-schema-note.md)]

## YAML syntax

| Key | Type | Description | Allowed values | Default value |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | The YAML schema. If you use the Azure Machine Learning VS Code extension to author the YAML file, including `$schema` at the top of your file enables you to invoke schema and resource completions. | | |
| `type` | string | **Required.** The type of compute. | `computeinstance` | |
| `name` | string | **Required.** Name of the compute. | | |
| `description` | string | Description of the compute. | | |
| `size` | string | The VM size to use for the compute instance. For more information, see [Supported VM series and sizes](concept-compute-target.md#supported-vm-series-and-sizes). Note that not all sizes are available in all regions. | For the list of supported sizes in a given region, please use the `az ml compute list-sizes` command.  | `Standard_DS3_v2` |
| `create_on_behalf_of` | object | Settings for creating the compute instance on behalf of another user. Please ensure that the assigned user has correct RBAC permissions. |  |  |
| `create_on_behalf_of.user_tenant_id` | string | The AAD Tenant ID of the assigned user. |  |  |
| `create_on_behalf_of.user_object_id` | string | The AAD Object ID of the assigned user. |  |  |
| `ssh_public_access_enabled` | boolean | Whether to enable public SSH access on the compute instance. | | `false` |
| `ssh_settings` | object | SSH settings for connecting to the compute instance. | | |
| `ssh_settings.ssh_key_value` | string | The SSH public key of the administrator user account. | | |
| `network_settings` | object | Network security settings. | | |
| `network_settings.vnet_name` | string | Name of the virtual network (VNet) when creating a new one or referencing an existing one. | | |
| `network_settings.subnet` | string | Either the name of the subnet when creating a new VNet or referencing an existing one, or the fully qualified resource ID of a subnet in an existing VNet. Do not specify `network_settings.vnet_name` if the subnet ID is specified. The subnet ID can refer to a VNet/subnet in another resource group. | | |

## Remarks

The `az ml compute` command can be used for managing Azure Machine Learning compute instances.

## YAML: minimal

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/instance-minimal.yml":::

## YAML: basic

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/instance-basic.yml":::

## Next steps

- [Install and use the CLI (v2)](how-to-configure-cli.md)
