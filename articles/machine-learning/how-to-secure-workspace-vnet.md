---
title: Secure an Azure Machine Learning workspace with virtual networks
titleSuffix: Azure Machine Learning
description: Use an isolated Azure Virtual Network to secure your Azure Machine Learning workspace and associated resources.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 01/11/2022
ms.topic: how-to
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, security

---

# Secure an Azure Machine Learning workspace with virtual networks

In this article, you learn how to secure an Azure Machine Learning workspace and its associated resources in a virtual network.

> [!TIP]
> This article is part of a series on securing an Azure Machine Learning workflow. See the other articles in this series:
>
> * [Virtual network overview](how-to-network-security-overview.md)
> * [Secure the training environment](how-to-secure-training-vnet.md)
> * [Secure the inference environment](how-to-secure-inferencing-vnet.md)
> * [Enable studio functionality](how-to-enable-studio-virtual-network.md)
> * [Use custom DNS](how-to-custom-dns.md)
> * [Use a firewall](how-to-access-azureml-behind-firewall.md)
>
> For a tutorial on creating a secure workspace, see [Tutorial: Create a secure workspace](tutorial-create-secure-workspace.md) or [Tutorial: Create a secure workspace using a template](tutorial-create-secure-workspace-template.md).

In this article you learn how to enable the following workspaces resources in a virtual network:
> [!div class="checklist"]
> - Azure Machine Learning workspace
> - Azure Storage accounts
> - Azure Machine Learning datastores and datasets
> - Azure Key Vault
> - Azure Container Registry

## Prerequisites

+ Read the [Network security overview](how-to-network-security-overview.md) article to understand common virtual network scenarios and overall virtual network architecture.

+ Read the [Azure Machine Learning best practices for enterprise security](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security) article to learn about best practices.

+ An existing virtual network and subnet to use with your compute resources.

    > [!TIP]
    > If you plan on using Azure Container Instances in the virtual network (to deploy models), then the workspace and virtual network must be in the same resource group. Otherwise, they can be in different groups.

