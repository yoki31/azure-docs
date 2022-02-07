---
title: Connect a  RuuviTag in Azure IoT Central | Microsoft Docs
description: Learn how to connect a RuuviTag environment sensor to your IoT Central application. 
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom: [iot-storeAnalytics-conditionMonitor, iot-p0-scenario]
ms.author: avneets
author: avneet723
ms.date: 08/20/2021

# This article applies to solution builders.
---

# Connect a RuuviTag sensor to your Azure IoT Central application

A RuuviTag is an advanced open-source sensor beacon platform designed to fulfill the needs of business customers, developers, makers, students, and hobbyists. The device is set up to work as soon as you take it out of its box and is ready for you to deploy it where you need it. It's a Bluetooth Low Energy (BLE) beacon with a built-in environment sensor and accelerometer.

A RuuviTag communicates over BLE and requires a gateway device to talk to Azure IoT Central. Make sure you have a gateway device, such as the Rigado Cascade 500, setup to enable a RuuviTag to connect to IoT Central. To learn more, see [Connect a Rigado Cascade 500 gateway device to your Azure IoT Central application](howto-connect-rigado-cascade-500.md).

This article describes how to connect a RuuviTag sensor to your Azure IoT Central application.

## Prerequisites

To connect RuuviTag sensors, you need the following resources:

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

- A RuuviTag sensor. For more information, please visit [RuuviTag](https://ruuvi.com/).

- A Rigado Cascade 500 device or another BLE gateway. To learn more, visit [Rigado](https://www.rigado.com/).

## Add a RuuviTag device template

To onboard a RuuviTag sensor into your Azure IoT Central application instance, you need to configure a corresponding device template within your application.

To add a RuuviTag device template:

1. Navigate to the **Device Templates** tab in the left pane, select **+ New**:

    ![Create new device template](./media/howto-connect-ruuvi/device-template-new.png)

    The page gives you an option to **Create a custom template** or **Use a preconfigured device template**.

1. Select the RuuviTag Multisensor device template from the list of preconfigured device templates:

    ![Select RuuviTag device template](./media/howto-connect-ruuvi/device-template-pre-configured.png)

1. Select **Next: Customize** to continue to the next step.

1. On the next screen, select **Create** to onboard the RuuviTag Multisensor device template into your IoT Central application.

## Connect a RuuviTag sensor

To connect the RuuviTag with your IoT Central application, you need to set up a gateway device. The following steps assume that you've set up a Rigado Cascade 500 gateway device:  

1. Power on your Rigado Cascade 500 device and connect it to your wired or wireless network.

1. Pop the cover off of the RuuviTag and pull the plastic tab to connect the battery.

1. Place the RuuviTag close to the Rigado Cascade 500 gateway that's already configured in your IoT Central application.

1. In a few seconds, your RuuviTag appears in the list of devices within IoT Central:

    ![RuuviTag Device List](./media/howto-connect-ruuvi/ruuvi-device-list.png)

You can now use this RuuviTag device within your IoT Central application.  

## Create a simulated RuuviTag

If you don't have a physical RuuviTag device, you can create a simulated RuuviTag sensor to use for testing within your Azure IoT Central application.

To create a simulated RuuviTag:

1. Select **Devices > RuuviTag**.

1. Select **+ New**.

1. Specify a unique **Device ID** and a friendly **Device name**.  

1. Enable the **Simulated** setting.

1. Select **Create**.  

## Next Steps

Some suggested next steps are to:

- Read about [Device connectivity in Azure IoT Central](./concepts-get-connected.md)
- Learn how to [Monitor device connectivity using Azure CLI](./howto-monitor-devices-azure-cli.md)
