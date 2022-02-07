---
title: How to protect Windows Admin Center servers with Microsoft Defender for Cloud
description: This article explains how to integrate Microsoft Defender for Cloud with Windows Admin Center
ms.topic: conceptual
ms.date: 11/09/2021
---
# Protect Windows Admin Center resources with Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Windows Admin Center is a management tool for your Windows servers. It's a single location for system administrators to access the majority of the most commonly used admin tools. From within Windows Admin Center, you can directly onboard your on-premises servers into Microsoft Defender for Cloud. You can then view a summary of your security recommendations and alerts directly in the Windows Admin Center experience.

> [!NOTE]
> Your Azure subscription and the associated Log Analytics workspace both need to have Microsoft Defender for Cloud's enhanced security features enabled in order to enable the Windows Admin Center integration.
> Enhanced security features are free for the first 30 days if you haven't previously used it on the subscription and workspace. For pricing details in your local currency or region, see the [pricing page](https://azure.microsoft.com/pricing/details/defender-for-cloud/).
>

When you've successfully onboarded a server from Windows Admin Center to Microsoft Defender for Cloud, you can:

* View security alerts and recommendations inside the Defender for Cloud extension in Windows Admin Center
* View the security posture and retrieve additional detailed information of your Windows Admin Center managed servers in Defender for Cloud within the Azure portal (or via an API)

By combining these two tools, Defender for Cloud becomes your single pane of glass to view all your security information, whatever the resource: protecting your Windows Admin Center managed on-premises servers, your VMs, and any additional PaaS workloads.

## Onboard Windows Admin Center managed servers into Defender for Cloud

1. From Windows Admin Center, select one of your servers, and in the **Tools** pane, select the Microsoft Defender for Cloud extension:

    ![Microsoft Defender for Cloud extension in Windows Admin Center.](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > If the server is already onboarded to Defender for Cloud, the set-up window will not appear.

1. Click **Sign in to Azure and set up**.
    ![Onboarding Windows Admin Center extension to Defender for Cloud.](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Follow the instructions to connect your server to Defender for Cloud. After you've entered the necessary details and confirmed, Defender for Cloud makes the necessary configuration changes to ensure that all of the following are true:
    * An Azure Gateway is registered.
    * The server has a workspace to report to and an associated subscription.
    * Defender for Cloud's Log Analytics solution is enabled on the workspace. This solution provides Microsoft Defender for Cloud's features for *all* servers and virtual machines reporting to this workspace.
    * Microsoft Defender for servers is enabled on the subscription.
    * The Log Analytics agent is installed on the server and configured to report to the selected workspace. If the server already reports to another workspace, it's configured to report to the newly selected workspace as well.

    > [!NOTE]
    > It may take some time after onboarding for recommendations to appear. In fact, depending on on your server activity you may not receive *any* alerts. To generate test alerts to test your alerts are working correctly, follow the instructions in [the alert validation procedure](alert-validation.md).


## View security recommendations and alerts in Windows Admin Center

Once onboarded, you can view your alerts and recommendations directly in the Microsoft Defender for Cloud area of Windows Admin Center. Click a recommendation or an alert to view them in the Azure portal. There, you'll get additional information and learn how to remediate issues.

[![Defender for Cloud recommendations and alerts as seen in Windows Admin Center.](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## View security recommendations and alerts for Windows Admin Center managed servers in Defender for Cloud
From Microsoft Defender for Cloud:

* To view security recommendations for all your Windows Admin Center servers, open [asset inventory](asset-inventory.md) and filter to the machine type that you want to investigate. select the **VMs and Computers** tab.

* To view security alerts for all your Windows Admin Center servers, open **Security alerts**. Click **Filter** and ensure **only** "Non-Azure" is selected:

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filter security alerts for Windows Admin Center managed servers." lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::
