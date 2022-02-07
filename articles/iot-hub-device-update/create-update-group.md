---
title: Create device group in Device Update for Azure IoT Hub | Microsoft Docs
description: Create a device group in Device Update for Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
---

# Create device groups in Device Update for IoT Hub
Device Update for IoT Hub allows deploying an update to a group of IoT devices.

  > [!NOTE] 
  > If you would like to deploy to a default group instead of a user-created group, you can directly move to [How to Deploy an Update](deploy-update.md)


## Prerequisites

* [Access to an IoT Hub with Device Update for IoT Hub enabled](create-device-update-account.md). It is recommended that you use a S1 (Standard) tier or above for your IoT Hub. 
* An IoT device (or simulator) provisioned for Device Update within IoT Hub.
* [At least one update has been successfully imported for the provisioned device.](import-update.md)
* Install and start the Device Update agent on your IoT device either as a [module or device level identity](device-update-agent-provisioning.md)

## Add a tag to your devices  

Device Update for IoT Hub allows deploying an update to a group of IoT devices. To create a group, the first step is to add a tag to the target set of devices in IoT Hub. Tags can only be successfully added to your device after it has been connected to Device Update.

The below documentation describes how to add and update a tag.

### Programmatically update Device Twin

You can update the Device Twin with the appropriate Tag using RegistryManager after enrolling the device with Device Update. 
[Learn how to add tags using a sample .NET app.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Learn about tag properties](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### Device Update Tag Format

```json
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### Using Jobs

It is possible to schedule a Job on multiple devices to add or update a Device Update tag following [these](../iot-hub/iot-hub-devguide-jobs.md) examples. You can update Device Twin or Module Twin (if Device Update agent is set up as a Module Identity) using Jobs. [Learn more](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > This action goes against your current IOT Hub messages quota and it is recommended to change only up to 50,000 device or module twin Tags at a time otherwise you may need to buy more IoT Hub units if you exceed your daily IoT Hub message quota. Details can be found at [Quotas and throttling](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling).

### Direct Twin Updates

Tags can also be added or updated in Device twin or Module Twin directly.

1. Log into [Azure portal](https://portal.azure.com) and navigate to your IoT Hub.

2. From 'IoT Devices' or 'IoT Edge' on the left navigation pane find your IoT device and navigate to the Device Twin, or the Device Update Module and then its Module Twin (this will be available if Device Update agent is set up as a Module Identity).

3. In the Device Twin or Module Twin, delete any existing Device Update tag value by setting them to null.

4. Add a new Device Update tag value as shown below. [Example device twin JSON document with tags.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### Limitations

* You can add any value to your tag except for ‘Uncategorized’ which is a reserved value.
* Tag value cannot exceed 255 characters.
* Tag value can contain alphanumeric characters and the following special characters ".","-","_","~".
* Tag and Group names are case-sensitive.
* A device can only have one tag with the name ADUGroup, any subsequent additions of a tag with that name will override the existing value for tag name ADUGroup.
* One device can only belong to one Group.

## Create a device group by selecting an existing IoT Hub tag

1. Go to the [Azure portal](https://portal.azure.com).

2. Select the IoT Hub you previously connected to your Device Update instance.

3. Select the Updates option under Device Management from the left-hand navigation bar.

4. Select the Groups and Deployments tab at the top of the page. 
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Screenshot of ungrouped devices." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Select the "Add group" button to create a new group.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Screenshot of device group addition." lightbox="media/create-update-group/add-group.png":::

6. Select an IoT Hub tag and Device Class from the list and then select Create group.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Screenshot of tag selection." lightbox="media/create-update-group/select-tag.png":::

7. Once the group is created, you will see that the update compliance chart and groups list are updated.  Update compliance chart shows the count of devices in various states of compliance: On latest update, New updates available, and Updates in Progress. [Learn  about update compliance.](device-update-compliance.md)
   :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Screenshot of update compliance view." lightbox="media/create-update-group/updated-view.png":::

8. You should see your newly created group and any available updates for the devices in the new group. If there are devices that don't meet the device class requirements of the group, they will show up in a corresponding invalid group. You can deploy the best available update to the new user-defined group from this view by clicking on the "Deploy" button next to the group. See Next Step: Deploy Update for more details.

## View Device details for the group you created

1. Navigate to your newly created group and click on the group name.

2. A list of devices that are part of the group will be shown along with their device update properties. In this view, you can also see the update compliance information for all devices that are members of the group. Update compliance chart shows the count of devices in various states of compliance: On latest update, New updates available and Updates in Progress.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Screenshot of device group details view." lightbox="media/create-update-group/group-details.png":::

3. You can also click on each individual device within a group to be redirected to the device details page in IoT Hub.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Screenshot of device details view." lightbox="media/create-update-group/device-details.png":::

## Next Steps 

[Deploy Update](deploy-update.md)

[Learn more about device groups](device-update-groups.md)

[Learn  about update compliance.](device-update-compliance.md)
