---
title: Tutorial - Azure IoT water quality monitoring | Microsoft Docs
description: This tutorial shows you how to deploy and use the water quality monitoring application template for IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/23/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
---


# Tutorial: Deploy and walk through the water quality monitoring application

Traditional water quality monitoring relies on manual sampling techniques and field laboratory analysis, which is time consuming and costly. By remotely monitoring water quality in real-time, water quality issues can be managed before citizens are affected. Moreover, with advanced analytics, water utilities, and environmental agencies can act on early warnings on potential water quality issues and plan on water treatment in advance.  

The water quality monitoring app is an IoT Central app template to help you kickstart your IoT solution development and enable water utilities to digitally monitor water quality in smart cities.

Use the IoT Central *water quality monitoring* application template and the guidance in this article to develop an end-to-end water quality monitoring solution.

![Water quality monitoring architecture](./media/tutorial-waterqualitymonitoring/concepts-water-quality-monitoring-architecture1.png)

### Devices and connectivity

Water management solutions use smart water devices such as flow meters, water quality monitors, smart valves, leak detectors.

Devices in smart water solutions may connect through low-power wide area networks (LPWAN) or through a third-party network operator. For these types of devices, use the [Azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) to send your device data to your IoT application in Azure IoT Central. You can also use an IP capable device gateway that connects directly to your IoT Central application.

### IoT Central

When you build an IoT solution, Azure IoT Central simplifies the build process and helps to reduce the burden and costs of IoT management, operations, and development. You can brand, customize, and integrate your solution with third-party services.

When you connect your smart water devices to IoT Central, the application provides device command and control, monitoring and alerting, a user interface with built-in RBAC, configurable dashboards, and extensibility options.

### Extensibility and integrations

You can extend your IoT application in IoT Central and optionally:

* Transform and integrate your IoT data for advanced analytics, for example training machine learning models, through continuous data export from IoT Central application.
* Automate workflows in other systems by triggering actions using Power Automate or webhooks from IoT Central application.
* Programatically access your IoT application in IoT Central through IoT Central APIs.

### Business applications

You can use IoT data to power various business applications within a water utility. In your [IoT Central water consumption monitoring application](tutorial-water-consumption-monitoring.md) you can configure rules and actions, and set them to create alerts in [Connected Field Service](/dynamics365/field-service/connected-field-service). Configure Power Automate in IoT Central rules to automate workflows across applications and services. Additionally, based on service activities in Connected Field Service, information can be sent back to Azure IoT Central.

In this tutorial, you learn to:

> [!div class="checklist"]

> * Use the **Water quality monitoring** template to create a water quality monitoring application.
> * Explore and customize an dashboard.
> * Explore a water quality monitoring device template.
> * Explore simulated devices.
> * Explore and configure rules.
> * Configure jobs.
> * Customize application branding by using white labeling.

## Prerequisites

* There are no specific prerequisites required to deploy this app.
* You can use the free pricing plan or use an Azure subscription.

## Create water quality monitoring application

Create the application using following steps:

