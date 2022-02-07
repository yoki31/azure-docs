---
title: Monitor Azure app services performance .NET Core | Microsoft Docs
description: Application performance monitoring for Azure app services using ASP.NET Core. Chart load and response time, dependency information, and set alerts on performance.
ms.topic: conceptual
ms.date: 08/05/2021
ms.devlang: csharp
ms.custom: devx-track-dotnet
---

# Application Monitoring for Azure App Service and ASP.NET Core 

Enabling monitoring on your ASP.NET Core based web applications running on [Azure App Services](../../app-service/index.yml) is now easier than ever. Whereas previously you needed to manually instrument your app, the latest extension/agent is now built into the App Service image by default. This article will walk you through enabling Azure Monitor application Insights monitoring as well as provide preliminary guidance for automating the process for large-scale deployments.

## Enable agent-based monitoring

# [Windows](#tab/Windows)

> [!IMPORTANT]
> The following versions of ASP.NET Core are supported for auto-instrumentation on Windows: ASP.NET Core 3.1, 5.0 and 6.0. Versions 2.0, 2.1, 2.2, and 3.0 have been retired and are no longer supported. Please upgrade to a [supported version](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) of .NET Core for auto-instrumentation to work.

> [!NOTE] 
> Auto-instrumentation used to be known as "codeless attach" before October 2021.

[Trim self-contained deployments](/dotnet/core/deploying/trimming/trim-self-contained) is **not supported**. Use [manual instrumentation](./asp-net-core.md) via code instead.

