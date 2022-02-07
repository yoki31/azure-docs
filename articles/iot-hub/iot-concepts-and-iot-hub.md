---
title: IoT concepts and Azure IoT Hub | Microsoft Docs
description: This article discusses the basic concepts for new users of Azure IoT Hub
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.custom: contperf-fy22q3
#Customer intent: As a developer new to IoT Hub, learn the basic concepts.
---

# IoT Concepts and Azure IoT Hub

This article discusses the Internet of Things (IoT), Azure IoT Hub, and IoT devices.

## IoT concepts

The Internet of Things (IoT) is typically defined as a network of physical devices that connect to and exchange data with other devices and services over the Internet or other communication network. There are currently over ten billion connected devices in the world and more are added every year. Anything that can be embedded with the necessary sensors and software can be connected over the internet. The following technologies have made IoT possible:

- Access to low cost, low power sensors.
- Various protocols that enable internet connectivity.
- Cloud computing platforms such as Azure.
- Big data.
- Machine learning.
- Artificial intelligence.

## Azure IoT Hub

IoT Hub is a managed service hosted in the cloud that acts as a central message hub for communication between an IoT application and its attached devices. You can connect millions of devices and their backend solutions reliably and securely. Almost any device can be connected to an IoT Hub. 

Several messaging patterns are supported, including device-to-cloud telemetry, uploading files from devices, and request-reply methods to control your devices from the cloud. IoT Hub also supports monitoring to help you track device creation, device connections, and device failures.

IoT Hub scales to millions of simultaneously connected devices and millions of events per second to support your IoT workloads. For more information about scaling your IoT Hub, see [IoT Hub Scaling](iot-hub-scaling.md). To learn more about the multiple tiers of service offered by IoT Hub and how to best fit your scalability needs, check out the [pricing page](https://azure.microsoft.com/pricing/details/iot-hub/).

You can integrate IoT Hub with other Azure services to build complete, end-to-end solutions. For example, use:

* [Azure Event Grid](../event-grid/index.yml) to enable your business to react quickly to critical events in a reliable, scalable, and secure manner.

* [Azure Logic Apps](../logic-apps/index.yml) to automate business processes.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md) to add machine learning and AI models to your solution.

* [Azure Stream Analytics](../stream-analytics/index.yml) to run real-time analytic computations on the data streaming from your devices.

[IoT Central](../iot-central/core/overview-iot-central.md) applications use multiple IoT hubs as part of their scalable and resilient infrastructure.