1. Navigate to the [Azure IoT Central Build](https://aka.ms/iotcentral) site. Then sign in with a Microsoft personal, work, or school account. Select **Build** from the left-hand navigation bar and then select the **Government** tab:
    :::image type="content" source="media/tutorial-waterqualitymonitoring/iot-central-government-tab-overview1.png" alt-text="Application template":::

1. Select **Create app** under **Water quality monitoring**.

To learn more, see [Create an IoT Central application](../core/howto-create-iot-central-application.md).

## Walk through the application

The following sections walk you through the key features of the application:

### Dashboard

After you create the application, the **Wide World water quality dashboard** pane opens.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-dashboard1.png" alt-text="The water quality monitoring dashboard.":::

As a builder, you can create and customize views on the dashboard for use by operators. But before you try to customize, first explore the dashboard.

All data shown in the dashboard is based on simulated device data, which is discussed in the next section.

The dashboard includes the following kinds of tiles:

* **Wide World water utility image tile**: The first tile in the upper-left corner of the dashboard is an image that shows the fictitious utility named Wide World. You can customize the tile to use your own image, or you can remove the tile.

* **Average pH KPI tiles**: KPI tiles like **Average pH in the last 30 minutes** are at the top of the dashboard pane. You can customize KPI tiles and set each to a different type and time range.

* **Water monitoring area map**: Azure IoT Central uses Azure Maps, which you can directly set in your application to show device [location](../core/howto-use-location-data.md). You can also map location information from your application to your device and then use Azure Maps to show the information on a map. Hover over the map and try the controls.

* **Average pH distribution heat-map chart**: You can select different visualization charts to show device telemetry in the way that is most appropriate for your application.

* **Critical quality indicators line chart**: You can visualize device telemetry plotted as a line chart over a time range.  

* **Concentration of chemical agents bar chart**: You can visualize device telemetry in a bar chart.

* **Reset sensors parameters tile**: The dashboard includes a tile for actions that an operator can initiate directly from the monitoring dashboard. Resetting a device's properties is an example of such actions.

* **Property list tiles**: The dashboard has multiple property tiles that represent threshold information, device health information, and maintenance information.

### Customize the dashboard

As a builder, you can customize views on the dashboard for use by operators.

1. Select **Edit** to customize the **Wide World water quality dashboard** pane. You can customize the dashboard by selecting commands on the **Edit** menu. After the dashboard is in edit mode, you can add new tiles, or you can configure the existing files.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/edit-dashboard.png" alt-text="Edit your dashboard.":::

1. Select **+ New** to create a new dashboard that you can configure. You can have multiple dashboards and can navigate among them from the dashboard menu.

## Explore a water quality monitoring device template

A device template in Azure IoT Central defines the capabilities of a device. Available capabilities are telemetry, properties, and commands. As a builder, you can define device templates in Azure IoT Central that represent the capabilities of the connected devices. You can also create simulated devices to test your device template and application.

The water quality monitoring application you created comes with a water quality monitoring device template.

To view the device template:

1. Select **Device templates** on the leftmost pane of your application in Azure IoT Central.
1. From the list of device templates, select **Water Quality Monitor** to open that device template.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template.png" alt-text="The device template.":::

### Customize the device template

Practice customizing the following device template settings:

1. From the device template menu, select **Customize**.
1. Go to the **Temperature** telemetry type.
1. Change the **Display name** value to **Reported temperature**.
1. Change the unit of measurement, or set **Min value** and **Max value**.
1. Select **Save**.

#### Add a cloud property

1. From the device template menu, select **Cloud properties**.
1. To add a new cloud property, select **+ Add Cloud Property**. In Azure IoT Central, you can add a property that is relevant to a device but that doesn't come from the device. One example of such a property is an alert threshold specific to installation area, asset information, or maintenance information.
1. Enter **Installation area** as the **Display name** and choose **String** as the **Schema**.
1. Select **Save**.

### Explore views

The water quality monitoring device template comes with predefined views. The views define how operators see the device data and set cloud properties. Explore the views and practice making changes.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template-views.png" alt-text="Device template views.":::

### Publish the device template

If you make any changes, be sure to select **Publish** to publish the device template.

### Create a new device template

1. On the **Device templates** page, select **+ New** to create a new device template and follow the creation process.
1. Create a custom device template or choose a device template from the Azure IoT device catalog.

## Explore simulated devices

The water quality monitoring application you created from the application template has two simulated devices. These devices map to the water quality monitoring device template.

### View the devices

1. Select **Devices** on the leftmost pane of your application.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-devices.png" alt-text="Devices":::

1. Select one simulated device.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitor-device1.png" alt-text="Select device 1":::

1. On the **Cloud Properties** tab, change the **Acidity (pH) threshold** value to **9** and select **Save**.
1. Explore the **Device Properties** tab and the **Device Dashboard** tab.

> [!NOTE]
> All tabs have been configured from **Device template views**.

### Add new devices

1. On the **Devices** tab, select **+ New** to add a new device.
1. Use the suggested **Device ID** or enter your own. You can also enter a **Device name** for your new device.
1. Select **Water Quality Monitor** as the **Device template**. 
1. Make sure the **Simulate this device** is set to **Yes** if you want to create a simulated device. 
1. Select **Create**.  

## Explore and configure rules

In Azure IoT Central, you can create rules that automatically monitor device telemetry. These rules trigger an action when any of their conditions are met. One possible action is to send email notifications. Other possibilities include a Power Automate action or a webhook action to send data to other services.

The water quality monitoring application you created has two preconfigured rules.

### View rules

1. Select **Rules** on the leftmost pane of your application.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-rules.png" alt-text="Rules":::

1. Select **High pH alert**, which is one of the preconfigured rules in the application.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-high-ph-alert.png" alt-text="The high pH alert rule.":::

   The **High pH alert** rule is configured to check the condition of acidity (pH) being greater than 8.

Next, add an email action to the rule:

1. Select **+ Email**.
1. In the **Display name** box, enter **High pH alert**.
1. In the **To** box, enter the email address associated with your Azure IoT Central account.
1. Optionally, enter a note to include in the text of the email.
1. Select **Done** to complete the action.
1. Set the rule to **Enabled** and select **Save**.

Within a few minutes, you should receive email when the configured condition is met.

> [!NOTE]
> The application sends email each time a condition is met. Select **Disable** for a rule to stop receiving automated email from that rule.
  
To create a new rule, select **Rules** on the leftmost pane of your application and then select **+New**.

## Configure jobs

With Azure IoT Central jobs, you can trigger updates to device or cloud properties on multiple devices. You can also use jobs to trigger device commands on multiple devices. Azure IoT Central automates the workflow for you.

1. Select **Jobs** on the leftmost pane of your application.
1. Select **+New job** and configure one or more jobs.

## Customize your application

As a builder, you can change several settings to customize the user experience in your application.

1. Select **Administration** > **Customize your application**.
1. Under **Masthead logo**, select **Change** to choose the image to upload as the logo.
1. Under **Browser icon**, select **Change** to choose the image that appears on browser tabs.
1. Under **Browser colors**, you can replace the default values with HTML hexadecimal color codes.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-customize-your-application1.png" alt-text="Customize your application":::

### Update the application image

1. Select **Administration** > **Your application**.

1. Select **Change** to choose an image to upload as the application image.

## Clean up resources

If you're not going to continue to use your application, delete the application with the following steps:

1. Open the **Administration** tab on the leftmost pane of your application.
1. Select **Your application** and select the **Delete** button.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-application-settings-delete-app1.png" alt-text="Delete your application.":::
