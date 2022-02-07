---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
---

1. Start Visual Studio 2019.

1. Make sure the **.NET cross-platform development** workload is available. Select **Tools** > **Get Tools and Features** from the Visual Studio menu bar to open the Visual Studio installer. If this workload is already enabled, close the dialog.   

   Otherwise, select the box next to **.NET Core cross-platform development**, and select **Modify** at the lower-right corner of the dialog. Installation of the new feature will take a moment.
   
   ![Screenshot of the Visual Studio installer, with the Workloads tab highlighted.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

1. Create a new Visual C# .NET Core console app. In the **New Project** dialog, from the left pane, expand **Installed** > **Visual C#** > **.NET Core**. Then select **Console App (.NET Core)**. For the project name, enter **helloworld**.

   ![Screenshot of the New Project dialog.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Create Visual C# Console App (.NET Core)")

1. Install and reference the [Speech SDK NuGet package](https://aka.ms/csspeech/nuget). In Solution Explorer, right-click the solution and select **Manage NuGet Packages for Solution**.

   ![Screenshot of Solution Explorer, with Manage NuGet Packages for Solution highlighted.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Manage NuGet Packages for Solution")

1. In the upper-right corner, in the **Package Source** box, select **nuget.org**. Search for the **Microsoft.CognitiveServices.Speech** package, and install it into the **helloworld** project.

   ![Screenshot that shows the Manage Packages for Solution dialog.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Install NuGet package")

1. Accept the displayed license to begin installation of the NuGet package.

   ![Screenshot of License Acceptance dialog.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Accept the license")

After the package is installed, a confirmation appears in the Package Manager console.
