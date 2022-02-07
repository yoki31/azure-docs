---
author: memildin
ms.service: defender-for-cloud
ms.topic: include
ms.date: 01/10/2022
ms.author: memildin
---
## Enable the plan

1. From Defender for Cloud's menu, open the [Environment settings page](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/EnvironmentSettings) and select the relevant subscription.

1. In the [Defender plans page](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/pricingTier), enable **Defender for Containers**

    > [!TIP]
    > If the subscription already has Defender for Kubernetes and/or Defender for container registries enabled, an update notice is shown. Otherwise, the only option will be **Defender for Containers**.
    >
    > :::image type="content" source="../media/release-notes/defender-plans-deprecated-indicator.png" alt-text="Defender for container registries and Defender for Kubernetes plans showing 'Deprecated' and upgrade information.":::

1. By default, the plan is configured to automatically defend any supported Kubernetes cluster that is attached to this subscription. To optionally modify the configuration, select *configure** from the configuration column.

    :::image type="content" source="../media/defender-for-containers/defender-for-containers-provisioning-configuration.gif" alt-text="Viewing the configuration for Defender for Containers.":::

    You can also modify this configuration from the [Auto provisioning page](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/dataCollection) on the **Microsoft Defender for Containers components (preview)** row:

    :::image type="content" source="../media/defender-for-containers/auto-provisioning-defender-for-containers.png" alt-text="Screenshot of the auto provisioning options for Microsoft Defender for Containers." lightbox="../media/defender-for-containers/auto-provisioning-defender-for-containers.png":::

    > [!NOTE]
    > If you choose to **disable the plan** at any time after enabling if through the portal as shown above, you'll need to manually disable auto provisioning of the Defender for Containers components. This will not remove the components from machines on which they've already been deployed.

1. If you disable the auto provisioning of any component, you can easily deploy the component to one or more clusters using the appropriate recommendation:

    - Policy Add-on for Kubernetes - [Azure Kubernetes Service clusters should have the Azure Policy Add-on for Kubernetes installed](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/08e628db-e2ed-4793-bc91-d13e684401c3)
    - Azure Kubernetes Service profile - [Azure Kubernetes Service clusters should have Defender profile enabled](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/56a83a6e-c417-42ec-b567-1e6fcb3d09a9)
    - Azure Arc-enabled Kubernetes extension - [Azure Arc-enabled Kubernetes clusters should have the Defender extension installed](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/3ef9848c-c2c8-4ff3-8b9c-4c8eb8ddfce6)

## Deploy the Defender profile

You can enable the Defender for Containers plan and deploy all of the relevant components from the Azure portal, the REST API, or with a Resource Manager template. For detailed steps, select the relevant tab.

The Defender security profile is a preview feature. [!INCLUDE [Legalese](../../../includes/defender-for-cloud-preview-legal-text.md)]

### [**Azure portal**](#tab/aks-deploy-portal)

### Use the fix button from the Defender for Cloud recommendation

A streamlined, frictionless, process lets you use the Azure portal pages to enable the Defender for Cloud plan and setup auto provisioning of all the necessary components for defending your Kubernetes clusters at scale. 

A dedicated Defender for Cloud recommendation provides:

- **Visibility** about which of your clusters has the Defender profile deployed
- **Fix** button to deploy it to those clusters without the extension

1. From Microsoft Defender for Cloud's recommendations page, open the **Enable enhanced security** security control.

1. Use the filter to find the recommendation named **Azure Kubernetes Service clusters should have Defender profile enabled**.

    > [!TIP]
    > Notice the **Fix** icon in the actions column

1. Select the clusters to see the details of the healthy and unhealthy resources - clusters with and without the profile.

1. From the unhealthy resources list, select a cluster and select **Remediate** to open the pane with the remediation confirmation.

1. Select **Fix *[x]* resources**.


### [**REST API**](#tab/aks-deploy-rest)

### Use the REST API to deploy the Defender profile

To install the 'SecurityProfile' on an existing cluster with the REST API, run the following PUT command:

```rest
PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

Request URI: `https://management.azure.com/subscriptions/{{SubscriptionId}}/resourcegroups/{{ResourceGroup}}/providers/Microsoft.ContainerService/managedClusters/{{ClusterName}}?api-version={{ApiVersion}}`
 
Request query parameters:
 
| Name           | Description                        | Mandatory |
|----------------|------------------------------------|-----------|
| SubscriptionId | Cluster's subscription ID          | Yes       |
| ResourceGroup  | Cluster's resource group           | Yes       |
| ClusterName    | Cluster's name                     | Yes       |
| ApiVersion     | API version, must be >= 2021-07-01 | Yes       |
|                |                                    |           |
 
Request Body:
 
```rest
{
  "location": "{{Location}}",
  "properties": {
    "securityProfile": {
            "azureDefender": {
                "enabled": true,
                "logAnalyticsWorkspaceResourceId": "{{LAWorkspaceResourceId}}"
            }
        }
    }
}
```
 
Request body parameters:

| Name                                                                     | Description                                                                              | Mandatory |
|--------------------------------------------------------------------------|------------------------------------------------------------------------------------------|-----------|
| location                                                                 | Cluster's location                                                                       | Yes       |
| properties.securityProfile.azureDefender.enabled                         | Determines whether to enable or disable Microsoft Defender for Containers on the cluster | Yes       |
| properties.securityProfile.azureDefender.logAnalyticsWorkspaceResourceId | Log Analytics workspace Azure resource ID                                                | Yes       |
|                                                                          |                                                                                          |           |


### [**Resource Manager**](#tab/aks-deploy-arm)

### Use Azure Resource Manager to deploy the Defender profile

To use Azure Resource Manager to deploy the Defender profile, you'll need a Log Analytics workspace on your subscription. Learn more in [Log Analytics workspaces](../../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces).

> [!TIP]
> If you're new to Resource Manager templates, start here: [What are Azure Resource Manager templates?](../../azure-resource-manager/templates/overview.md)

To install the 'SecurityProfile' on an existing cluster with Resource Manager:

```
{ 
    "type": "Microsoft.ContainerService/managedClusters", 
    "apiVersion": "2021-07-01", 
    "name": "string", 
    "location": "string",
    "properties": {
        …
        "securityProfile": { 
            "azureDefender": { 
                "enabled": true, 
                "logAnalyticsWorkspaceResourceId": “logAnalyticsWorkspaceResourceId "
            }
        },
    }
}
```