---
title: CLI script - Create server - Azure Database for MariaDB
description: This sample CLI script creates an Azure Database for MariaDB server and configures a server-level firewall rule.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 01/26/2022 
---

# Create a MariaDB server and configure a firewall rule using the Azure CLI

This sample CLI script creates an Azure Database for MariaDB server and configures a server-level firewall rule. Once the script runs successfully, the MariaDB server is accessible by all Azure services and the configured IP address.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## Sample script

[!INCLUDE [cli-launch-cloud-shell-sign-in.md](../../../includes/cli-launch-cloud-shell-sign-in.md)]

### Run the script

:::code language="azurecli" source="~/azure_cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh" range="4-31":::

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
| [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create) | Creates a firewall rule to allow access to the server and databases under it from the entered IP address range. |
| [az group delete](/cli/azure/group#az_group_delete) | Deletes a resource group including all nested resources. |

## Next steps

- Read more information on the Azure CLI: [Azure CLI documentation](/cli/azure).
- Try additional scripts: [Azure CLI samples for Azure Database for MariaDB](../sample-scripts-azure-cli.md)
