---
title: Understand Device Update for Azure IoT Hub Configuration File| Microsoft Docs
description: Understand Device Update for Azure IoT Hub  Configuration File.
author: ValOlson
ms.author: valls
ms.date: 12/13/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
---

# Device Update for IoT Hub Configuration File

The "du-config.json" is a file that contains the below configurations for the Device Update agent. The Device Update Agent will then read these values and report them to the Device Update Service. 

* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]
* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]
* DeviceInformation.manufacturer
* DeviceInformation.model
* connectionData 
* connectionType
    
## File location

When installing Debian agent on an IoT Device with a Linux OS, modify the '/etc/adu/du-config.json' file to update values. For a Yocto build system, in the partition or disk called 'adu' create a json file called '/adu/du-config.json'.

## List of fields

|Name|Description|
|-----------|--------------------|
|SchemaVersion|The schema version that maps the current configuration file format version|
|aduShellTrustedUsers|The list of users that can launch the 'adu-shell' program. Note, 'adu-shell' is a "broker" program that does various Update Actions, as 'root'. The Device Update default content update handlers invoke 'adu-shell' to do tasks that require "super user" privilege. Examples of tasks that require this privilege are "apt-get install" or executing a privileged scripts.|
|aduc_manufacturer|Reported by the `AzureDeviceUpdateCore:4.ClientMetadata:4` interface to classify the device for targeting the update deployment.|
|aduc_model|Reported by the `AzureDeviceUpdateCore:4.ClientMetadata:4` interface to classify the device for targeting the update deployment.|
|connectionType|Possible values "string" when connecting the device to IoT Hub manually for testing purposes. For production scenarios, use value "AIS" when using the IoT Identity Service to connect the device to IoT Hub. See [understand IoT Identity Service configurations](https://azure.github.io/iot-identity-service/configuration.html)|
|connectionData|If connectionType = "string", add the value from your IoT Device's, device or module connection string here. If connectionType = "AIS", set the connectionData to empty string("connectionData": "").
|manufacturer|Reported by the Device Update Agent as part of the `DeviceInformation` interface.|
|model|Reported by the Device Update Agent as part of the `DeviceInformation` interface.|


## Example "du-config.json" file contents

```markdown

{
  "schemaVersion": "1.1",
  "aduShellTrustedUsers": [
    "adu",
    "do"
  ],
  "manufacturer": <Place your device info manufacturer here>,
  "model": <Place your device info model here>,
  "agents": [
    {
      "name": <Place your agent name here>,
      "runas": "adu",
      "connectionSource": {
        "connectionType": "string", //or “AIS”
        "connectionData": <Place your Azure IoT device connection string here>
      },
      "manufacturer": <Place your device property manufacturer here>,
      "model": <Place your device property model here>
    }
  ]
}

```
