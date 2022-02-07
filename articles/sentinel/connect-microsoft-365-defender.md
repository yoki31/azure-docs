---
title: Connect Microsoft 365 Defender data to Microsoft Sentinel| Microsoft Docs
description: Learn how to ingest incidents, alerts, and raw event data from Microsoft 365 Defender into Microsoft Sentinel.
author: yelevin
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
---

# Connect data from Microsoft 365 Defender to Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> **Microsoft 365 Defender** was formerly known as **Microsoft Threat Protection** or **MTP**.
>
> **Microsoft Defender for Endpoint** was formerly known as **Microsoft Defender Advanced Threat Protection** or **MDATP**.
>
> **Microsoft Defender for Office 365** was formerly known as **Office 365 Advanced Threat Protection**.
>
> You may see the old names still in use for a period of time.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## Background

Microsoft Sentinel's [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) connector with incident integration allows you to stream all M365D incidents and alerts into Microsoft Sentinel, and keeps the incidents synchronized between both portals. M365D incidents include all their alerts, entities, and other relevant information, and they are enriched by and group together alerts from M365D's component services **Microsoft Defender for Endpoint**, **Microsoft Defender for Identity**, **Microsoft Defender for Office 365**, and **Microsoft Defender for Cloud Apps**.

The connector also lets you stream **advanced hunting** events from Microsoft Defender for Endpoint and Microsoft Defender for Office 365 into Microsoft Sentinel, allowing you to copy those Defender components' advanced hunting queries into Microsoft Sentinel, enrich Sentinel alerts with the Defender components' raw event data to provide additional insights, and store the logs with increased retention in Log Analytics.

For more information about incident integration and advanced hunting event collection, see [Microsoft 365 Defender integration with Microsoft Sentinel](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection).

> [!IMPORTANT]
>
> The Microsoft 365 Defender connector is currently in **PREVIEW**. See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## Prerequisites

- You must have a valid license for Microsoft 365 Defender, as described in [Microsoft 365 Defender prerequisites](/microsoft-365/security/mtp/prerequisites). 

- Your user must be assigned the [Global Administrator](../active-directory/roles/permissions-reference.md#global-administrator) or [Security Administrator](../active-directory/roles/permissions-reference.md#security-administrator) roles on the tenant you want to stream the logs from.

- Your user must have read and write permissions on your Microsoft Sentinel workspace.

## Connect to Microsoft 365 Defender

1. In Microsoft Sentinel, select **Data connectors**, select **Microsoft 365 Defender (Preview)** from the gallery and select **Open connector page**.

1. Under **Configuration** in the **Connect incidents & alerts** section, select the **Connect incidents & alerts** button.

1. To avoid duplication of incidents, it is recommended to mark the check box labeled **Turn off all Microsoft incident creation rules for these products.**

    > [!NOTE]
    > When you enable the Microsoft 365 Defender connector, all of the M365D components’ connectors (the ones mentioned at the beginning of this article) are automatically connected in the background. In order to disconnect one of the components’ connectors, you must first disconnect the Microsoft 365 Defender connector.

1. To query Microsoft 365 Defender incident data, use the following statement in the query window:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. If you want to collect advanced hunting events from Microsoft Defender for Endpoint or Microsoft Defender for Office 365, the following types of events can be collected from their corresponding advanced hunting tables.

    1. Mark the check boxes of the tables with the event types you wish to collect:

       # [Defender for Endpoint](#tab/MDE)

       | Table name | Events type |
       |-|-|
       | **[DeviceInfo](/microsoft-365/security/defender/advanced-hunting-deviceinfo-table)** | Machine information, including OS information |
       | **[DeviceNetworkInfo](/microsoft-365/security/defender/advanced-hunting-devicenetworkinfo-table)** | Network properties of devices, including physical adapters, IP and MAC addresses, as well as connected networks and domains |
       | **[DeviceProcessEvents](/microsoft-365/security/defender/advanced-hunting-deviceprocessevents-table)** | Process creation and related events |
       | **[DeviceNetworkEvents](/microsoft-365/security/defender/advanced-hunting-devicenetworkevents-table)** | Network connection and related events |
       | **[DeviceFileEvents](/microsoft-365/security/defender/advanced-hunting-devicefileevents-table)** | File creation, modification, and other file system events |
       | **[DeviceRegistryEvents](/microsoft-365/security/defender/advanced-hunting-deviceregistryevents-table)** | Creation and modification of registry entries |
       | **[DeviceLogonEvents](/microsoft-365/security/defender/advanced-hunting-devicelogonevents-table)** | Sign-ins and other authentication events on devices |
       | **[DeviceImageLoadEvents](/microsoft-365/security/defender/advanced-hunting-deviceimageloadevents-table)** | DLL loading events |
       | **[DeviceEvents](/microsoft-365/security/defender/advanced-hunting-deviceevents-table)** | Multiple event types, including events triggered by security controls such as Windows Defender Antivirus and exploit protection |
       | **[DeviceFileCertificateInfo](/microsoft-365/security/defender/advanced-hunting-DeviceFileCertificateInfo-table)** | Certificate information of signed files obtained from certificate verification events on endpoints |
       |

       # [Defender for Office 365](#tab/MDO)

       | Table name | Events type |
       |-|-|
       | **[EmailAttachmentInfo](/microsoft-365/security/defender/advanced-hunting-emailattachmentinfo-table)** | Information about files attached to emails |
       | **[EmailEvents](/microsoft-365/security/defender/advanced-hunting-emailevents-table)** | Microsoft 365 email events, including email delivery and blocking events |
       | **[EmailPostDeliveryEvents](/microsoft-365/security/defender/advanced-hunting-emailpostdeliveryevents-table)** | Security events that occur post-delivery, after Microsoft 365 has delivered the emails to the recipient mailbox |
       | **[EmailUrlInfo](/microsoft-365/security/defender/advanced-hunting-emailurlinfo-table)** | Information about URLs on emails |
       |

       ---

    1. Click **Apply Changes**.

    1. To query the advanced hunting tables in Log Analytics, enter the table name from the list above in the query window.

## Verify data ingestion

The data graph in the connector page indicates that you are ingesting data. You'll notice that it shows one line each for incidents, alerts, and events, and the events line is an aggregation of event volume across all enabled tables. Once you have enabled the connector, you can use the following KQL queries to generate more specific graphs.

Use the following KQL query for a graph of the incoming Microsoft 365 Defender incidents:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Use the following KQL query to generate a graph of event volume for a single table (change the *DeviceEvents* table to the required table of your choosing):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

In the **Next steps** tab, you’ll find some useful workbooks, sample queries, and analytics rule templates that have been included. You can run them on the spot or modify and save them.

## Next steps

In this document, you learned how to integrate Microsoft 365 Defender incidents, and advanced hunting event data from Microsoft Defender for Endpoint and Defender for Office 365, into Microsoft Sentinel, using the Microsoft 365 Defender connector. To learn more about Microsoft Sentinel, see the following articles:

- Learn how to [get visibility into your data, and potential threats](get-visibility.md).
- Get started [detecting threats with Microsoft Sentinel](./detect-threats-built-in.md).