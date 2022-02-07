---
title: Monitoring Azure Event Hubs data reference
description: Important reference material needed when you monitor Azure Event Hubs. 
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 01/20/2022
---


# Monitoring Azure Event Hubs data reference
See [Monitoring Azure Event Hubs](monitor-event-hubs.md) for details on collecting and analyzing monitoring data for Azure Event Hubs.

## Metrics
This section lists all the automatically collected platform metrics collected for Azure Event Hubs. The resource provider for these metrics is **Microsoft.EventHub/clusters** or **Microsoft.EventHub/clusters**.

### Request metrics
Counts the number of data and management operations requests.

| Metric Name |  Exportable via diagnostic settings | Unit | Aggregation type |  Description | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| Incoming Requests| Yes | Count | Total | The number of requests made to the Event Hubs service over a specified period. This metric includes all the data and management plane operations. | Entity name| 
| Successful Requests| No | Count | Total | The number of successful requests made to the Event Hubs service over a specified period. |  Entity name<br/><br/>Operation Result | 
| Throttled Requests| No | Count | Total |  The number of requests that were throttled because the usage was exceeded. | Entity name<br/><br/>Operation Result |

The following two types of errors are classified as **user errors**:

1. Client-side errors (In HTTP that would be 400 errors).
2. Errors that occur while processing messages.


### Message metrics
| Metric Name |  Exportable via diagnostic settings | Unit | Aggregation type |  Description | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|Incoming Messages|  Yes | Count | Total | The number of events or messages sent to Event Hubs over a specified period. | Entity name|
|Outgoing Messages| Yes | Count | Total | The number of events or messages received from Event Hubs over a specified period. | Entity name | 
| Captured Messages| No | Count| Total | The number of captured messages.  |  Entity name | 
|Incoming Bytes | Yes |  Bytes | Total | Incoming bytes for an event hub over a specified period.  | Entity name| 
|Outgoing Bytes | Yes |  Bytes | Total |Outgoing bytes for an event hub over a specified period.  | Entity name | 
| Size | No |  Bytes | Average |  Size of an event hub in bytes.|Entity name |


> [!NOTE]
> - These values are point-in-time values. Incoming messages that were consumed immediately after that point-in-time may not be reflected in these metrics. 
> - The **Incoming requests** metric includes all the data and management plane operations. The **Incoming messages** metric gives you the total number of events that are sent to the event hub. For example, if you send a batch of 100 events to an event hub, it'll count as 1 incoming request and 100 incoming messages. 

### Capture metrics
| Metric Name |  Exportable via diagnostic settings | Unit | Aggregation type |  Description | Dimensions | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
| Captured Messages| No | Count| Total | The number of captured messages.  | Entity name |
| Captured Bytes | No | Bytes | Total | Captured bytes for an event hub | Entity name | 
| Capture Backlog | No | Count| Total | Capture backlog for an event hub | Entity name | 


### Connection metrics
| Metric Name |  Exportable via diagnostic settings | Unit | Aggregation type |  Description | Dimensions | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
|Active Connections| No | Count | Average | The number of active connections on a namespace and on an entity (event hub) in the namespace. Value for this metric is a point-in-time value. Connections that were active immediately after that point-in-time may not be reflected in the metric.| Entity name | 
|Connections Opened | No | Count | Average |  The number of open connections. | Entity name | 
|Connections Closed | No | Count | Average|  The number of closed connections. | Entity name | 

### Error metrics
| Metric Name |  Exportable via diagnostic settings | Unit | Aggregation type |  Description | Dimensions |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|Server Errors| No | Count | Total | The number of requests not processed because of an error in the Event Hubs service over a specified period. | Entity name<br/><br/>Operation Result |
|User Errors | No | Count | Total | The number of requests not processed because of user errors over a specified period. | Entity name<br/><br/>Operation Result|
|Quota Exceeded Errors | No |Count | Total | The number of errors caused by exceeding quotas over a specified period. | Entity name<br/><br/>Operation Result|

> [!NOTE]
> Logic Apps creates epoch receivers and receivers may be moved from one node to another depending on the service load. During those moves, `ReceiverDisconnection` exceptions may occur. They are counted as user errors on the Event Hubs service side. Logic Apps may collect failures from Event Hubs clients so that you can view them in user logs.

## Metric dimensions

Azure Event Hubs supports the following dimensions for metrics in Azure Monitor. Adding dimensions to your metrics is optional. If you don't add dimensions, metrics are specified at the namespace level. 

|Dimension name|Description|
| ------------------- | ----------------- |
|Entity Name| Name of the event hub.|

## Resource logs
[!INCLUDE [event-hubs-diagnostic-log-schema](./includes/event-hubs-diagnostic-log-schema.md)]


## Runtime audit logs
Runtime audit logs capture aggregated diagnostic information for all data plane access operations (such as send or receive events) in the Event Hubs dedicated cluster. 

> [!NOTE] 
> Runtime audit logs are currently available only in the **dedicated** tier.  

Runtime audit logs include the elements listed in the following table:

Name | Description
------- | -------
`ActivityId` | A randomly generated UUID that ensures uniqueness for the audit activity. 
`ActivityName` | Runtime operation name.  
`ResourceId` | Resource associated with the activity. 
`Timestamp` | Aggregation time.
`Status` | Status of the activity (success or failure).
`Protocol` | Type of the protocol associated with the operation.
`AuthType` | Type of authentication (Azure Active Directory or SAS Policy).
`AuthKey` | Azure Active Directory application ID or SAS policy name that's used to authenticate to a resource.
`NetworkType` | Type of the network access: `Public` or `Private`.
`ClientIP` | IP address of the client application.
`Count` | Total number of operations performed during the aggregated period of 1 minute. 
`Properties` | Metadata that are specific to the data plane operation. 
`Category` | Log category

Here's an example of a runtime audit log entry:

```json
{
    "ActivityId": "<activity id>",
    "ActivityName": "ConnectionOpen | Authorization | SendMessage | ReceiveMessage",
    "ResourceId": "/SUBSCRIPTIONS/xxx/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace>/eventhubs/<event hub name>",
    "Time": "1/1/2021 8:40:06 PM +00:00",
    "Status": "Success | Failure",
    "Protocol": "AMQP | KAFKA | HTTP | Web Sockets", 
    "AuthType": "SAS | Azure Active Directory", 
    "AuthId": "<AAD application name | SAS policy name>",
    "NetworkType": "Public | Private", 
    "ClientIp": "x.x.x.x",
    "Count": 1,
    "Category": "RuntimeAuditLogs"
 }

```

## Application metrics Logs
Application metrics logs capture the aggregated information on certain metrics related to data plane operations. The captured information includes the following runtime metrics. 

Name | Description
------- | -------
`ConsumerLag` | Indicate the lag between consumers and producers. 
`NamespaceActiveConnections` | Details of active connections established from a client to the event hub. 
`GetRuntimeInfo` | Obtain run time information from Event Hubs. 
`GetPartitionRuntimeInfo` | Obtain the approximate runtime information for a logical partition of an event hub. 


## Azure Monitor Logs tables
Azure Event Hubs uses Kusto tables from Azure Monitor Logs. You can query these tables with Log Analytics. For a list of Kusto tables the service uses, see [Azure Monitor Logs table reference](/azure/azure-monitor/reference/tables/tables-resourcetype#event-hubs).


## Next steps
- For details on monitoring Azure Event Hubs, see [Monitoring Azure Event Hubs](monitor-event-hubs.md).
- For details on monitoring Azure resources, see [Monitoring Azure resources with Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
