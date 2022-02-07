---
title: Azure IoT Edge for Linux on Windows (EFLOW) with IoT Central | Microsoft Docs
description: Learn how to connect Azure IoT Edge for Linux on Windows (EFLOW) with IoT Central 
author: v-krishnag
ms.author: v-krishnag
ms.date: 11/09/2021
ms.topic: how-to
ms.service: iot-central
---

# Azure IoT Edge for Linux on Windows (EFLOW) with IoT Central

[Azure IoT Edge for Linux on Windows (EFLOW)](/windows/iot/iot-enterprise/azure-iot-edge-for-linux-on-windows) lets you run Azure IoT Edge in a Linux container on your Windows device. In this article, you learn how to provision an EFLOW device and manage it from your IoT Central application.

In this how-to article, you learn how to:

* Create a device template for an IoT Edge device.
* Create an IoT Edge device in IoT Central.
* Connect and provision an EFLOW device.

## Prerequisites

To complete the steps in this article, you need:

* An active Azure subscription. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

* An [IoT Central application created](howto-create-iot-central-application.md) from the **Custom application** template. To learn more, see [Create an IoT Central application](howto-create-iot-central-application.md).

* A Windows device with the following minimum requirements:

  * Windows 10<sup>1</sup>/11 (Pro, Enterprise, IoT Enterprise) or Windows Server 2019<sup>1</sup>/2022
  * Minimum free memory: 1 GB
  * Minimum free disk space: 10 GB
  * <sup>1</sup> Windows 10 and Windows Server 2019 minimum build 17763 with all current cumulative updates installed.

To follow the steps in this article, download the [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json) file to your computer.

## Add device template

In this section, you create an IoT Central device template for an IoT Edge device. You import an IoT Edge manifest to get started, and then modify the template to add telemetry definitions and views:

### Create the device template and import the manifest

1. Create a device template and choose **Azure IoT Edge** as the template type.

1. On the **Customize** page of the wizard, enter a name such as *Environmental Sensor Edge Device* for the device template.

1. Select **Browse** and upload the *EnvironmentalSensorManifest.json* manifest file you downloaded previously.

1. On the **Review** page, select **Create**.

1. Select the **management** interface in the **SimulatedTemperatureSensor** module to view the two properties defined in the manifest:

:::image type="content" source="media/howto-connect-eflow/imported-manifest.png" alt-text="Device template created from IoT Edge manifest.":::

### Add telemetry to the device template

An IoT Edge manifest doesn't define the telemetry a module sends. You add the telemetry definitions to the device template in IoT Central. The **SimulatedTemperatureSensor** module sends telemetry messages that look like the following JSON:

```json
{
  "machine": {
    "temperature": 75.0,
    "pressure": 40.2
  },
  "ambient": {
    "temperature": 23.0,
    "humidity": 30.0
  },
  "timeCreated": ""
}
```

To add the telemetry definitions to the device template:

1. Select the **management** interface in the **Environmental Sensor Edge Device** template.

1. Select **+ Add capability**. Enter *machine* as the **Display name** and select the **Capability type** as **Telemetry**.

1. Select **Object** as the schema type, and then select **Define**. On the object definition page, add *temperature* and *pressure* as attributes of type **Double** and then select **Apply**.

1. Select **+ Add capability**. Enter *ambient* as the **Display name** and select the **Capability type** as **Telemetry**.

1. Select **Object** as the schema type, and then select **Define**. On the object definition page, add *temperature* and *humidity* as attributes of type **Double** and then select **Apply**.

1. Select **+ Add capability**. Enter *timeCreated* as the **Display name** and make sure that the **Capability type** is **Telemetry**.

1. Select **DateTime** as the schema type.

1. Select **Save** to update the template.

The **management** interface now includes the **machine**, **ambient**, and **timeCreated** telemetry types:

:::image type="content" source="media/howto-connect-eflow/manage-interface.png" alt-text="Interface with machine and ambient telemetry types.":::

### Add views to template

To enable an operator to view the telemetry from the device, define a view in the device template.

1. Select **Views** in the **Environmental Sensor Edge Device** template.

1. On the **Select to add a new view** page, select the **Visualizing the device** tile.

1. Change the view name to *View IoT Edge device telemetry*.

1. Under **Start with devices**, select the **ambient/temperature**, **ambient/humidity**, **machine/humidity**, and **machine/temperature** telemetry types. Then select **Add tile**.

1. Select **Save** to save the **View IoT Edge device telemetry** view.

:::image type="content" source="media/howto-connect-eflow/template-telemetry-view.png" alt-text="Device template with telemetry view.":::

### Publish the template

Before you can add a device that uses the **Environmental Sensor Edge Device** template, you must publish the template.

Navigate to the **Environmental Sensor Edge Device** template and select **Publish**. On the **Publish this device template to the application** panel, select **Publish** to publish the template:

:::image type="content" source="media/howto-connect-eflow/publish-template.png" alt-text="Publish the device template.":::

## Add an IoT Edge device

Before you can connect a device to IoT Central, you must register the device in your application:

1. In your IoT Central application, navigate to the **Devices** page and select **Environmental Sensor Edge Device** in the list of available templates.

1. Select **+ New** to add a new device from the template. On the **Create new device** page, select **Create**.

You now have a new device with the status **Registered**:

:::image type="content" source="media/howto-connect-eflow/new-device.png" alt-text="New Device.":::

### Get the device credentials

When you deploy the IoT Edge device later in this how-to article, you need the credentials that allow the device to connect to your IoT Central application. To get the device credentials:

1. On the **Device** page, select the device you created.

1. Select **Connect**.

1. On the **Device connection** page, make a note of the **ID Scope**, the **Device ID**, and the **Primary Key**. You use these values later.

1. Select **Close**.

You've now finished configuring your IoT Central application to enable an IoT Edge device to connect.

## Install and provision an EFLOW device

1. In an elevated PowerShell session, run each of the following commands to download IoT Edge for Linux on Windows.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Install IoT Edge for Linux on Windows on your device.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   You can specify custom IoT Edge for Linux on Windows installation and VHDX directories by adding `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` and `VHDXDIR="<FULLY_QUALIFIED_PATH>"` parameters to the install command.

1. Create the IoT Edge for Linux on Windows deployment. The deployment creates your Linux VM and installs the IoT Edge runtime for you.

   ```powershell
   Deploy-Eflow
   ```

1. Use the **ID scope**, **Device ID** and the **Primary Key** you made a note of previously.

 ```powershell
   Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId <ID_SCOPE_HERE> -registrationId <DEVCIE_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

To learn about other ways you can deploy and provision an EFLOW device, see [Install and provision Azure IoT Edge for Linux on a Windows device](../../iot-edge/how-to-install-iot-edge-on-windows.md).

Go to the **Device Details** page in your IoT Central application and you can see telemetry flowing from your EFLOW device:

:::image type="content" source="media/howto-connect-eflow/telemetry.png" alt-text="Telemetry from the device.":::

## Next Steps

Now that you've learned how to connect Azure IoT Edge for Linux on Windows (EFLOW) with IoT Central, the suggested next step is to learn how to [Connect devices through an IoT Edge transparent gateway](how-to-connect-iot-edge-transparent-gateway.md).