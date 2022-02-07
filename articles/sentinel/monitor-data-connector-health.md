---
title: Monitor the health of your Microsoft Sentinel data connectors | Microsoft Docs
description: Use the SentinelHealth data table and the Health Monitoring workbook to keep track of your data connectors' connectivity and performance.
author: bagol
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 12/30/2021
ms.author: yelevin
---

---
# Monitor the health of your data connectors

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

After you've configured and connected your Microsoft Sentinel workspace to your data connectors, you'll want to monitor your connector health, viewing any service or data source issues, such as authentication, throttling, and more.

You also might like to configure notifications for health drifts for relevant stakeholders who can take action. For example, configure email messages, Microsoft Teams messages, new tickets in your ticketing system, and so on.

This article describes how to use the following features, which allow you to keep track of your data connectors' health, connectivity, and performance from within Microsoft Sentinel:

- **Data connectors health monitoring workbook**. This workbook provides additional monitors, detects anomalies, and gives insight regarding the workspace’s data ingestion status. You can use the workbook’s logic to monitor the general health of the ingested data, and to build custom views and rule-based alerts.

- ***SentinelHealth* data table**. (Public preview) Provides insights on health drifts, such as latest failure events per connector, or connectors with changes from success to failure states, which you can use to create alerts and other automated actions.

    > [!NOTE]
    > The *SentinelHealth* data table is currently supported only for [selected data connectors](#supported-data-connectors).
    >


## Use the health monitoring workbook

1. From the Microsoft Sentinel portal, select **Workbooks** from the **Threat management** menu.

1. In the **Workbooks** gallery, enter *health* in the search bar, and select **Data collection health monitoring** from among the results.

1. Select **View template** to use the workbook as is, or select **Save** to create an editable copy of the workbook. When the copy is created, select **View saved workbook**.

1. Once in the workbook, first select the **subscription** and **workspace** you wish to view, then define the **TimeRange** to filter the data according to your needs. Use the **Show help** toggle to display in-place explanation of the workbook.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="data connector health monitoring workbook landing page" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

There are three tabbed sections in this workbook:

- The **Overview** tab shows the general status of data ingestion in the selected workspace: volume measures, EPS rates, and time last log received.

- The **Data collection anomalies** tab will help you to detect anomalies in the data collection process, by table and data source. Each tab presents anomalies for a particular table (the **General** tab includes a collection of tables). The anomalies are calculated using the **series_decompose_anomalies()** function that returns an **anomaly score**. [Learn more about this function](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Set the following parameters for the function to evaluate:

    - **AnomaliesTimeRange**: This time picker applies only to the data collection anomalies view.
    - **SampleInterval**: The time interval in which data is sampled in the given time range. The anomaly score is calculated only on the last interval's data.
    - **PositiveAlertThreshold**: This value defines the positive anomaly score threshold. It accepts decimal values.
    - **NegativeAlertThreshold**: This value defines the negative anomaly score threshold. It accepts decimal values.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="data connector health monitoring workbook anomalies page" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

- The **Agent info** tab shows you information about the health of the Log Analytics agents installed on your various machines, whether Azure VM, other cloud VM, on-premises VM, or physical. You can monitor the following:

   - System location

   - Heartbeat status and latency

   - Available memory and disk space

   - Agent operations

    In this section you must select the tab that describes your machines’ environment: choose the **Azure-managed machines** tab if you want to view only the Azure Arc-managed machines; choose the **All machines** tab to view both managed and non-Azure machines with the Log Analytics agent installed.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="data connector health monitoring workbook agent info page" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## Use the SentinelHealth data table (Public preview)

To get data connector health data from the *SentinelHealth* data table, you must first [turn on the Microsoft Sentinel health feature](#turn-on-microsoft-sentinel-health-for-your-workspace) for your workspace.

Once the health feature is turned on, the *SentinelHealth* data table is created at the first success or failure event generated for your data connectors.

> [!TIP]
> To configure the retention time for your health events, see the [Log Analytics retention configuration documentation](/azure/azure-monitor/logs/manage-cost-storage).
>

> [!IMPORTANT]
>
> The SentinelHealth data table is currently in **PREVIEW**. See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>

### Supported data connectors

The *SentinelHealth* data table is currently supported only for the following data connectors:

- [Amazon Web Services (CloudTrail and S3)](connect-aws.md)
- [Dynamics 365](connect-dynamics-365.md)
- [Office 365](connect-office-365.md)
- [Office ATP](connect-microsoft-defender-advanced-threat-protection.md)
- [Threat Intelligence - TAXII](connect-threat-intelligence-taxii.md)
- [Threat Intelligence Platforms](connect-threat-intelligence-tip.md)

### Turn on Microsoft Sentinel health for your workspace

1. In Microsoft Sentinel, under the **Configuration** menu on the left, select **Settings** and expand the **Health** section.

1. Select **Configure Diagnostic Settings** and create a new diagnostic setting.

    - In the **Diagnostic setting name** field, enter a meaningful name for your setting.

    - In the **Category details** column, select **DataConnectors**.

    - Under **Destination details**, select **Send to Log Analytics workspace**, and select your subscription and workspace from the dropdown menus.

1. Select **Save** to save your new setting.

The *SentinelHealth* data table is created at the first success or failure event generated for your data connectors.


### Access the *SentinelHealth* table

In the Microsoft Sentinel **Logs** page, run a query on the  *SentinelHealth* table. For example:

```kusto
SentinelHealth
 | take 20
```

### Understanding SentinelHealth table events

The following types of health events are logged in the *SentinelHealth* table:

- **Data fetch status change**. Logged once an hour as long as a data connector status remains stable, with either continuous success or failure events. For as long as a data connector's status does not change, monitoring only hourly works to prevent redundant auditing and reduce table size. If the data connector's status has continuous failures, additional details about the failures are included in the *ExtendedProperties* column.

    If the data connector's status changes, either from a success to failure, from failure to success, or has changes in failure reasons, the event is logged immediately to allow your team to take proactive and immediate action.

    Potentially transient errors, such as source service throttling, are logged only after they've continued for more than 60 minutes. These 60 minutes allow Microsoft Sentinel to overcome a transient issue in the backend and catch up with the data, without requiring any user action. Errors that are definitely not transient are logged immediately.

- **Failure summary**. Logged once an hour, per connector, per workspace, with an aggregated failure summary. Failure summary events are created only when the connector has experienced polling errors during the given hour. They contain any extra details provided in the *ExtendedProperties* column, such as the time period for which the connector's source platform was queried, and a distinct list of failures encountered during the time period.

For more information, see [SentinelHealth table columns schema](#sentinelhealth-table-columns-schema).

### Run queries to detect health drifts

Create queries on the *SentinelHealth* table to help you detect health drifts in your data connectors. For example:

**Detect latest failure events per connector**:

```kusto
SentinelHealth
| where TimeGenerated > ago(3d)
| where OperationName == 'Data fetch status change'
| where Status in ('Success', 'Failure')
| summarize TimeGenerated = arg_max(TimeGenerated,*) by SentinelResourceName, SentinelResourceId
| where Status == 'Failure'
```

**Detect connectors with changes from fail to success state**:

```kusto
let lastestStatus = SentinelHealth
| where TimeGenerated > ago(12h)
| where OperationName == 'Data fetch status change'
| where Status in ('Success', 'Failure')
| project TimeGenerated, SentinelResourceName, SentinelResourceId, LastStatus = Status
| summarize TimeGenerated = arg_max(TimeGenerated,*) by SentinelResourceName, SentinelResourceId;
let nextToLastestStatus = SentinelHealth
| where TimeGenerated > ago(12h)
| where OperationName == 'Data fetch status change'
| where Status in ('Success', 'Failure')
| join kind = leftanti (lastestStatus) on SentinelResourceName, SentinelResourceId, TimeGenerated
| project TimeGenerated, SentinelResourceName, SentinelResourceId, NextToLastStatus = Status
| summarize TimeGenerated = arg_max(TimeGenerated,*) by SentinelResourceName, SentinelResourceId;
lastestStatus
| join kind=inner (nextToLastestStatus) on SentinelResourceName, SentinelResourceId
| where NextToLastStatus == 'Failure' and LastStatus == 'Success'
```

**Detect connectors with changes from success to fail state**:

```kusto
let lastestStatus = SentinelHealth
| where TimeGenerated > ago(12h)
| where OperationName == 'Data fetch status change'
| where Status in ('Success', 'Failure')
| project TimeGenerated, SentinelResourceName, SentinelResourceId, LastStatus = Status
| summarize TimeGenerated = arg_max(TimeGenerated,*) by SentinelResourceName, SentinelResourceId;
let nextToLastestStatus = SentinelHealth
| where TimeGenerated > ago(12h)
| where OperationName == 'Data fetch status change'
| where Status in ('Success', 'Failure')
| join kind = leftanti (lastestStatus) on SentinelResourceName, SentinelResourceId, TimeGenerated
| project TimeGenerated, SentinelResourceName, SentinelResourceId, NextToLastStatus = Status
| summarize TimeGenerated = arg_max(TimeGenerated,*) by SentinelResourceName, SentinelResourceId;
lastestStatus
| join kind=inner (nextToLastestStatus) on SentinelResourceName, SentinelResourceId
| where NextToLastStatus == 'Success' and LastStatus == 'Failure'
```

### Configure alerts and automated actions for health issues

While you can use the Microsoft Sentinel [analytics rules](automate-incident-handling-with-automation-rules.md) to configure automation in Microsoft Sentinel logs, if you want to be notified and take immediate action for health drifts in your data connectors, we recommend that you use [Azure Monitor alert rules](/azure/azure-monitor/alerts/alerts-overview).

For example:

1. In an Azure Monitor alert rule, select your Microsoft Sentinel workspace as the rule scope, and **Custom log search** as the first condition.

1. Customize the alert logic as needed, such as frequency or lookback duration, and then use [queries](#run-queries-to-detect-health-drifts) to search for health drifts.

1. For the rule actions, select an existing action group or create a new one as needed to configure push notifications or other automated actions such as triggering a Logic App, Webhook, or Azure Function in your system.

For more information, see [Azure Monitor alerts overview](/azure/azure-monitor/alerts/alerts-overview) and [Azure Monitor alerts log](/azure/azure-monitor/alerts/alerts-log).

### SentinelHealth table columns schema

The following table describes the columns and data generated in the *SentinelHealth* data table:

| ColumnName    | ColumnType     | Description|
| ----------------------------------------------- | -------------- | --------------------------------------------------------------------------- |
| **TenantId**      | String         | The tenant ID for your Microsoft Sentinel workspace.                    |
| **TimeGenerated** | Datetime       | The time at which the health event occurred.         |
| <a name="operationname"></a>**OperationName** | String         | The health operation. One of the following values: <br><br>-`Data fetch status change` for health or success indications <br>- `Failure summary` for aggregated health summaries. <br><br>For more information, see [Understanding SentinelHealth table events](#understanding-sentinelhealth-table-events). |
| <a name="sentinelresourceid"></a>**SentinelResourceId**        | String         | The unique identifier of the Microsoft Sentinel workspace and the associated connector on which the health event occurred. |
| **SentinelResourceName**      | String         | The data connector name.                           |
| <a name="status"></a>**Status**        | String         | Indicates `Success` or `Failure` for the `Data fetch status change` [OperationName](#operationname), and `Informational` for the `Failure summary` [OperationName](#operationname).         |
| **Description**   | String         | Describes the operation, including extended data as needed. For example, for failures, this column might indicate the failure reason. |
| **WorkspaceId**   | String         | The workspace GUID on which the health issue occurred. The full Azure Resource Identifier is available in the [SentinelResourceID](#sentinelresourceid) column. |
| **SentinelResourceType**      | String         |The Microsoft Sentinel resource type being monitored: `Data connector`|
| **SentinelResourceKind**      | String         | The type of data connector being monitored, such as `Office365`.               |
| **RecordId**      | String         | A unique identifier for the record that can be shared with the support team for better correlation as needed.                |
| **ExtendedProperties**        | Dynamic (json) | A JSON bag that varies by the [OperationName](#operationname) value and the [Status](#status) of the event: <br><br>- For `Data fetch status change` events with a success indicator, the bag contains a ‘DestinationTable’ property to indicate where data from this connector is expected to land. For failures, the contents vary depending on the failure type.    |
| **Type**          | String         | `SentinelHealth`                         |
| | | |

## Next steps

Learn how to [onboard your data to Microsoft Sentinel](quickstart-onboard.md), [connect data sources](connect-data-sources.md), and [get visibility into your data, and potential threats](get-visibility.md).
