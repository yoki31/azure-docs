---
title: Monitor Azure app services performance Java | Microsoft Docs
description: Application performance monitoring for Azure app services using Java. Chart load and response time, dependency information, and set alerts on performance.
ms.topic: conceptual
ms.date: 08/05/2021
ms.devlang: java
ms.custom: "devx-track-java"
---

# Application Monitoring for Azure App Service and Java

Monitoring of your Java web applications running on [Azure App Services](../../app-service/index.yml) does not require any modifications to the code. This article will walk you through enabling Azure Monitor Application Insights monitoring as well as provide preliminary guidance for automating the process for large-scale deployments.

## Enable Application Insights

The recommended way to enable application monitoring for Java applications running on Azure App Services is through Azure portal.
Turning on application monitoring in Azure portal will automatically instrument your application with Application Insights, and doesn't require any code changes.
You can apply additional configurations, and then based on your specific scenario you [add your own custom telemetry](./java-in-process-agent.md#modify-telemetry) if needed.

### Auto-instrumentation through Azure portal

You can turn on monitoring for your Java apps running in Azure App Service just with one click, no code change required.
Application Insights for Java is integrated with Azure App Service on Linux - both code-based and custom containers, and with App Service on Windows for code-based apps.
The integration adds [Application Insights Java 3.x](./java-in-process-agent.md) and you will get the telemetry auto-collected.

1. **Select Application Insights** in the Azure control panel for your app service, then select **Enable**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Screenshot of Application Insights tab with enable selected."::: 

2. Choose to create a new resource, or select an existing Application Insights resource for this application.

    > [!NOTE]
    > When you select **OK** to create the new resource you will be prompted to **Apply monitoring settings**. Selecting **Continue** will link your new Application Insights resource to your app service, doing so will also **trigger a restart of your app service**. 

    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Screenshot of Change your resource dropdown.":::

3. This last step is optional. After specifying which resource to use, you can configure the Java agent. If you do not configure the Java agent, default configurations will apply.

    The full [set of configurations](./java-standalone-config.md) is available, you just need to paste a valid [json file](./java-standalone-config.md#an-example). **Exclude the connection string and any configurations that are in preview** - you will be able to add the items that are currently in preview as they become generally available.

    Once you modify the configurations through Azure portal, APPLICATIONINSIGHTS_CONFIGURATION_FILE environment variable will automatically be populated and will appear in App Service settings panel. This variable will contain the full json content that you have pasted in Azure portal configuration text box for your Java app. 

    :::image type="content"source="./media/azure-web-apps-java/create-app-service-ai.png" alt-text="Screenshot of instrument your application."::: 
    

## Enable client-side monitoring

To enable client-side monitoring for your Java application, you need to [manually add the client-side JavaScript SDK to your application](./javascript.md).

## Automate monitoring

In order to enable telemetry collection with Application Insights, only the following Application settings need to be set:

:::image type="content"source="./media/azure-web-apps-java/application-settings-java.png" alt-text="Screenshot of App Service Application Settings with available Application Insights settings.":::

### Application settings definitions

| App setting name | Definition | Value |
|------------------|------------|------:|
| ApplicationInsightsAgent_EXTENSION_VERSION | Main extension, which controls runtime monitoring. | `~2` in Windows or `~3` in Linux. |
| XDT_MicrosoftApplicationInsights_Java | Flag to control if Java agent is included. | 0 or 1 (only applicable in Windows). |

> [!NOTE]
> Profiler and snapshot debugger are not available for Java applications

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]

## Troubleshooting

Below is our step-by-step troubleshooting guide for Java-based applications running on Azure App Services.

1. Check that `ApplicationInsightsAgent_EXTENSION_VERSION` app setting is set to a value of "~2" on Windows, "~3" on Linux
1. Examine the log file to see that the agent has started successfully: browse to `https://yoursitename.scm.azurewebsites.net/, under SSH change to the root directory, the log file is located under LogFiles/ApplicationInsights. 
  
    :::image type="content"source="./media/azure-web-apps-java/app-insights-java-status.png" alt-text="Screenshot of the link above results page."::: 

1. After enabling application monitoring for your Java app, you can validate that the agent is working by looking at the live metrics - even before you deploy and app to App Service you will see some requests from the environment. Remember that the full set of telemetry will only be available when you have your app deployed and running. 
1. Set APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL environment variable to 'debug' if you do not see any errors and there is no telemetry
1. Sometimes the latest version of the Application Insights Java agent is not available in App Service - it takes a couple of months for the latest versions to roll out to all regions. In case you need the latest version of Java agent to monitor your app in App Service, you can upload the agent manually: 
    * Upload the Java agent jar file to App Service
        * Get the latest version of [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli)
        * Get the latest version of [Application Insights Java agent](./java-in-process-agent.md)
        * Deploy Java agent to App Service - a sample command to deploy the Java agent jar: `az webapp deploy --src-path applicationinsights-agent-{VERSION_NUMBER}.jar --target-path java/applicationinsights-agent-{VERSION_NUMBER}.jar --type static --resource-group {YOUR_RESOURCE_GROUP} --name {YOUR_APP_SVC_NAME}` or use [this guide](../../app-service/quickstart-java.md?tabs=javase&pivots=platform-linux#configure-the-maven-plugin) to deploy through Maven plugin
    * Once the agent jar file is uploaded, go to App Service configurations and add a new environment variable, JAVA_OPTS, and set its value to `-javaagent:D:/home/{PATH_TO_THE_AGENT_JAR}/applicationinsights-agent-{VERSION_NUMBER}.jar`
    * Disable Application Insights via Application Insights tab
    * Restart the app

    > [!NOTE]
    > If you set the JAVA_OPTS environment variable, you will have to disable Application Insights in the portal. Alternatively, if you prefer to enable Application Insights from the portal, make sure that you don't set the JAVA_OPTS variable in App Service configurations settings. 


[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

## Release notes

For the latest updates and bug fixes, [consult the release notes](web-app-extension-release-notes.md).

## Next steps

* [Monitor Azure Functions with Application Insights](monitor-functions.md).
* [Enable Azure diagnostics](../agents/diagnostics-extension-to-application-insights.md) to be sent to Application Insights.
* [Monitor service health metrics](../data-platform.md) to make sure your service is available and responsive.
* [Receive alert notifications](../alerts/alerts-overview.md) whenever operational events happen or metrics cross a threshold.
* Use [Application Insights for JavaScript apps and web pages](javascript.md) to get client telemetry from the browsers that visit a web page.
* [Set up Availability web tests](monitor-web-app-availability.md) to be alerted if your site is down.