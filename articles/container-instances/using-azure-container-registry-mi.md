---
title: Deploy container image from Azure Container Registry using a managed identity
description: Learn how to deploy containers in Azure Container Instances by pulling container images from an Azure container registry using a managed identity.
services: container-instances
ms.topic: article
ms.date: 11/11/2021
ms.custom: mvc, devx-track-azurecli
---

# Deploy to Azure Container Instances from Azure Container Registry using a managed identity

[Azure Container Registry][acr-overview] (ACR) is an Azure-based, managed container registry service used to store private Docker container images. This article describes how to pull container images stored in an Azure container registry when deploying to container groups with Azure Container Instances. One way to configure registry access is to create an Azure Active Directory managed identity.

## Prerequisites

**Azure container registry**: You need a premium SKU Azure container registry with at least one image. If you need to create a registry, see [Create a container registry using the Azure CLI][acr-get-started]. Be sure to take note of the registry's `id` and `loginServer`

**Azure CLI**: The command-line examples in this article use the [Azure CLI](/cli/azure/) and are formatted for the Bash shell. You can [install the Azure CLI](/cli/azure/install-azure-cli) locally, or use the [Azure Cloud Shell][cloud-shell-bash].

## Limitations

> [!IMPORTANT]
> Managed identity-authenticated container image pulls from ACR are not supported in Canada Central, South India, and West Central US at this time.  

* Virtual Network injected container groups don't support managed identity authentication image pulls with ACR.

* Windows containers don't support managed identity-authenticated image pulls with ACR.

* Container groups don't support pulling images from an Azure Container Registry using [private DNS zones][private-dns-zones].

## Configure registry authentication

Your container registry must have Trusted Services enabled. To find instructions on how to enable trusted services, see [Allow trusted services to securely access a network-restricted container registry][allow-access-trusted-services].

## Create an identity

Create an identity in your subscription using the [az identity create][az-identity-create] command. You can use the same resource group you used previously to create the container registry, or a different one.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

To configure the identity in the following steps, use the [az identity show][az-identity-show] command to store the identity's resource ID and service principal ID in variables.

In order to properly configure the identity in future steps, use [az identity show][az-identity-show] to obtain and store the identity's resource ID and service principal ID in variables.

```azurecli-interactive
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

You'll need the identity's resource ID to sign in to the CLI from your virtual machine. To show the value:

```bash
echo $userID
```

The resource ID is of the form:

```bash
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

You'll also need the service principal ID to grant the managed identity access to your container registry. To show the value:

```bash
echo $spID
```

The service principal ID is of the form:

```bash
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx
```

## Grant the identity a role assignment

In order for your identity to access your container registry, you must grant it a role assignment. Use to following command to grant the `acrpull` role to the identity you've just created, making sure to provide your registry's ID and the service principal we obtained earlier:

```azurecli-interactive
az role assignment create --assignee $spID --scope <registry-id> --role acrpull
```

## Deploy using an Azure Resource Manager (ARM) template

Start by copying the following JSON into a new file named `azuredeploy.json`. In Azure Cloud Shell, you can use Visual Studio Code to create the file in your working directory:

```bash
code azuredeploy.json
```

You can specify the properties of your Azure container registry in an ARM template by including the `imageRegistryCredentials` property in the container group definition. For example, you can specify the registry credentials directly:

> [!NOTE]
> This is not a comprehensive ARM template, but rather an example of what the `resources` section of a complete template would look like.

```JSON
{
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2021-09-01",
    "name": "myContainerGroup",
    "location": "norwayeast",
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId": {}
        }
    },
    "properties": {
      "containers": [
        {
          "name": "mycontainer",
          "properties": {
            "image": "myacr.azurecr.io/hello-world:latest",
            "ports": [
              {
                "port": 80,
                "protocol": "TCP"
              }
            ],
            "resources": {
              "requests": {
                "cpu": 1,
                "memoryInGB": 1
              }
            }
        }
        }
      ],
      "imageRegistryCredentials": [
        {
            "server":"myacr.azurecr.io",
            "identity":"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId"
        }
      ],
      "ipAddress": {
        "ports": [
          {
            "port": 80,
            "protocol": "TCP"
          }
        ],
        "type": "public"
      },
      "osType": "Linux"
    }
  }
```

### Deploy the template

Deploy your Resource Manager template with the following command:

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
```

## Deploy using the Azure CLI

To deploy a container group using managed identity to authenticate image pulls via the Azure CLI, use the following command, making sure that your `<dns-label>` is globally unique:

```azurecli-interactive
az container create --name my-containergroup --resource-group myResourceGroup --image <loginServer>/hello-world:v1 --acr-identity $userID --assign-identity $userID --ports 80 --dns-name-label <dns-label>
```

## Clean up resources

To remove all resources from your Azure subscription, delete the resource group:

```azurecli-interactive
az group delete --name myResourceGroup
```

## Next Steps

* [Learn how to deploy to Azure Container Instances from Azure Container Registry using a service principal][use-service-principal]

<!-- Links Internal -->

[use-service-principal]: ./container-instances-using-azure-container-registry.md
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-identity-create]: /cli/azure/identity#az_identity_create
[acr-overview]: ../container-registry/container-registry-intro.md
[acr-get-started]: ../container-registry/container-registry-get-started-azure-cli.md
[private-dns-zones]: ../dns/private-dns-privatednszone.md
[allow-access-trusted-services]: ../container-registry/allow-access-trusted-services.md

<!-- Links External -->
[cloud-shell-bash]: https://shell.azure.com/bash