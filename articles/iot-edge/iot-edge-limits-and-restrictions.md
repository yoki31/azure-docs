---
title: Limits and restrictions - Azure IoT Edge | Microsoft Docs 
description: Description of the limits and restrictions when using IoT Edge.
author: raisalitch
ms.author: ralitchf
ms.date: 01/28/2022
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
---

# Understand Azure IoT Edge limits and restrictions

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

This article explains the limits and restrictions when using IoT Edge.

## Limits
### Number of children in gateway hierarchy
IoT Edge gateway hierarchies have a default limit of up to 100 connected child devices. This limit can be changed by setting the **MaxConnectedClients** environment variable in the parent device's edgeHub module.

For more information, see [Create a gateway hierarchy](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy).

### Size of desired properties
IoT Hub enforces the following restrictions:
* An 8-kb size limit on the value of tags.
* A 32-kb size limit on both the value of `properties/desired` and `properties/reported`.

For more information, see [Module twin size](../iot-hub/iot-hub-devguide-module-twins.md#module-twin-size).

### Number of nested hierarchy layers
An IoT Edge device has a limit of five layers of IoT Edge devices linked as children below it.

For more information, see [Parent and child relationships](iot-edge-as-gateway.md#cloud-identities).

### Number of modules in a deployment
IoT Hub has the following restrictions for IoT Edge automatic deployments:
* 50 modules per deployment
    * This limit is superseded by the IoT Hub 32-kb module twin size limit. For more information, see [Be mindful of twin size limits when using custom modules](production-checklist.md#be-mindful-of-twin-size-limits-when-using-custom-modules).
* 100 deployments (including layered deployments per paid SKU hub)
* 10 deployments per free SKU hub

## Restrictions
### Certificates
IoT Edge certificates have the following restrictions:
* The common name (CN) can't be the same as the "hostname" that will be used in the configuration file on the IoT Edge device.
* The name used by clients to connect to IoT Edge can't be the same as the common name used in the edge CA certificate.

For more information, see [Certificates for device security](iot-edge-certs.md#production-implications).

### TPM attestation
When using TPM attestation with the device provisioning service, you need to use TPM 2.0.

For more information, see [TPM attestation device requirements](how-to-provision-devices-at-scale-linux-tpm.md#device-requirements).

### Routing syntax
IoT Edge and IoT Hub routing syntax is almost identical.
Supported query syntax:
* [Message routing query based on message properties](../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties)
* [Message routing query based on message body](../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)

Not supported query syntax:
* [Message routing query based on device twin](../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-device-twin)

### File upload
IoT Hub only supports file upload APIs for device identities, not module identities. Since IoT Edge exclusively uses modules, file upload isn't natively supported in IoT Edge.

For more information on uploading files with IoT Hub, see [Upload files with IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md).

## Next steps
For more information, see [IoT Hub other limits](../iot-hub/iot-hub-devguide-quotas-throttling.md#other-limits).