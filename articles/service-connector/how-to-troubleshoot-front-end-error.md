---
title: Service Connector Troubleshooting Guidance
description: Error list and suggested actions of Service Connector
author: shizn
ms.author: xshi
ms.service: service-connector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
---

# How to troubleshoot with Service Connector

If you come across an issue, you can refer to the error message to find suggested actions or fixes. This how-to guide shows you the options to troubleshoot Service Connector.

## Error message and suggested actions from Portal

| Error message | Suggested Action |
| --- | --- |
| Unknown resource type | <ul><li>Check source and target resource to verify whether the service types are supported by Service Connector.</li><li>Check whether the specified source-target connection combination is supported by Service Connector.</li></ul> |
| Unknown resource type | <ul><li>Check whether the target resource exists.</li><li>Check the correctness of the target resource ID.</li></ul> |
| Unsupported resource | <ul><li>Check whether the authentication type is supported by the specified source-target connection combination.</li></ul> |

### Error type，Error message and suggested actions using Azure CLI

### InvalidArgumentValueError


| Error message | Suggested Action |
| --- | --- |
| The source resource ID is invalid: `{SourceId}` | <ul><li>Check whether the source resource ID supported by Service Connector.</li><li>Check the correctness of source resource ID.</li></ul> |
| Target resource ID is invalid: `{TargetId}` | <ul><li>Check whether the target service type is supported by Service Connector.</li><li>Check the correctness of target resource ID.</li></ul> |
| Connection ID is invalid: `{ConnectionId}` | <ul><li>Check the correctness of the connection ID.</li></ul> |


### RequiredArgumentMissingError

| Error message | Suggested Action |
| --- | --- |
| `{Argument}` should not be blank | User should provide argument value for interactive input |
| Required keys missing for parameter `{Parameter}`. All possible keys are: `{Keys}` | Provide value for the auth info parameter, usually in the form of `--param key1=val1 key2=val2`. |
| Required argument is missing, please provide the arguments: `{Arguments}` | Provide the required argument. | 

### ValidationError

| Error message | Suggested Action |
| --- | --- |
| Only one auth info is needed | User can only provide one auth info parameter, check whether auth info is not provided or multiple auth info parameters are provided. |
| Auth info argument should be provided when updating the connection: `{ConnectionName}` | When updating a secret type connection, auth info parameter should be provided. (This is because user's secret can not be accessed through ARM api) |
| Either client type or auth info should be specified to update | When updating a connection, either client type or auth info should be provided. |
| Usage error: {} [KEY=VALUE ...] | Check the available keys and provide values for the auth info parameter, usually in the form of `--param key1=val1 key2=val2`. |
| Unsupported Key `{Key}` is provided for parameter `{Parameter}`. All possible keys are: `{Keys}` | Check the available keys and provide values for the auth info parameter, usually in the form of `--param key1=val1 key2=val2`. |
| Provision failed, please create the target resource manually and then create the connection. Error details: `{ErrorTrace}` | <ul><li>Retry.</li><li>Create the target resource manually and then create the connection.</li></ul> |

## Next steps

Follow the tutorials listed below to learn more about Service Connector.

> [!div class="nextstepaction"]
> [Learn about Service Connector concepts](./concept-service-connector-internals.md)
