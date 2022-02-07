---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
---

To create a Visual Studio project for UWP development, you need to:

- Set up Visual Studio development options.
- Create the project and select the target architecture.
- Set up audio capture.
- Install the Speech SDK.

### Set up Visual Studio development options

To start, make sure you're set up correctly in Visual Studio for UWP development:

1. Open Visual Studio 2019 to display the start window.   

1. Select **Continue without code** to go to the Visual Studio IDE.

   ![Screenshot that shows the start window with the action for continuing without code highlighted.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. From the Visual Studio menu bar, select **Tools** > **Get Tools and Features** to open Visual Studio Installer and view the **Modifying** dialog.

1. On the **Workloads** tab, under **Windows**, find the **Universal Windows Platform development** workload. If the check box next to that workload is already selected, close the **Modifying** dialog and go to step 7.

   ![Screenshot that shows the Workloads tab of the Modifying dialog, with the workload for Universal Windows Platform development highlighted.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Select the **Universal Windows Platform development** check box, and then select **Modify**. 

1. In the **Before we get started** dialog, select **Continue** to install the UWP development workload. Installation of the new feature might take a while.

1. Close Visual Studio Installer.

### Create the project

Next, create your project and select the target architecture:

1. On the Visual Studio menu bar, select **File** > **New** > **Project** to display the **Create a new project** window.   

1. Find and select **Blank App (Universal Windows)**. Make sure that you select the C# version of this project type (as opposed to Visual Basic).

1. Select **Next**.  

   ![Screenshot that shows the window for creating a new project, with Blank App (Universal Windows) selected and the Next button highlighted.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. In the **Configure your new project** dialog, in **Project name**, enter **helloworld**.

1. In **Location**, go to and select (or create) the folder where you want to save your project.

1. Select **Create**.  

   ![Screenshot that shows the dialog for configuring a new project, with boxes for project name and location and the Create button highlighted.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. In the **New Universal Windows Platform Project** window, in **Minimum version** (the second drop-down box), select **Windows 10 Fall Creators Update (10.0; Build 16299)**. That's the minimum requirement for the Speech SDK.   

1. In **Target version** (the first drop-down box), choose a value identical to or later than the value in **Minimum version**.

   ![Screenshot that shows the New Universal Windows Platform Project dialog with minimum and target versions selected.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Select **OK**. You're returned to the Visual Studio IDE, with the new project created and visible on the **Solution Explorer** pane.

   ![Screenshot that shows the helloworld project visible in Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

1. Select your target platform architecture. On the Visual Studio toolbar, find the **Solution Platforms** drop-down box. If you don't see it, select **View** > **Toolbars** > **Standard** to display the toolbar that contains **Solution Platforms**. 

   If you're running 64-bit Windows, select **x64** in the drop-down box. 64-bit Windows can also run 32-bit applications, so you can choose **x86** if you prefer.

   > [!NOTE]
   > The Speech SDK supports all Intel-compatible processors, but *only x64* versions of ARM processors.

### Set up audio capture

Allow the project to capture audio input:

1. In **Solution Explorer**, double-click **Package.appxmanifest** to open the package application manifest.

1. Select the **Capabilities** tab.

   ![Screenshot of Visual Studio that shows the Capabilities tab in the package application manifest.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Select the box for the **Microphone** capability.

1. From the menu bar, select **File** > **Save Package.appxmanifest** to save your changes.

### Install the Speech SDK

Finally, install the [Speech SDK NuGet package](https://aka.ms/csspeech/nuget), and reference the Speech SDK in your project:

1. In Solution Explorer, right-click your solution, and select **Manage NuGet Packages for Solution** to go to the **NuGet - Solution** window.

1. Select **Browse**.  

1. In **Package source**, select **nuget.org**.

   ![Screenshot that shows the Manage Packages for Solution dialog, with the Browse tab, search box, and package source highlighted.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. In the **Search** box, enter **Microsoft.CognitiveServices.Speech**. Choose that package after it appears in the search results.   

1. In the package status pane next to the search results, select your **helloworld** project.

1. Select **Install**.

   ![Screenshot that shows the Microsoft.CognitiveServices.Speech package selected, with the project and the Install button highlighted.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. In the **Preview Changes** dialog, select **OK**.

1. In the **License Acceptance** dialog, view the license, and then select **I Accept**. The package installation begins. When installation is complete, the **Output** pane displays a message that's similar to the following text: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld`.
