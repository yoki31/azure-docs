---
author: memildin
ms.service: defender-for-cloud
ms.topic: include
ms.date: 01/10/2022
ms.author: memildin
---
## Remove the Defender extension

::: zone pivot="defender-for-container-arc"
To remove this - or any - Defender for Cloud extension, it's not enough to turn off auto provisioning:

- **Enabling** auto provisioning, potentially impacts *existing* and *future* machines. 
- **Disabling** auto provisioning for an extension, only affects the *future* machines - nothing is uninstalled by disabling auto provisioning.

Nevertheless, to ensure the Defender for Containers components aren't automatically provisioned to your resources from now on, disable auto provisioning of the extensions as explained in [Configure auto provisioning for agents and extensions from Microsoft Defender for Cloud](../enable-data-collection.md).
::: zone-end

You can remove the extension using Azure portal, Azure CLI, or REST API as explained in the tabs below.

### [**Azure portal - Arc**](#tab/k8s-remove-arc)

### Use Azure portal to remove the extension

1. From the Azure portal, open Azure Arc.
1. From the infrastructure list, select **Kubernetes clusters** and then select the specific cluster.
1. Open the extensions page. The extensions on the cluster are listed.
1. Select the cluster and select **Uninstall**.

    :::image type="content" source="../media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Removing an extension from your Arc-enabled Kubernetes cluster." lightbox="../media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### [**Azure CLI**](#tab/k8s-remove-cli)

### Use Azure CLI to remove the Defender extension

1. Remove the Microsoft Defender for Kubernetes Arc extension with the following commands:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    Removing the extension may take a few minutes. We recommend you wait before you try to verify that it was successful.

1. To verify that the extension was successfully removed, run the following commands:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    There should be no delay in the extension resource getting deleted from Azure Resource Manager. After that, validate that there are no pods called "azuredefender-XXXXX" on the cluster by running the following command with the `kubeconfig` file pointed to your cluster: 

    ```console
    kubectl get pods -n azuredefender
    ```

    It might take a few minutes for the pods to be deleted.

### [**REST API**](#tab/k8s-remove-api)

### Use REST API to remove the Defender extension 

To remove the extension using the REST API, run the following DELETE command:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Name            | In   | Required | Type   | Description                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| Subscription ID | Path | True     | String | Your Azure Arc-enabled Kubernetes cluster's subscription ID |
| Resource Group  | Path | True     | String | Your Azure Arc-enabled Kubernetes cluster's resource group  |
| Cluster Name    | Path | True     | String | Your Azure Arc-enabled Kubernetes cluster's name            |
||||||

For **Authentication**, your header must have a Bearer token (as with other Azure APIs). To get a bearer token, run the following command:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

The request may take several minutes to complete.

---
