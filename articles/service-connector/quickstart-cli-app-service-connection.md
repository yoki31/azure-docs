---
title: Quickstart - Create a service connection in App Service with the Azure CLI
description: Quickstart showing how to create a service connection in App Service with the Azure CLI
author: shizn
ms.author: xshi
ms.service: service-connector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021, mode-api
---

# Quickstart: Create a service connection in App Service with the Azure CLI

The [Azure CLI](/cli/azure) is a set of commands used to create and manage Azure resources. The Azure CLI is available across Azure services and is designed to get you working quickly with Azure, with an emphasis on automation. This quickstart shows you the options to create Azure Web PubSub instance with the Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- This quickstart requires version 2.30.0 or higher of the Azure CLI. If using Azure Cloud Shell, the latest version is already installed.

- This quickstart assumes that you already have at least an App Service running on Azure. If you don't have an App Service, [create one](../app-service/quickstart-dotnetcore.md).

## View supported target service types

Use the Azure CLI [az webapp connection](/cli/azure/webapp/connection) command create and manage service connections to App Service. 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az webapp connection list-support-types --output table
```

## Create a service connection

#### [Using Access Key](#tab/Using-access-key)

Use the Azure CLI [az webapp connection](/cli/azure/webapp/connection) command to create a service connection to a blob storage with access key, providing the following information:

- **Source compute service resource group name:** The resource group name of the App Service.
- **App Service name:** The name of your App Service that connects to the target service.
- **Target service resource group name:** The resource group name of the blob storage.
- **Storage account name:** The account name of your blob storage.

```azurecli-interactive
az webapp connection create storage-blob --secret
```

> [!NOTE]
> If you don't have a blob storage, you can run `az webapp connection create storage-blob --new --secret` to provision a new one and directly get connected to your app service.

#### [Using Managed Identity](#tab/Using-Managed-Identity)

> [!IMPORTANT]
> Using Managed Identity requires you have the permission to [Azure AD role assignment](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). If you don't have the permission, your connection creation would fail. You can ask your subscription owner for the permission or using access key to create the connection.

Use the Azure CLI [az webapp connection](/cli/azure/webapp/connection) command to create a service connection to a blob storage with System-assigned Managed Identity, providing the following information:

- **Source compute service resource group name:** The resource group name of the App Service.
- **App Service name:** The name of your App Service that connects to the target service.
- **Target service resource group name:** The resource group name of the blob storage.
- **Storage account name:** The account name of your blob storage.

```azurecli-interactive
az webapp connection create storage-blob --system-identity
```

> [!NOTE]
> If you don't have a blob storage, you can run `az webapp connection create storage-blob --new --system-identity` to provision a new one and directly get connected to your app service.

---

## View connections

Use the Azure CLI [az webapp connection](/cli/azure/webapp/connection) command to list connection to your App Service, providing the following information:

- **Source compute service resource group name:** The resource group name of the App Service.
- **App Service name:** The name of your App Service that connects to the target service.

```azurecli-interactive
az webapp connection list -g "<your-app-service-resource-group>" --webapp "<your-app-service-name>"
```

## Next steps

Follow the tutorials listed below to start building your own application with Service Connector.

> [!div class="nextstepaction"]
> [Tutorial: WebApp + Storage with Azure CLI](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [Tutorial: WebApp + PostgreSQL with Azure CLI](./tutorial-django-webapp-postgres-cli.md)
