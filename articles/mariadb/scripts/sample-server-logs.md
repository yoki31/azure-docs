---
title: CLI script - Download slow query logs - Azure Database for MariaDB
description: This sample Azure CLI script shows how to enable and download the slow query logs of an Azure Database for MariaDB server.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 01/26/2022 
---

# Enable and download server slow query logs of an Azure Database for MariaDB server using Azure CLI

This sample CLI script enables and downloads the slow query logs of a single Azure Database for MariaDB server.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## Sample script

[!INCLUDE [cli-launch-cloud-shell-sign-in.md](../../../includes/cli-launch-cloud-shell-sign-in.md)]

### Run the script

:::code language="azurecli" source="~/azure_cli_scripts/mariadb/server-logs/server-logs.sh" range="4-51":::

## Clean up resources

[!INCLUDE [cli-clean-up-resources.md](../../../includes/cli-clean-up-resources.md)]

```azurecli
az group delete --name $resourceGroup
```

## Sample reference

This script uses the commands outlined in the following table:

| **Command** | **Notes** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Creates a resource group in which all resources are stored. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Creates a MariaDB server that hosts the databases. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list) | List the configuration values for a server. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) | Update the configuration of a server. |
| [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list) | List log files for a server. |
| [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download) | Download log files. |
| [az group delete](/cli/azure/group#az_group_delete) | Deletes a resource group including all nested resources. |

## Next steps

- Read more information on the Azure CLI: [Azure CLI documentation](/cli/azure).
- Try additional scripts: [Azure CLI samples for Azure Database for MariaDB](../sample-scripts-azure-cli.md)
