---
title: Microsoft Defender for Resource Manager - the benefits and features
description: Learn about the benefits and features of Microsoft Defender for Resource Manager
ms.date: 11/09/2021
ms.topic: overview
---

# Introduction to Microsoft Defender for Resource Manager

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Resource Manager](../azure-resource-manager/management/overview.md) is the deployment and management service for Azure. It provides a management layer that enables you to create, update, and delete resources in your Azure account. You use management features, like access control, locks, and tags, to secure and organize your resources after deployment.

The cloud management layer is a crucial service connected to all your cloud resources. Because of this, it is also a potential target for attackers. Consequently, we recommend security operations teams monitor the resource management layer closely. 

Microsoft Defender for Resource Manager automatically monitors the resource management operations in your organization, whether they're performed through the Azure portal, Azure REST APIs, Azure CLI, or other Azure programmatic clients. Defender for Cloud runs advanced security analytics to detect threats and alerts you about suspicious activity.

>[!NOTE]
> Some of these analytics are powered by [Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security) (formerly known as Microsoft Cloud App Security). To benefit from these analytics, you must activate a Defender for Cloud Apps license. If you have a Defender for Cloud Apps license, then these alerts are enabled by default. To disable the alerts:
>
> 1. From Defender for Cloud's menu, open **Environment settings**.
> 1. Select the subscription you want to change.
> 1. Select **Integrations**.
> 1. Clear **Allow Microsoft Defender for Cloud Apps to access my data**, and select **Save**.


## Availability

|Aspect|Details|
|----|:----|
|Release state:|General availability (GA)|
|Pricing:|**Microsoft Defender for Resource Manager** is billed as shown on the [pricing page](https://azure.microsoft.com/pricing/details/defender-for-cloud/)|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Commercial clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet|
|||

## What are the benefits of Microsoft Defender for Resource Manager?

Microsoft Defender for Resource Manager protects against issues including:

- **Suspicious resource management operations**, such as operations from malicious IP addresses, disabling antimalware and suspicious scripts running in VM extensions
- **Use of exploitation toolkits** like Microburst or PowerZure
- **Lateral movement** from the Azure management layer to the Azure resources data plane

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure Resource Manager overview diagram.":::

A full list of the alerts provided by Microsoft Defender for Resource Manager is on the [alerts reference page](alerts-reference.md#alerts-resourcemanager).


 ## How to investigate alerts from Microsoft Defender for Resource Manager

Security alerts from Microsoft Defender for Resource Manager are based on threats detected by monitoring Azure Resource Manager operations. Defender for Cloud uses internal log sources of Azure Resource Manager as well as Azure Activity log, a platform log in Azure that provides insight into subscription-level events.

Learn more about [Azure Activity log](../azure-monitor/essentials/activity-log.md).

To investigate security alerts from Microsoft Defender for Resource Manager:

1. Open Azure Activity log.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="How to open Azure Activity log.":::

1. Filter the events to:
    - The subscription mentioned in the alert
    - The timeframe of the detected activity
    - The related user account (if relevant)

1. Look for suspicious activities.

> [!TIP]
> For a better, richer investigation experience, stream your Azure activity logs to Microsoft Sentinel as described in [Connect data from Azure Activity log](../sentinel/data-connectors-reference.md#azure-activity).



## Next steps

In this article, you learned about Microsoft Defender for Resource Manager.

> [!div class="nextstepaction"]
> [Enable enhanced protections](enable-enhanced-security.md)

For related material, see the following article:

- Security alerts might be generated or received by Defender for Cloud from different security products. To export all of these alerts to Microsoft Sentinel, any third-party SIEM, or any other external tool, follow the instructions in [Exporting alerts to a SIEM solution](continuous-export.md).
