---
title: Take a tour of the Azure IoT Central API | Microsoft Docs
description: Become familiar with the key areas of the Azure IoT Central REST API. Use the API to create, manage, and use your IoT solution from client applications.
author: dominicbetts
ms.author: dobett
ms.date: 01/25/2022
ms.topic: overview
ms.service: iot-central
services: iot-central
---

# Take a tour of the Azure IoT Central API

This article introduces you to Azure IoT Central REST API. Use the API to create client applications that can create, manage, and use an IoT Central application and its connected devices. The extensibility surface enabled by the IoT Central REST API lets you integrate IoT insights and device management capabilities into your existing dashboards and business applications.

The REST API operations are grouped into the:

- *Data plane* operations that let you work with resources inside IoT Central applications. Data plane operations let you automate tasks that can also be completed using the IoT Central UI. Currently, there are [generally available](/rest/api/iotcentral/1.0dataplane/api-tokens) and [preview](/rest/api/iotcentral/1.1-previewdataplane/api-tokens) versions of the data plane API.
- *Control plane* operations that let you work with the Azure resources associated with IoT Central applications. Control plane operations let you automate tasks that can also be completed in the Azure portal.

## Data plane operations

Version 1.0 of the data plane API lets you manage the following resources in your IoT Central application:

- API tokens
- Device templates
- Devices
- Roles
- Users

The devices API also lets you [query telemetry and property values from your devices](howto-query-with-rest-api.md).

To get started with the data plane APIs, see [Explore the IoT Central APIs](/learn/modules/manage-iot-central-apps-with-rest-api/).

## Control plane operations

Version 2021-06-01 of the control plane API lets you manage the IoT Central applications in your Azure subscription. To learn more, see the [Control plane overview](/rest/api/iotcentral/2021-06-01controlplane/apps).

## Next steps

Now that you have an overview of Azure IoT Central and are familiar with the capabilities of the IoT Central REST API, the suggested next step is to complete the [Explore the IoT Central APIs](/learn/modules/manage-iot-central-apps-with-rest-api/) Learn module.