+ To deploy resources into a virtual network or subnet, your user account must have permissions to the following actions in Azure role-based access control (Azure RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" on the virtual network resource.
    - "Microsoft.Network/virtualNetworks/subnets/join/action" on the subnet resource.

    For more information on Azure RBAC with networking, see the [Networking built-in roles](../role-based-access-control/built-in-roles.md#networking)

### Azure Container Registry

* Your Azure Container Registry must be Premium version. For more information on upgrading, see [Changing SKUs](../container-registry/container-registry-skus.md#changing-tiers).

* If your Azure Container Registry uses a __private endpoint__, it must be in the same _virtual network_ as the storage account and compute targets used for training or inference. If it uses a __service endpoint__, it must be in the same _virtual network_ and _subnet_ as the storage account and compute targets.

* Your Azure Machine Learning workspace must contain an [Azure Machine Learning compute cluster](how-to-create-attach-compute-cluster.md).

## Limitations

### Azure Storage Account

* If you plan to use Azure Machine Learning studio and the storage account is also in the VNet, there are extra validation requirements:

    * If the storage account uses a __service endpoint__, the workspace private endpoint and storage service endpoint must be in the same subnet of the VNet.
    * If the storage account uses a __private endpoint__, the workspace private endpoint and storage service endpoint must be in the same VNet. In this case, they can be in different subnets.

### Azure Container Registry

When ACR is behind a virtual network, Azure Machine Learning cannot use it to directly build Docker images. Instead, the compute cluster is used to build the images.

> [!IMPORTANT]
> The compute cluster used to build Docker images needs to be able to access the package repositories that are used to train and deploy your models. You may need to add network security rules that allow access to public repos, [use private Python packages](how-to-use-private-python-packages.md), or use [custom Docker images](how-to-train-with-custom-image.md) that already include the packages.

> [!WARNING]
> If your Azure Container Registry uses a private endpoint to communicate with the virtual network, you cannot use a managed identity with an Azure Machine Learning compute cluster. To use a managed identity with a compute cluster, use a service endpoint with the Azure Container Registry for the workspace.

### Azure Monitor

> [!WARNING]
> Azure Monitor supports using Azure Private Link to connect to a VNet. However, Azure Machine Learning does not support using a private link-enabled Azure Monitor (including Azure Application Insights). Do __not_ configure private link for the Azure Monitor or Azure Application Insights you plan to use with Azure Machine Learning.

## Required public internet access

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

For information on using a firewall solution, see [Use a firewall with Azure Machine Learning](how-to-access-azureml-behind-firewall.md).

## Secure the workspace with private endpoint

Azure Private Link lets you connect to your workspace using a private endpoint. The private endpoint is a set of private IP addresses within your virtual network. You can then limit access to your workspace to only occur over the private IP addresses. A private endpoint helps reduce the risk of data exfiltration.

For more information on configuring a private endpoint for your workspace, see [How to configure a private endpoint](how-to-configure-private-link.md).

> [!WARNING]
> Securing a workspace with private endpoints does not ensure end-to-end security by itself. You must follow the steps in the rest of this article, and the VNet series, to secure individual components of your solution. For example, if you use a private endpoint for the workspace, but your Azure Storage Account is not behind the VNet, traffic between the workspace and storage does not use the VNet for security.

## Secure Azure storage accounts

Azure Machine Learning supports storage accounts configured to use either a private endpoint or service endpoint. 

# [Private endpoint](#tab/pe)

1. In the Azure portal, select the Azure Storage Account.
1. Use the information in [Use private endpoints for Azure Storage](../storage/common/storage-private-endpoints.md#creating-a-private-endpoint) to add private endpoints for the following storage sub-resources:

    * **Blob**
    * **File**
    * **Queue** - Only needed if you plan to use [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) in an Azure Machine Learning pipeline.
    * **Table** - Only needed if you plan to use [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) in an Azure Machine Learning pipeline.

    :::image type="content" source="./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png" alt-text="Screenshot showing private endpoint configuration page with blob and file options":::

    > [!TIP]
    > When configuring a storage account that is **not** the default storage, select the **Target subresource** type that corresponds to the storage account you want to add.

1. After creating the private endpoints for thee sub-resources, select the __Firewalls and virtual networks__ tab under __Networking__ for the storage account.
1. Select __Selected networks__, and then under __Resource instances__, select `Microsoft.MachineLearningServices/Workspace` as the __Resource type__. Select your workspace using __Instance name__. For more information, see [Trusted access based on system-assigned managed identity](../storage/common/storage-network-security.md#trusted-access-based-on-system-assigned-managed-identity).

    > [!TIP]
    > Alternatively, you can select __Allow Azure services on the trusted services list to access this storage account__ to more broadly allow access from trusted services. For more information, see [Configure Azure Storage firewalls and virtual networks](../storage/common/storage-network-security.md#trusted-microsoft-services).

    :::image type="content" source="./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-no-vnet.png" alt-text="The networking area on the Azure Storage page in the Azure portal when using private endpoint":::

1. Select __Save__ to save the configuration.

> [!TIP]
> When using a private endpoint, you can also disable public access. For more information, see [disallow public read access](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

# [Service endpoint](#tab/se)

1. In the Azure portal, select the Azure Storage Account.

1. From the __Security + networking__ section on the left of the page, select __Networking__ and then select the __Firewalls and virtual networks__ tab.

1. Select __Selected networks__. Under __Virtual networks__, select the __Add existing virtual network__ link and select the virtual network that your workspace uses.

    > [!IMPORTANT]
    > The storage account must be in the same virtual network and subnet as the compute instances or clusters used for training or inference.

1. Under __Resource instances__, select `Microsoft.MachineLearningServices/Workspace` as the __Resource type__ and select your workspace using __Instance name__. For more information, see [Trusted access based on system-assigned managed identity](../storage/common/storage-network-security.md#trusted-access-based-on-system-assigned-managed-identity).

    > [!TIP]
    > Alternatively, you can select __Allow Azure services on the trusted services list to access this storage account__ to more broadly allow access from trusted services. For more information, see [Configure Azure Storage firewalls and virtual networks](../storage/common/storage-network-security.md#trusted-microsoft-services).

    :::image type="content" source="./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png" alt-text="The networking area on the Azure Storage page in the Azure portal":::

1. Select __Save__ to save the configuration.

> [!TIP]
> When using a service endpoint, you can also disable public access. For more information, see [disallow public read access](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

---

## Secure Azure Key Vault

Azure Machine Learning uses an associated Key Vault instance to store the following credentials:
* The associated storage account connection string
* Passwords to Azure Container Repository instances
* Connection strings to data stores

Azure key vault can be configured to use either a private endpoint or service endpoint. To use Azure Machine Learning experimentation capabilities with Azure Key Vault behind a virtual network, use the following steps:

> [!TIP]
> Regardless of whether you use a private endpoint or service endpoint, the key vault must be in the same network as the private endpoint of the workspace.

# [Private endpoint](#tab/pe)

For information on using a private endpoint with Azure Key Vault, see [Integrate Key Vault with Azure Private Link](../key-vault/general/private-link-service.md#establish-a-private-link-connection-to-key-vault-using-the-azure-portal).


# [Service endpoint](#tab/se)

1. Go to the Key Vault that's associated with the workspace.

1. On the __Key Vault__ page, in the left pane, select __Networking__.

1. On the __Firewalls and virtual networks__ tab, do the following actions:
    1. Under __Allow access from__, select __Private endpoint and selected networks__.
    1. Under __Virtual networks__, select __Add existing virtual networks__ to add the virtual network where your experimentation compute resides.
    1. Under __Allow trusted Microsoft services to bypass this firewall__, select __Yes__.

    :::image type="content" source="./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png" alt-text="The Firewalls and virtual networks section in the Key Vault pane":::

For more information, see [Configure Azure Key Vault network settings](../key-vault/general/how-to-azure-key-vault-network-security.md).

---

## Enable Azure Container Registry (ACR)

> [!TIP]
> If you did not use an existing Azure Container Registry when creating the workspace, one may not exist. By default, the workspace will not create an ACR instance until it needs one. To force the creation of one, train or deploy a model using your workspace before using the steps in this section.

Azure Container Registry can be configured to use a private endpoint. Use the following steps to configure your workspace to use ACR when it is in the virtual network:

1. Find the name of the Azure Container Registry for your workspace, using one of the following methods:

    __Azure portal__

    From the overview section of your workspace, the __Registry__ value links to the Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry for the workspace" border="true":::

    __Azure CLI__

    If you have [installed the Machine Learning extension for Azure CLI](reference-azure-machine-learning-cli.md), you can use the `az ml workspace show` command to show the workspace information.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    This command returns a value similar to `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. The last part of the string is the name of the Azure Container Registry for the workspace.

1. Limit access to your virtual network using the steps in [Connect privately to an Azure Container Registry](../container-registry/container-registry-private-link.md). When adding the virtual network, select the virtual network and subnet for your Azure Machine Learning resources.

1. Configure the ACR for the workspace to [Allow access by trusted services](../container-registry/allow-access-trusted-services.md).

1. Create an Azure Machine Learning compute cluster. This is used to build Docker images when ACR is behind a VNet. For more information, see [Create a compute cluster](how-to-create-attach-compute-cluster.md).

1. Use the Azure Machine Learning Python SDK to configure the workspace to build Docker images using the compute cluster. The following code snippet demonstrates how to update the workspace to set a build compute. Replace `mycomputecluster` with the name of the cluster to use:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > Only AzureML Compute cluster of CPU SKU is supported for the image build on compute.
    
    For more information, see the [update()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) method reference.

> [!TIP]
> When ACR is behind a VNet, you can also [disable public access](../container-registry/container-registry-access-selected-networks.md#disable-public-network-access) to it.

## Datastores and datasets
The following table lists the services that you need to skip validation for:

| Service | Skip validation required? |
| ----- |:-----:|
| Azure Blob storage | Yes |
| Azure File share | Yes |
| Azure Data Lake Store Gen1 | No |
| Azure Data Lake Store Gen2 | No |
| Azure SQL Database | Yes |
| PostgreSql | Yes |

> [!NOTE]
> Azure Data Lake Store Gen1 and Azure Data Lake Store Gen2 skip validation by default, so you don't have to do anything.

The following code sample creates a new Azure Blob datastore and sets `skip_validation=True`.

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### Use datasets

The syntax to skip dataset validation is similar for the following dataset types:
- Delimited file
- JSON 
- Parquet
- SQL
- File

The following code creates a new JSON dataset and sets `validate=False`.

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 
```

## Securely connect to your workspace

[!INCLUDE [machine-learning-connect-secure-workspace](../../includes/machine-learning-connect-secure-workspace.md)]

## Workspace diagnostics

[!INCLUDE [machine-learning-workspace-diagnostics](../../includes/machine-learning-workspace-diagnostics.md)]

## Next steps

This article is part of a series on securing an Azure Machine Learning workflow. See the other articles in this series:

* [Virtual network overview](how-to-network-security-overview.md)
* [Secure the training environment](how-to-secure-training-vnet.md)
* [Secure the inference environment](how-to-secure-inferencing-vnet.md)
* [Enable studio functionality](how-to-enable-studio-virtual-network.md)
* [Use custom DNS](how-to-custom-dns.md)
* [Use a firewall](how-to-access-azureml-behind-firewall.md)
