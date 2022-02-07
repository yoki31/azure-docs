---
title: Upgrade legacy rules management to the current Azure Monitor Log Alerts API
description: Learn how to switch to the log alerts management to ScheduledQueryRules API
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 01/25/2022
---
# Upgrade legacy rules management to the current Log Alerts API from legacy Log Analytics Alert API

> [!NOTE]
> This article is only relevant to Azure public (**not** to Azure Government or Azure China cloud).

> [!NOTE]
> Once a user chooses to switch rules with legacy management to the current [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrule-2021-08-01/scheduled-query-rules) it is not possible to revert back to the older [legacy Log Analytics Alert API](./api-alerts.md).

In the past, users used the [legacy Log Analytics Alert API](./api-alerts.md) to manage log alert rules. Currently workspaces use [ScheduledQueryRules API](/rest/api/monitor/scheduledqueryrule-2021-08-01/scheduled-query-rules) for new rules. This article describes the benefits and the process of switching legacy log alert rules management from the legacy API to the current API.

## Benefits

- Manage all log rules in one API.
- Single template for creation of alert rules (previously needed three separate templates).
- Single API for all Azure resources log alerting.
- Support for stateful and 1-minute log alert previews for legacy rules.
- [PowerShell cmdlets](./alerts-manage-alerts-previous-version.md#manage-log-alerts-using-powershell) and [Azure CLI](./alerts-log.md#manage-log-alerts-using-cli) support for switched rules.
- Alignment of severities with all other alert types and newer rules.
- Ability to create [cross workspace log alert](../logs/cross-workspace-query.md) that span several external resources like Log Analytics workspaces or Application Insights resources for switched rules.
- Users can specify dimensions to split the alerts for switched rules.
- Log alerts have extended period of up to two days of data (previously limited to one day) for switched rules.

## Impact

- All switched rules must be created/edited with the current API. See [sample use via Azure Resource Template](alerts-log-create-templates.md) and [sample use via PowerShell](./alerts-manage-alerts-previous-version.md#manage-log-alerts-using-powershell).
- As rules become Azure Resource Manager tracked resources in the current API and must be unique, rules resource ID will change to this structure: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`. Display names of the alert rule will remain unchanged.

## Process

The process of switching isn't interactive and doesn't require manual steps, in most cases. Your alert rules aren't stopped or stalled, during or after the switch.
Do this call to switch all alert rules associated with the specific Log Analytics workspace:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

With request body containing the below JSON:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Here is an example of using [ARMClient](https://github.com/projectkudu/ARMClient), an open-source command-line tool, that simplifies invoking the above API call:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": true}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

If the switch is successful, the response is:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## Check switching status of workspace

You can also use this API call to check the switch status:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

You can also use [ARMClient](https://github.com/projectkudu/ARMClient) tool:

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

If the Log Analytics workspace was switched to [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrule-2021-08-01/scheduled-query-rules), the response is:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
If the Log Analytics workspace wasn't switched, the response is:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## Next steps

- Learn about the [Azure Monitor - Log Alerts](./alerts-unified-log.md).
- Learn how to [manage your log alerts using the API](alerts-log-create-templates.md).
- Learn how to [manage log alerts using PowerShell](./alerts-manage-alerts-previous-version.md#manage-log-alerts-using-powershell).
- Learn more about the [Azure Alerts experience](./alerts-overview.md).