---
title: Migration overview - Speech service
titleSuffix: Azure Cognitive Services
description: This document summarizes the benefits of migration from non-neural voice to neural voice.
services: cognitive-services
author: sally-baolian
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: v-baolianzou
---

# Migration overview

We're retiring two features from [Text-to-Speech](index-text-to-speech.yml) capabilities as detailed below.

## Custom voice (non-neural training)

> [!IMPORTANT]
> We are retiring the standard non-neural training tier of custom voice from March 1, 2021 through February 29, 2024. If you used a non-neural custom voice with your Speech resource prior to March 1, 2021 then you can continue to do so until February 29, 2024. All other Speech resources can only use custom neural voice. After February 29, 2024, the non-neural custom voices won't be supported with any Speech resource. 

Go to [this article](how-to-migrate-to-custom-neural-voice.md) to learn how to migrate to custom neural voice. 

Custom neural voice is a gated service. You need to create an Azure account and Speech service subscription (with S0 tier), and [apply](https://aka.ms/customneural) to use custom neural feature. After you've been granted access, you can visit the [Speech Studio portal](https://speech.microsoft.com/portal) and then select Custom Voice to get started. 

Even without an Azure account, you can listen to voice samples in [Speech Studio](https://aka.ms/customvoice) and determine the right voice for your business needs.

Go to the [pricing page](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) and check the pricing details. Custom voice (retired) is referred as **Custom**, and custom neural voice is referred as **Custom Neural**. 

## Prebuilt standard voice

> [!IMPORTANT]
> We are retiring the standard voices from September 1, 2021 through August 31, 2024. If you used a standard voice with your Speech resource prior to September 1, 2021 then you can continue to do so until August 31, 2024. All other Speech resources can only use prebuilt neural voices. You can choose from the supported [neural voice names](language-support.md#prebuilt-neural-voices). After August 31, the standard voices won't be supported with any Speech resource.

Go to [this article](how-to-migrate-to-prebuilt-neural-voice.md) to learn how to migrate to prebuilt neural voice.

Prebuilt neural voice is powered by deep neural networks. You need to create an Azure account and Speech service subscription. Then you can use the [Speech SDK](./get-started-text-to-speech.md) or visit the [Speech Studio portal](https://speech.microsoft.com/portal), and select prebuilt neural voices to get started. Listening to the voice sample without creating an Azure account, you can visit [here](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#overview) and determine the right voice for your business needs.

Go to the [pricing page](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) and check the pricing details. Prebuilt standard voice (retired) is referred as **Standard**, and prebuilt neural voice is referred as **Neural**. 

## Next steps

- [Try out prebuilt neural voice](text-to-speech.md)
- [Try out custom neural voice](custom-neural-voice.md)
