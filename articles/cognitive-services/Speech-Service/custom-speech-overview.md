---
title: Custom Speech overview - Speech service
titleSuffix: Azure Cognitive Services
description: Custom Speech is a set of online tools that allows you to evaluate and improve the Microsoft speech-to-text accuracy for your applications, tools, and products. 
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2022
ms.author: eur
ms.custom: contperf-fy21q2, references_regions
---

# What is Custom Speech?

With Custom Speech, you can evaluate and improve the Microsoft speech-to-text accuracy for your applications and products. Follow the links in this article to start creating a custom speech-to-text experience.

## What's in Custom Speech?

Before you can do anything with Custom Speech, you'll need an Azure account and a Speech service subscription. After you have an account, you can prep your data, train and test your models, inspect recognition quality, evaluate accuracy, and ultimately deploy and use the custom speech-to-text model.

This diagram highlights the pieces that make up the [Custom Speech area of the Speech Studio](https://aka.ms/speechstudio/customspeech).

![Diagram that highlights the components that make up the Custom Speech area of the Speech Studio.](./media/custom-speech/custom-speech-overview.png)

Here's more information about the sequence of steps that the diagram shows:

1. [Subscribe and create a project](#set-up-your-azure-account). Create an Azure account and subscribe to the Speech service. This unified subscription gives you access to speech-to-text, text-to-speech, speech translation, and the [Speech Studio](https://speech.microsoft.com/customspeech). Then use your Speech service subscription to create your first Custom Speech project.

1. [Upload test data](./how-to-custom-speech-test-and-train.md). Upload test data (audio files) to evaluate the Microsoft speech-to-text offering for your applications, tools, and products.

1. [Inspect recognition quality](how-to-custom-speech-inspect-data.md). Use the [Speech Studio](https://speech.microsoft.com/customspeech) to play back uploaded audio and inspect the speech recognition quality of your test data. For quantitative measurements, see [Inspect data](how-to-custom-speech-inspect-data.md).

1. [Evaluate and improve accuracy](how-to-custom-speech-evaluate-data.md). Evaluate and improve the accuracy of the speech-to-text model. The [Speech Studio](https://speech.microsoft.com/customspeech) provides a *word error rate*, which you can use to determine if additional training is required. If you're satisfied with the accuracy, you can use the Speech service APIs directly. If you want to improve accuracy by a relative average of 5 through 20 percent, use the **Training** tab in the portal to upload additional training data, like human-labeled transcripts and related text.

1. [Train and deploy a model](how-to-custom-speech-train-model.md). Improve the accuracy of your speech-to-text model by providing written transcripts (from 10 to 1,000 hours), and related text (<200 MB), along with your audio test data. This data helps to train the speech-to-text model. After training, retest. If you're satisfied with the result, you can deploy your model to a custom endpoint.

## Set up your Azure account

You need to have an Azure account and Speech service subscription before you can use the [Speech Studio](https://speech.microsoft.com/customspeech) to create a custom model. If you don't have an account and subscription, [try the Speech service for free](overview.md#try-the-speech-service-for-free).

If you plan to train a custom model with audio data, pick one of the following regions that have dedicated hardware available for training. This reduces the time it takes to train a model and allows you to use more audio for training. In these regions, the Speech service will use up to 20 hours of audio for training; in other regions, it will only use up to 8 hours.

* Australia East
* Canada Central
* Central India
* East US
* East US 2
* North Central US
* North Europe
* South Central US
* Southeast Asia
* UK South
* US Gov Arizona
* US Gov Virginia
* West Europe
* West US 2

After you create an Azure account and a Speech service subscription, sign in to the [Speech Studio](https://speech.microsoft.com/customspeech), and connect your subscription.

1. Sign in to the [Speech Studio](https://aka.ms/speechstudio/customspeech).
1. Select the subscription in which you need to work, and create a speech project.
1. If you want to modify your subscription, select the cog icon in the top menu.

## Create a project

Content like data, models, tests, and endpoints are organized into *projects* in the [Speech Studio](https://speech.microsoft.com/customspeech). Each project is specific to a domain and country or language. For example, you might create a project for call centers that use English in the United States.

To create your first project, select **Speech-to-text/Custom speech**, and then select **New Project**. Follow the instructions provided by the wizard to create your project. After you create a project, you should see four tabs: **Data**, **Testing**, **Training**, and **Deployment**. Use the links provided in [Next steps](#next-steps) to learn how to use each tab.

## Model and endpoint lifecycle

Older models typically become less useful over time because the newest model usually has higher accuracy. Therefore, base models as well as custom models and endpoints created through the portal are subject to expiration after one year for adaptation, and two years for decoding. For more information, see [Model and endpoint lifecycle](./how-to-custom-speech-model-and-endpoint-lifecycle.md).

## Next steps

* [Prepare and test your data](./how-to-custom-speech-test-and-train.md)
* [Evaluate and improve model accuracy](how-to-custom-speech-evaluate-data.md)
* [Train and deploy a model](how-to-custom-speech-train-model.md)
