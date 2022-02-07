---
title: Specify source language for speech to text
titleSuffix: Azure Cognitive Services
description: The Speech SDK allows you to specify the source language when you convert speech to text. This article describes how to use the FromConfig and SourceLanguageConfig methods to let the Speech service know the source language and provide a custom model target.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.devlang: cpp, csharp, java, javascript, objective-c, python
ms.custom: "devx-track-js, devx-track-csharp"
---

# Specify source language for speech-to-text

In this article, you'll learn how to specify the source language for an audio input passed to the Speech SDK for speech recognition. The example code that's provided specifies a custom speech model for improved recognition.

::: zone pivot="programming-language-csharp"

## Specify source language in C#

In the following example, the source language is provided explicitly as a parameter by using the `SpeechRecognizer` construct:

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In the following example, the source language is provided by using `SourceLanguageConfig`. Then, `sourceLanguageConfig` is passed as a parameter to the `SpeechRecognizer` construct.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In the following example, the source language and custom endpoint are provided by using `SourceLanguageConfig`. Then, `sourceLanguageConfig` is passed as a parameter to the `SpeechRecognizer` construct.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> The `SpeechRecognitionLanguage` and `EndpointId` set methods are deprecated from the `SpeechConfig` class in C#. The use of these methods is discouraged. Don't use them when you create a `SpeechRecognizer` construct.

::: zone-end

::: zone pivot="programming-language-cpp"

## Specify source language in C++

In the following example, the source language is provided explicitly as a parameter by using the `FromConfig` method.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

In the following example, the source language is provided by using `SourceLanguageConfig`. Then, `sourceLanguageConfig` is passed as a parameter to `FromConfig` when you create the `recognizer` construct.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

In the following example, the source language and custom endpoint are provided by using `SourceLanguageConfig`. Then, `sourceLanguageConfig` is passed as a parameter to `FromConfig` when you create the `recognizer` construct.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` and `SetEndpointId` are deprecated methods from the `SpeechConfig` class in C++ and Java. The use of these methods is discouraged. Don't use them when you create a `SpeechRecognizer` construct.

::: zone-end

::: zone pivot="programming-language-java"

## Specify source language in Java

In the following example, the source language is provided explicitly when you create a new `SpeechRecognizer` construct.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In the following example, the source language is provided by using `SourceLanguageConfig`. Then, `sourceLanguageConfig` is passed as a parameter when you create a new `SpeechRecognizer` construct.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In the following example, the source language and custom endpoint are provided by using `SourceLanguageConfig`. Then, `sourceLanguageConfig` is passed as a parameter when you create a new `SpeechRecognizer` construct.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` and `setEndpointId` are deprecated methods from the `SpeechConfig` class in C++ and Java. The use of these methods is discouraged. Don't use them when you create a `SpeechRecognizer` construct.

::: zone-end

::: zone pivot="programming-language-python"

## Specify source language in Python

In the following example, the source language is provided explicitly as a parameter by using the `SpeechRecognizer` construct.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

In the following example, the source language is provided by using `SourceLanguageConfig`. Then, `SourceLanguageConfig` is passed as a parameter to the `SpeechRecognizer` construct.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

In the following example, the source language and custom endpoint are provided by using `SourceLanguageConfig`. Then, `SourceLanguageConfig` is passed as a parameter to the `SpeechRecognizer` construct.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> The `speech_recognition_language` and `endpoint_id` properties are deprecated from the `SpeechConfig` class in Python. The use of these properties is discouraged. Don't use them when you create a `SpeechRecognizer` construct.

::: zone-end

::: zone pivot="programming-language-more"

## Specify source language in JavaScript

The first step is to create a `SpeechConfig` construct:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Next, specify the source language of your audio with `speechRecognitionLanguage`:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

If you're using a custom model for recognition, you can specify the endpoint with `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## Specify source language in Objective-C

In the following example, the source language is provided explicitly as a parameter by using the `SPXSpeechRecognizer` construct.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

In the following example, the source language is provided by using `SPXSourceLanguageConfiguration`. Then, `SPXSourceLanguageConfiguration` is passed as a parameter to the `SPXSpeechRecognizer` construct.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

In the following example, the source language and custom endpoint are provided by using `SPXSourceLanguageConfiguration`. Then, `SPXSourceLanguageConfiguration` is passed as a parameter to the `SPXSpeechRecognizer` construct.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> The `speechRecognitionLanguage` and `endpointId` properties are deprecated from the `SPXSpeechConfiguration` class in Objective-C. The use of these properties is discouraged. Don't use them when you create a `SPXSpeechRecognizer` construct.

::: zone-end

## See also

For a list of supported languages and locales for speech-to-text, see [Language support](language-support.md).

## Next steps

See the [Speech SDK reference documentation](speech-sdk.md).
