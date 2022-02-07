---
title: Understand Device Update for Azure IoT Hub device groups | Microsoft Docs
description: Understand how device groups are used.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
---

# Device groups

A device group is a collection of devices. Device groups provide a way to scale deployments to many devices. Each device belongs to exactly one device group at a time.
You may choose to create multiple device groups to organize your devices. For example, Contoso might use the "Flighting" device group for the devices in its test laboratory and 
the "Evaluation" device group for the devices that its field team uses in the operations center. Further, Contoso might choose to group their Production devices based on
their geographic regions, so that they can update devices on a schedule that aligns with their regional timezones. 


## Using device or module twin tag for device group creation

Tags enable users to group devices. Devices need to have a ADUGroup key and a value in their device or module twin to allow them to be grouped.

### Device or module twin tag format

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```

## Default device group

Any device that has the Device Update agent installed and provisioned, but does not have a ADUGroup tag added to its device or module twin will be added to a default group. Default groups or system-assigned groups help reduce the overhead of tagging and grouping devices, so customers can easily deploy updates to them. Default groups cannot be deleted or re-created by customers. Customers cannot change the definition or add/remove devices from a default group manually. Devices with the same device class are grouped together in a default group. Default group names are reserved within an IOT solution. Default groups will be named in the format “Default-(deviceClassID)”. All deployment features that are available for user-defined groups are also available for default, system-assigned groups.

For example consider the devices with their device twin tags below:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Below are the devices and the possible groups that can be created for them.

|Device	|Group  |
|-----------|--------------|
|Device1	|Group1|
|Device2	|Group1|
|Device3	|Group2|
|Device4	|DefaultGroup1-(deviceClassId)|


## Invalid group

A corresponding invalid group is created for every user-defined group. A device is added to the invalid group if it doesn't meet the compatibility requirements of the user-defined group. This can be resolved by either re-tagging and regrouping the device under a new group, or modifying it's compatibility properties through the agent configuration file. 

An invalid group only exists for diagnostic purposes. Updates cannot be deployed to invalid groups

## Next steps

[Create device group](./create-update-group.md)
