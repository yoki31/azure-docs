---
title: "Tutorial: Use dynamic configuration in a .NET Core app"
titleSuffix: Azure App Configuration
description: In this tutorial, you learn how to dynamically update the configuration data for .NET Core apps
services: azure-app-configuration
documentationcenter: ''
author: maud-lv
manager: zhenlan
editor: ''

ms.assetid: 
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: malev

#Customer intent: I want to dynamically update my app to use the latest configuration data in App Configuration.
---
# Tutorial: Use dynamic configuration in a .NET Core app

The App Configuration .NET provider library supports updating configuration on demand without causing an application to restart. This tutorial shows how you can implement dynamic configuration updates in your code. It builds on the app introduced in the quickstart. You should finish [Create a .NET Core app with App Configuration](./quickstart-dotnet-core-app.md) before continuing.

You can use any code editor to do the steps in this tutorial. [Visual Studio Code](https://code.visualstudio.com/) is an excellent option that's available on the Windows, macOS, and Linux platforms.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Set up your .NET Core app to update its configuration in response to changes in an App Configuration store.
> * Consume the latest configuration in your application.

## Prerequisites

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Finish the quickstart [Create a .NET Core app with App Configuration](./quickstart-dotnet-core-app.md).

## Activity-driven configuration refresh

Open *Program.cs* and update the code as following.

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private static IConfiguration _configuration = null;
        private static IConfigurationRefresher _refresher = null;

        static void Main(string[] args)
        {
            var builder = new ConfigurationBuilder();
            builder.AddAzureAppConfiguration(options =>
            {
                options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                        .ConfigureRefresh(refresh =>
                        {
                            refresh.Register("TestApp:Settings:Message")
                                   .SetCacheExpiration(TimeSpan.FromSeconds(10));
                        });

                _refresher = options.GetRefresher();
            });

            _configuration = builder.Build();
            PrintMessage().Wait();
        }

        private static async Task PrintMessage()
        {
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

            // Wait for the user to press Enter
            Console.ReadLine();

            await _refresher.TryRefreshAsync();
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
        }
    }
}
```

In the `ConfigureRefresh` method, a key within your App Configuration store is registered for change monitoring. The `Register` method has an optional boolean parameter `refreshAll` that can be used to indicate whether all configuration values should be refreshed if the registered key changes. In this example, only the key *TestApp:Settings:Message* will be refreshed. The `SetCacheExpiration` method specifies the minimum time that must elapse before a new request is made to App Configuration to check for any configuration changes. In this example, you override the default expiration time of 30 seconds, specifying a time of 10 seconds instead for demonstration purposes.

Calling the `ConfigureRefresh` method alone won't cause the configuration to refresh automatically. You call the `TryRefreshAsync` method from the interface `IConfigurationRefresher` to trigger a refresh. This design is to avoid phantom requests sent to App Configuration even when your application is idle. You will want to include the `TryRefreshAsync` call where you consider your application active. For example, it can be when you process an incoming message, an order, or an iteration of a complex task. It can also be in a timer if your application is active all the time. In this example, you call `TryRefreshAsync` every time you press the Enter key. Note that, even if the call `TryRefreshAsync` fails for any reason, your application will continue to use the cached configuration. Another attempt will be made when the configured cache expiration time has passed and the `TryRefreshAsync` call is triggered by your application activity again. Calling `TryRefreshAsync` is a no-op before the configured cache expiration time elapses, so its performance impact is minimal, even if it's called frequently.

## Build and run the app locally

1. Set an environment variable named **ConnectionString**, and set it to the access key to your App Configuration store. If you use the Windows command prompt, run the following command and restart the command prompt to allow the change to take effect:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    If you use Windows PowerShell, run the following command:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    If you use macOS or Linux, run the following command:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Run the following command to build the console app:

    ```console
     dotnet build
    ```

1. After the build successfully completes, run the following command to run the app locally:

    ```console
     dotnet run
    ```

    ![Quickstart app launch local](./media/quickstarts/dotnet-core-app-run.png)

1. Sign in to the [Azure portal](https://portal.azure.com). Select **All resources**, and select the App Configuration store instance that you created in the quickstart.

1. Select **Configuration Explorer**, and update the values of the following keys:

    | Key | Value |
    |---|---|
    | TestApp:Settings:Message | Data from Azure App Configuration - Updated |

1. Press the Enter key to trigger a refresh and print the updated value in the Command Prompt or PowerShell window.

    ![Quickstart app refresh local](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Since the cache expiration time was set to 10 seconds using the `SetCacheExpiration` method while specifying the configuration for the refresh operation, the value for the configuration setting will only be updated if at least 10 seconds have elapsed since the last refresh for that setting.

## Clean up resources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## Next steps

In this tutorial, you enabled your .NET Core app to dynamically refresh configuration settings from App Configuration. To learn how to use an Azure managed identity to streamline the access to App Configuration, continue to the next tutorial.

> [!div class="nextstepaction"]
> [Managed identity integration](./howto-integrate-azure-managed-service-identity.md)