See the [enable monitoring section](#enable-monitoring ) below to begin setting up Application Insights with your App Service resource. 

# [Linux](#tab/Linux)

> [!IMPORTANT]
> Only ASP.NET Core 6.0 is supported for auto-instrumentation on Linux.

> [!NOTE]
> Linux auto-instrumentation App Services portal enablement is in Public Preview. These preview versions are provided without a service level agreement. Certain features might not be supported or might have constrained capabilities.

[Trim self-contained deployments](/dotnet/core/deploying/trimming/trim-self-contained) is **not supported**. Use [manual instrumentation](./asp-net-core.md) via code instead.

See the [enable monitoring section](#enable-monitoring ) below to begin setting up Application Insights with your App Service resource. 

---
 

### Enable monitoring 

1. **Select Application Insights** in the Azure control panel for your app service, then select **Enable**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text=" Screenshot of Application Insights tab with enable selected."::: 

2. Choose to create a new resource, or select an existing Application Insights resource for this application.

    > [!NOTE]
    > When you click **OK** to create the new resource you will be prompted to **Apply monitoring settings**. Selecting **Continue** will link your new Application Insights resource to your app service, doing so will also **trigger a restart of your app service**. 

    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Screenshot of Change your resource dropdown."::: 

2. After specifying which resource to use, you can choose how you want Application Insights to collect data per platform for your application. ASP.NET Core offers **Recommended collection** or **Disabled**.

    :::image type="content"source="./media/azure-web-apps-net-core/instrument-net-core.png" alt-text=" Screenshot of instrument your application section."::: 


## Enable client-side monitoring

Client-side monitoring is **enabled by default** for ASP.NET Core apps with **Recommended collection**, regardless of whether the app setting 'APPINSIGHTS_JAVASCRIPT_ENABLED' is present.

If for some reason you would like to disable client-side monitoring:

* **Settings** **>** **Configuration**
   * Under Application settings, create a **new application setting**:

     name: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Value: `false`

   * **Save** the settings and **Restart** your app.


## Automate monitoring

To enable telemetry collection with Application Insights, only the Application settings need to be set:

:::image type="content"source="./media/azure-web-apps-net-core/application-settings-net-core.png" alt-text="Screenshot of App Service Application Settings with Application Insights settings."::: 


### Application settings definitions

|App setting name |  Definition | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Main extension, which controls runtime monitoring. | `~2` for Windows or `~3` for Linux |
|XDT_MicrosoftApplicationInsights_Mode |  In default mode, only essential features are enabled to insure optimal performance. | `disabled` or `recommended`. |
|XDT_MicrosoftApplicationInsights_PreemptSdk | For ASP.NET Core apps only. Enables Interop (interoperation) with Application Insights SDK. Loads the extension side-by-side with the SDK and uses it to send telemetry (disables the Application Insights SDK). |`1`|


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## Upgrade monitoring extension/agent - .NET 

### Upgrade from versions 2.8.9 and up

Upgrading from version 2.8.9 happens automatically, without any additional actions. The new monitoring bits are delivered in the background to the target app service, and on application restart they'll be picked up.

To check which version of the extension you're running, go to `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="Screenshot of the URL path to check the version of the extension you are running." border="false"::: 

### Upgrade from versions 1.0.0 - 2.6.5

Starting with version 2.8.9 the pre-installed site extension is used. If you're using an earlier version, you can update via one of two ways:

* [Upgrade by enabling via the portal](#enable-agent-based-monitoring). (Even if you have the Application Insights extension for Azure App Service installed, the UI shows only **Enable** button. Behind the scenes, the old private site extension will be removed.)

* [Upgrade through PowerShell](#enable-through-powershell):

    1. Set the application settings to enable the pre-installed site extension ApplicationInsightsAgent. See [Enable through PowerShell](#enable-through-powershell).
    2. Manually remove the private site extension named Application Insights extension for Azure App Service.

If the upgrade is done from a version prior to 2.5.1, check that the ApplicationInsigths dlls are removed from the application bin folder [see troubleshooting steps](#troubleshooting).

## Troubleshooting

> [!NOTE]
> When you create a web app with the `ASP.NET Core` runtimes in Azure App Services it deploys a single static HTML page as a starter website. It is **not** recommended to troubleshoot an issue with default template. Deploy an application before troubleshooting an issue.

Below is our step-by-step troubleshooting guide for extension/agent based monitoring for ASP.NET Core based applications running on Azure App Services.

# [Windows](#tab/windows)

1. Check that `ApplicationInsightsAgent_EXTENSION_VERSION` app setting is set to a value of "~2".
2. Browse to `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Screenshot of the link above results page."border ="false"::: 
    
    - Confirm that the `Application Insights Extension Status` is `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.` 
    
         If it isn't running, follow the [enable Application Insights monitoring instructions](#enable-agent-based-monitoring).

    - Confirm that the status source exists and looks like: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         If a similar value isn't present, it means the application isn't currently running or isn't supported. To ensure that the application is running, try manually visiting the application url/application endpoints, which will allow the runtime information to become available.

    - Confirm that `IKeyExists` is `true`
        If it is `false`, add `APPINSIGHTS_INSTRUMENTATIONKEY` and `APPLICATIONINSIGHTS_CONNECTION_STRING` with your ikey guid to your application settings.

    -  In case your application refers to any Application Insights packages, for example if you've previously instrumented (or attempted to instrument) your app with the [ASP.NET Core SDK](./asp-net-core.md), enabling the App Service integration may not take effect and the data may not appear in Application Insights. To fix the issue, in portal turn on "Interop with Application Insights SDK" and you'll start seeing the data in Application Insights. 
    - 
        > [!IMPORTANT]
        > This functionality is in preview 

        :::image type="content"source="./media/azure-web-apps-net-core/interop.png" alt-text=" Screenshot of interop setting enabled."::: 
        
        The data is now going to be sent using codeless approach even if Application Insights SDK was originally used or attempted to be used.

        > [!IMPORTANT]
        > If the application used Application Insights SDK to send any telemetry, such telemetry will be disabled – in other words, custom telemetry - if any, such as for example any Track*() methods, and any custom settings, such as sampling, will be disabled. 

# [Linux](#tab/linux)

1. Check that `ApplicationInsightsAgent_EXTENSION_VERSION` app setting is set to a value of "~3".
2. Navigate to */home\LogFiles\ApplicationInsights\status* and open *status_557de146e7fa_27_1.json*.

    Confirm that `AppAlreadyInstrumented` is set to false, `AiHostingStartupLoaded` to true and `IKeyExists` to true.

    Below is an example of the JSON file:

    ```json
        "AppType":".NETCoreApp,Version=v6.0",
                
        "MachineName":"557de146e7fa",
                
        "PID":"27",
                
        "AppDomainId":"1",
                
        "AppDomainName":"dotnet6demo",
                
        "InstrumentationEngineLoaded":false,
                
        "InstrumentationEngineExtensionLoaded":false,
                
        "HostingStartupBootstrapperLoaded":true,
                
        "AppAlreadyInstrumented":false,
                
        "AppDiagnosticSourceAssembly":"System.Diagnostics.DiagnosticSource, Version=6.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51",
                
        "AiHostingStartupLoaded":true,
                
        "IKeyExists":true,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "ConnectionString":"InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/"
    
    ```
    
    If `AppAlreadyInstrumented` is true this indicates that the extension detected that some aspect of the SDK is already present in the Application, and will back-off.

##### No Data

1. List and identify the process that is hosting an app. Navigate to your terminal and on the command line type `ps ax`. 
    
    The output should be similar to: 

   ```bash
     PID TTY      STAT   TIME COMMAND
    
        1 ?        SNs    0:00 /bin/bash /opt/startup/startup.sh
    
       19 ?        SNs    0:00 /usr/sbin/sshd
    
       27 ?        SNLl   5:52 dotnet dotnet6demo.dll
    
       50 ?        SNs    0:00 sshd: root@pts/0
    
       53 pts/0    SNs+   0:00 -bash
    
       55 ?        SNs    0:00 sshd: root@pts/1
    
       57 pts/1    SNs+   0:00 -bash
   ``` 


1. Then list environment variables from app process. On the command line type `cat /proc/27/environ | tr '\0' '\n`.
    
    The output should be similar to: 

    ```bash
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
    
    DOTNET_STARTUP_HOOKS=/DotNetCoreAgent/2.8.39/StartupHook/Microsoft.ApplicationInsights.StartupHook.dll
    
    APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/
    
    ```
    
1. Validate that `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_STARTUP_HOOKS`, and `APPLICATIONINSIGHTS_CONNECTION_STRING` are set.

---

#### Default website deployed with web apps does not support automatic client-side monitoring

When you create a web app with the `ASP.NET Core` runtimes in Azure App Services, it deploys a single static HTML page as a starter website. The static webpage also loads a ASP.NET managed web part in IIS. This allows for testing codeless server-side monitoring, but doesn't support automatic client-side monitoring.

If you wish to test out codeless server and client-side monitoring for ASP.NET Core in an Azure App Services web app, we recommend following the official guides for [creating a ASP.NET Core web app](../../app-service/quickstart-dotnetcore.md). Then use the instructions in the current article to enable monitoring.

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### PHP and WordPress are not supported

PHP and WordPress sites aren't supported. There's currently no officially supported SDK/agent for server-side monitoring of these workloads. However, manually instrumenting client-side transactions on a PHP or WordPress site by adding the client-side JavaScript to your web pages can be accomplished by using the [JavaScript SDK](./javascript.md).

The table below provides a more detailed explanation of what these values mean, their underlying causes, and recommended fixes:

|Problem Value |Explanation |Fix |
|---- |----|---|
| `AppAlreadyInstrumented:true` | This value indicates that the extension detected that some aspect of the SDK is already present in the Application, and will back-off. It can be due to a reference to `Microsoft.ApplicationInsights.AspNetCore`, or `Microsoft.ApplicationInsights`  | Remove the references. Some of these references are added by default from certain Visual Studio templates, and older versions of Visual Studio may add references to `Microsoft.ApplicationInsights`. |
|`AppAlreadyInstrumented:true` | If the application is targeting ASP.NET Core 2.1 or 2.2, this value indicates that the extension detected that some aspect of the SDK is already present in the Application, and will back-off | Customers on .NET Core 2.1,2.2 are [recommended](https://github.com/aspnet/Announcements/issues/287) to use Microsoft.AspNetCore.App meta-package instead. In addition, turn on "Interop with Application Insights SDK" in portal (see the instructions above).|
|`AppAlreadyInstrumented:true` | This value can also be caused by the presence of Microsoft.ApplicationsInsights dll in the app folder from a previous deployment. | Clean the app folder to ensure that these dlls are removed. Check both your local app's bin directory, and the wwwroot directory on the App Service. (To check the wwwroot directory of your App Service web app: Advanced Tools (Kudu) > Debug console > CMD > home\site\wwwroot). |
|`IKeyExists:false`|This value indicates that the instrumentation key is not present in the AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Possible causes: The values may have been accidentally removed, forgot to set the values in automation script, etc. | Make sure the setting is present in the App Service application settings. |

## Release notes

For the latest updates and bug fixes, [consult the release notes](web-app-extension-release-notes.md).

## Next steps
* [Run the profiler on your live app](./profiler.md).
* [Monitor Azure Functions with Application Insights](monitor-functions.md).
* [Enable Azure diagnostics](../agents/diagnostics-extension-to-application-insights.md) to be sent to Application Insights.
* [Monitor service health metrics](../data-platform.md) to make sure your service is available and responsive.
* [Receive alert notifications](../alerts/alerts-overview.md) whenever operational events happen or metrics cross a threshold.
* Use [Application Insights for JavaScript apps and web pages](javascript.md) to get client telemetry from the browsers that visit a web page.
* [Set up Availability web tests](monitor-web-app-availability.md) to be alerted if your site is down.
