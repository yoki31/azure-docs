---
title: Audio Content Creation - Speech service
titleSuffix: Azure Cognitive Services
description: Audio Content Creation is an online tool that allows you to customize and fine-tune Microsoft's text-to-speech output for your apps and products.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2022
ms.author: eur
---

# Improve synthesis with the Audio Content Creation tool

[Audio Content Creation](https://aka.ms/audiocontentcreation) is an easy-to-use and powerful tool that lets you build highly natural audio content for a variety of scenarios, such as audiobooks, news broadcasts, video narrations, and chat bots. With Audio Content Creation, you can fine-tune text-to-speech voices and design customized audio experiences in an efficient and low-cost way.

The tool is based on [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). It allows you to adjust text-to-speech output attributes in real time or batch synthesis, such as voice characters, voice styles, speaking speed, pronunciation, and prosody.

As of November 2021, you have easy access to more than 270 neural voices across 119 different languages. These voices include state-of-the-art prebuilt neural voices and your custom neural voice, if you've built one.

To learn more, view the [Audio Content Creation tutorial video](https://youtu.be/ygApYuOOG6w).

## Get started

Audio Content Creation is a free tool, but you'll pay for the Speech service that you consume. To work with the tool, you need to sign in with an Azure account and create a Speech resource. For each Azure account, you have free monthly speech quotas, which include 0.5 million characters for prebuilt neural voices (referred to as *Neural* on the [pricing page](https://aka.ms/speech-pricing)). The monthly allotted amount is usually enough for a small content team of around 3-5 people. 

The next sections cover how to create an Azure account and get a Speech resource.

### Step 1: Create an Azure account

To work with Audio Content Creation, you need a [Microsoft account](https://account.microsoft.com/account) and an [Azure account](https://azure.microsoft.com/free/ai/). To set up the accounts, see the "Try the Speech service for free" section in [What is the Speech service?](./overview.md#try-the-speech-service-for-free).

[The Azure portal](https://portal.azure.com/) is the centralized place for you to manage your Azure account. You can create the Speech resource, manage the product access, and monitor everything from simple web apps to complex cloud deployments.

### Step 2: Create a Speech resource

After you sign up for the Azure account, you need to create a Speech resource in your Azure account to access Speech services. For instructions, see [how to create a Speech resource](./overview.md#create-the-azure-resource).

It takes a few moments to deploy your new Speech resource. After the deployment is complete, you can start using the Audio Content Creation tool.

 > [!NOTE]
   > If you plan to use neural voices, make sure that you create your resource in [a region that supports neural voices](regions.md#prebuilt-neural-voices).

### Step 3: Sign in to Audio Content Creation with your Azure account and Speech resource

1. After you get the Azure account and the Speech resource, you can sign in to the [Audio Content Creation tool](https://aka.ms/audiocontentcreation) by selecting **Get started**.

1. The home page lists all the products under Speech Studio. To start, select **Audio Content Creation**.

    The **Welcome to Speech Studio** page opens. 
    
1. Select the Azure subscription and the Speech resource you want to work with, and then select **Use resource**. 

   The next time you sign in to Audio Content Creation, you're linked directly to the audio work files under the current Speech resource. You can check your Azure subscription details and status in the [Azure portal](https://portal.azure.com/).  
   
   If you don't have an available Speech resource and you're the owner or admin of an Azure subscription, you can create a Speech resource in Speech Studio by selecting **Create a new resource**. 
   
   If you have a user role for a certain Azure subscription, you might not have permissions to create a new Speech resource. To get access, contact your admin. 

   To modify your Speech resource at any time, select **Settings** at the top of the page.

   To switch directories, select **Settings** or go to your profile. 

## Use the tool

The following diagram displays the process for fine-tuning the text-to-speech outputs. 

:::image type="content" source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Diagram of the sequence of steps for fine-tuning text-to-speech outputs.":::

Each step in the preceding diagram is described here:

1. Choose the Speech resource you want to work with.

1. [Create an audio tuning file](#create-an-audio-tuning-file) by using plain text or SSML scripts. Type or upload your content in to Audio Content Creation.
1. Choose the voice and the language for your script content. Audio Content Creation includes all of the [Microsoft text-to-speech voices](language-support.md#text-to-speech). You can use prebuilt neural voices or a custom neural voice.

   > [!NOTE]
   > Gated access is available for Custom Neural Voice, which allows you to create high-definition voices that are similar to natural-sounding speech. For more information, see [Gating process](./text-to-speech.md).

1. Select the content you want to preview, and then select **Play** (triangle icon) to preview the default synthesis output. 

   If you make any changes to the text, select the **Stop** icon, and then select **Play** again to regenerate the audio with changed scripts. 

   Improve the output by adjusting pronunciation, break, pitch, rate, intonation, voice style, and more. For a complete list of options, see [Speech Synthesis Markup Language](speech-synthesis-markup.md). 

   For more information about fine-tuning speech output, view the [How to convert Text to Speech using Microsoft Azure AI voices](https://youtu.be/ygApYuOOG6w) video.

1. Save and [export your tuned audio](#export-tuned-audio). 

   When you save the tuning track in the system, you can continue to work and iterate on the output. When you're satisfied with the output, you can create an audio creation task with the export feature. You can observe the status of the export task and download the output for use with your apps and products.

## Create an audio tuning file

You can get your content into the Audio Content Creation tool in either of two ways:

* **Option 1**

  1. Select **New** > **File** to create a new audio tuning file.

  1. Type or paste your content into the editing window. The allowable number of characters for each file is 20,000 or fewer. If your script contains more than 20,000 characters, you can use Option 2 to automatically split your content into multiple files.
  1. Select **Save**.

* **Option 2**

  1. Select **Upload** to import one or more text files. Both plain text and SSML are supported. 

     If your script file is more than 20,000 characters, split the content by paragraphs, by characters, or by regular expressions.

  1. When you upload your text files, make sure that they meet these requirements:

        | Property | Description |
        |----------|---------------|
        | File format | Plain text (.txt)\*<br> SSML text (.txt)\**<br/> Zip files aren't supported. |
        | Encoding format | UTF-8 |
        | File name | Each file must have a unique name. Duplicate files aren't supported. |
        | Text length | Character limit is 20,000. If your files exceed the limit, split them according to the instructions in the tool. |
        | SSML restrictions | Each SSML file can contain only a single piece of SSML. |
        | | |

        \* **Plain text example**:

        ```txt
        Welcome to use Audio Content Creation to customize audio output for your products.
        ```

        \** **SSML text example**:

        ```xml
        <speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
            <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
            Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
            </voice>
        </speak>
        ```

## Export tuned audio

After you've reviewed your audio output and are satisfied with your tuning and adjustment, you can export the audio.

1. Select **Export** to create an audio creation task. 

   We recommend **Export to Audio Library**, because this option supports the long audio output and the full audio output experience. You can also download the audio to your local disk directly, but only the first 10 minutes are available.

1. Choose the output format for your tuned audio. The **supported audio formats and sample rates** are listed in the following table:

    | Format | 8 kHz sample rate | 16 kHz sample rate | 24 kHz sample rate | 48 kHz sample rate |
    |--- |--- |--- |--- |--- |
    | wav | riff-8khz-16bit-mono-pcm | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |riff-48khz-16bit-mono-pcm |
    | mp3 | N/A | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |audio-48khz-192kbitrate-mono-mp3 |
    | | |

1. To view the status of the task, select the **Export task** tab. 

   If the task fails, see the detailed information page for a full report.

1. When the task is complete, your audio is available for download on the **Audio Library** pane.

1. Select **Download**. Now you're ready to use your custom tuned audio in your apps or products.

## Add or remove Audio Content Creation users

If more than one user wants to use Audio Content Creation, you can grant them access to the Azure subscription and the Speech resource. If you add users to an Azure subscription, they can access all the resources under the Azure subscription. But if you add users to a Speech resource only, they'll have access only to the Speech resource and not to other resources under this Azure subscription. Users with access to the Speech resource can use the Audio Content Creation tool.

The users you grant access to need to set up a [Microsoft account](https://account.microsoft.com/account). If they don' have a Microsoft account, they can create one in just a few minutes. They can use their existing email and link it to a Microsoft account, or they can create and use an Outlook email address as a Microsoft account.

### Add users to a Speech resource

To add users to a Speech resource so that they can use Audio Content Creation, do the following:

1. In the [Azure portal](https://portal.azure.com/), search for and select **Cognitive Services**, and then  select the Speech resource that you want to add users to.
1. Select **Access control (IAM)**, select **Add**, and then select **Add role assignment (preview)** to open the **Add role assignment** pane. 
1. Select the **Role** tab, and then select the **Cognitive Service User** role. If you want to give a user ownership of this Speech resource, select the **Owner** role.
1. Select the **Members** tab, enter a user's email address and select the user's name in the directory. The email address must be linked to a Microsoft account that's trusted by Azure Active Directory. Users can easily sign up for a [Microsoft account](https://account.microsoft.com/account) by using their personal email address. 
1. Select the **Review + assign** tab, and then select **Review + assign** to assign the role to a user. 

Here is what happens next:

An email invitation is automatically sent to users. They can accept it by selecting **Accept invitation** > **Accept to join Azure** in their email. They're then redirected to the Azure portal. They don't need to take further action in the Azure portal. After a few moments, users are assigned the role at the Speech resource scope, which gives them access to this Speech resource. If users don't receive the invitation email, you can search for their account under **Role assignments** and go into their profile. Look for **Identity** > **Invitation accepted**, and select **(manage)** to resend the email invitation. You can also copy and send the invitation link to them. 

Users now visit or refresh the [Audio Content Creation](https://aka.ms/audiocontentcreation) product page, and sign in with their Microsoft account. They select **Audio Content Creation** block among all speech products. They choose the Speech resource in the pop-up window or in the settings at the upper right. 

If they can't find the available Speech resource, they can check to ensure that they're in the right directory. To do so, they select the account profile at the upper right and then select **Switch** next to **Current directory**. If there's more than one directory available, it means they have access to multiple directories. They can switch to different directories and go to **Settings** to see whether the right Speech resource is available. 

:::image type="content" source="media/audio-content-creation/add-role-first.png" alt-text="Add role dialog":::

Users who are in the same Speech resource will see each other's work in Audio Content Creation studio. If you want each individual user to have a unique and private workplace in Audio Content Creation, [create a new Speech resource](#step-2-create-a-speech-resource) for each user and give each user the unique access to the Speech resource.

### Remove users from a Speech resource

1. Search for **Cognitive services** in the Azure portal, select the Speech resource that you want to remove users from.
1. Select **Access control (IAM)**, and then select the **Role assignments** tab to view all the role assignments for this Speech resource.
1. Select the users you want to remove, select **Remove**, and then select **OK**.

    :::image type="content" source="media/audio-content-creation/remove-user.png" alt-text="Screenshot of the 'Remove' button on the 'Remove role assignments' pane.":::

### Enable users to grant access to others

If you want to allow a user to grant access to other users, you need to assign them the owner role for the Speech resource and set the user as the Azure directory reader.
1. Add the user as the owner of the Speech resource. For more information, see [Add users to a Speech resource](#add-users-to-a-speech-resource).

    :::image type="content" source="media/audio-content-creation/add-role.png" alt-text="Screenshot showing the 'Owner' role on the 'Add role assignment' pane. ":::

1. In the [Azure portal](https://portal.azure.com/), select the collapsed menu at the upper left, select **Azure Active Directory**, and then select **Users**.
1. Search for the user's Microsoft account, go to their detail page, and then select **Assigned roles**.
1. Select **Add assignments** > **Directory Readers**. If the **Add assignments** button is unavailable, it means that you don't have access. Only the global administrator of this directory can add assignments to users.

## See also

* [Long Audio API](./long-audio-api.md)

## Next steps

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