IoT Hub has a 99.9% [Service Level Agreement for IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/). The full [Azure SLA](https://azure.microsoft.com/support/legal/sla/) explains the guaranteed availability of Azure as a whole.

Each Azure subscription has default quota limits in place to prevent service abuse. These limits could impact the scope of your IoT solution. The current limit on a per-subscription basis is 50 IoT hubs per subscription. You can request quota increases by contacting support. For more information, see [IoT Hub Quotas and Throttling](iot-hub-devguide-quotas-throttling.md). For more details on quota limits, see one of the following articles:

* [Azure subscription service limits](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## IoT devices

IoT devices differ from other clients such as browsers and mobile apps. Specifically, IoT devices:

- Are often embedded systems with no human operator.
- Can be deployed in remote locations where physical access is expensive.
- Might only be reachable through the solution back end.
- Might have limited power and processing resources.
- Might have intermittent, slow, or expensive network connectivity.
- Might need to use proprietary, custom, or industry-specific application protocols.

### Device identity

Every IoT hub has an identity registry that stores information about the devices and modules permitted to connect to it. Before a device or module can connect, there must be an entry for that device or module in the IoT hub's identity registry. A device or module must also authenticate with the IoT hub based on credentials stored in the identity registry.

We support two methods of authentication between the device and the IoT Hub. You can use an SAS token-based authentication or X.509 certificate authentication.

The SAS-based token method provides authentication for each call made by the device to IoT Hub by associating the symmetric key to each call. X.509-based authentication allows authentication of an IoT device at the physical layer as part of the Transport Layer Security (TLS) standard connection establishment. The security-token-based method can be used without the X.509 authentication, which is a less secure pattern. The choice between the two methods is primarily dictated by how secure the device authentication needs to be, and availability of secure storage on the device (to store the private key securely).

You can set up and provision many devices at a time using the [IoT Hub Device Provisioning Service](../iot-dps/index.yml).

### Device communication

After selecting your authentication method, the internet connection between the IoT device and IoT Hub is secured using the Transport Layer Security (TLS) standard. Azure IoT supports TLS 1.2, TLS 1.1, and TLS 1.0, in that order. Support for TLS 1.0 is provided for backward compatibility only. Check TLS support in IoT Hub to see how to configure your hub to use TLS 1.2, which provides the most security.

### Device communication patterns

Typically, IoT devices send telemetry from the sensors to back-end services in the cloud. However, other types of communication are possible, such as a back-end service sending commands to your devices. Some examples of different types of communication include the following: 

*  A refrigeration truck sending temperature every 5 minutes to an IoT Hub
*  A back-end service sending a command to a device to change the frequency at which it sends telemetry to help diagnose a problem
*  A device monitoring a batch reactor in a chemical plant, sending an alert when the temperature exceeds a certain value

### Device telemetry

Examples of telemetry received from a device can include sensor data such as speed or temperature, an error message such as missed event, or an information message to indicate the device is in good health. IoT Devices send events to an application to gain insights. Applications may require specific subsets of events for processing or storage at different endpoints.

### Device properties

Properties can be read or set from the IoT Hub and can be used to send notifications when an action has completed. An example of a specific property on a device is temperature. This can be a writable property that can be updated on the device or read from a temperature sensor attached to the device. 

You can enable properties in IoT Hub using [Device Twins](iot-hub-devguide-device-twins.md) or [Plug and Play](../iot-develop/overview-iot-plug-and-play.md).

To learn more about the differences between device twins and Plug and Play, see [Plug and Play](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins).

### Device commands

An example of a command is rebooting a device. IoT Hub implements commands by allowing you to invoke direct methods on devices. [Direct methods](iot-hub-devguide-direct-methods.md) represent a request-reply interaction with a device similar to an HTTP call in that they succeed or fail immediately (after a user-specified timeout). This approach is useful for scenarios where the course of immediate action is different depending on whether the device was able to respond.

### Act on device data

IoT Hub gives you the ability to unlock the value of your device data with other Azure services so you can shift to predictive problem-solving rather than reactive management. Connect your IoT Hub with other Azure services to do machine learning, analytics and AI to act on real-time data, optimize processing, and gain deeper insights.

#### Built-in endpoint collects device data by default

A built-in endpoint collects data from your device by default. The data is collected using a request-response pattern over dedicated IoT device endpoints, is available for a maximum duration of seven days, and can be used to take actions on a device. Here is the data accepted by the device endpoint:

*    Send device-to-cloud messages.
*    Receive cloud-to-device messages.
*    Initiate file uploads.
*    Retrieve and update device twin properties.
*    Receive direct method requests.

For more information about IoT Hub endpoints, see [IoT Hub Dev Guide Endpoints](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints)

#### Message Routing sends data to other endpoints

Data can also be routed to different services for further processing. As the IoT solution scales out, the number of devices, volume of events, variety of events, and different services also varies. A flexible, scalable, consistent, and reliable method to route events is necessary to serve this pattern. Once a message route has been created, data stops flowing to the built-in-endpoint unless a fallback route has been configured. For a tutorial showing multiple uses of message routing, see the [Routing Tutorial](tutorial-routing.md).

IoT Hub also integrates with Event Grid which enables you to fan out data to multiple subscribers. Event Grid is a fully managed event service that enables you to easily manage events across many different Azure services and applications. Made for performance and scale, it simplifies building event-driven applications and serverless architectures. The differences between message routing and using Event Grid are explained in the [Message Routing and Event Grid Comparison](iot-hub-event-grid-routing-comparison.md)

## Next steps

To try out an end-to-end IoT solution, check out the IoT Hub quickstarts:

* [Quickstart: Send telemetry from a device to an IoT hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)

To learn more about the ways you can build and deploy IoT solutions with Azure IoT, visit:

* [Fundamentals: Azure IoT technologies and solutions](../iot-fundamentals/iot-services-and-technologies.md).