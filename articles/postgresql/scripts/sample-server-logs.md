---
title: Azure CLI script - Download server logs in Azure Database for PostgreSQL
description: This sample Azure CLI script shows how to enable and download the server logs of an Azure Database for PostgreSQL server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 01/26/2022 
---

# Enable and download server slow query logs of an Azure Database for PostgreSQL server using Azure CLI

This sample CLI script enables and downloads the slow query logs of a single Azure Database for PostgreSQL server.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## Sample script

[!INCLUDE [cli-launch-cloud-shell-sign-in.md](../../../includes/cli-launch-cloud-shell-sign-in.md)]

### Run the script

:::code language="azurecli" source="~/azure_cli_scripts/postgresql/server-logs/server-logs.sh" range="4-51":::

## Clean up deployment

[!INCLUDE [cli-clean-up-resources.md](../../../includes/cli-clean-up-resources.md)]

```azurecli
az group delete --name $resourceGroup
```

## Sample reference

This script uses the commands outlined in the following table:

| **Command** | **Notes** |
|---|---|
| [az group create](/cli/azure/group) | Creates a resource group in which all resources are stored. |
| [az postgres server create](/cli/azure/postgres/server) | Creates a PostgreSQL server that hosts the databases. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | List the configuration values for a server. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Update the configuration of a server. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | List log files for a server. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | Download log files. |
| [az group delete](/cli/azure/group) | Deletes a resource group including all nested resources. |

## Next steps

- Read more information on the Azure CLI: [Azure CLI documentation](/cli/azure).
- Try additional scripts: [Azure CLI samples for Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- [Configure and access server logs in the Azure portal](../howto-configure-server-logs-in-portal.md)
