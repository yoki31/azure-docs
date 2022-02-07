---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: eric-urban
ms.custom: devx-track-js
---

One of the core features of the Speech service is the ability to recognize human speech and translate it to other languages. In this quickstart, you learn how to use the Speech SDK in your apps and products to perform high-quality speech translation. This quickstart covers these topics:

* Translating speech to text
* Translating speech to multiple target languages
* Performing direct speech-to-speech translation

## Skip to samples on GitHub

If you want to skip straight to sample code, see the [JavaScript quickstart samples](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/translate-speech-to-text) on GitHub.

## Prerequisites

This article assumes that you have an Azure account and a Speech service subscription. If you don't have an account and a subscription, [try the Speech service for free](../../../overview.md#try-the-speech-service-for-free).

### Install the Speech SDK

Before you can do anything, you need to install the <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK for JavaScript</a>. Depending on your platform, use the following instructions:
- [Node.js](../../../speech-sdk.md?tabs=nodejs#get-the-speech-sdk)
- [Web browser](../../../speech-sdk.md?tabs=browser#get-the-speech-sdk)

Additionally, depending on the target environment, use one of the following:

# [script](#tab/script)

Download and extract the <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK for JavaScript </a> *microsoft.cognitiveservices.speech.sdk.bundle.js* file. Place it in a folder that your HTML file can access.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> If you're targeting a web browser and using the `<script>` tag, you don't need the `sdk` prefix. The `sdk` prefix is an alias that's used to name the `require` module.

# [import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

For more information on `import`, see <a href="https://javascript.info/import-export" target="_blank">Export and import</a>.

# [require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

For more information on `require`, see <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">What is require?</a>.

---

## Create a translation configuration

To call the translation service by using the Speech SDK, you need to create a [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) instance. This class includes information about your subscription, like your key and associated region, endpoint, host, or authorization token.

> [!NOTE]
> Regardless of whether you're performing speech recognition, speech synthesis, translation, or intent recognition, you'll always create a configuration.

You can initialize `SpeechTranslationConfig` in a few ways:

* With a subscription: pass in a key and the associated region.
* With an endpoint: pass in a Speech service endpoint. A key or authorization token is optional.
* With a host: pass in a host address. A key or authorization token is optional.
* With an authorization token: pass in an authorization token and the associated region.

Let's look at how you create a `SpeechTranslationConfig` instance by using a key and region. Get these credentials by following steps in [Try the Speech service for free](../../../overview.md#try-the-speech-service-for-free).

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## Initialize a translator

After you've created a [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) instance, the next step is to initialize [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer). When you initialize `TranslationRecognizer`, you need to pass it your `speechTranslationConfig` instance. The configuration object provides the credentials that the translation service requires to validate your request.

If you're translating speech provided through your device's default microphone, here's what `TranslationRecognizer` should look like:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

If you want to specify the audio input device, then you need to create an [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) class instance and provide the `audioConfig` parameter when initializing `TranslationRecognizer`.

> [!TIP]
> [Learn how to get the device ID for your audio input device](../../../how-to-select-audio-input-devices.md).

Reference the `AudioConfig` object as follows:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

If you want to provide an audio file instead of using a microphone, you still need to provide an `audioConfig` parameter. However, you can do this only when you're targeting Node.js. When you create an `AudioConfig` class instance, instead of calling `fromDefaultMicrophoneInput`, you call `fromWavFileOutput` and pass the `filename` parameter:

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## Translate speech

The [TranslationRecognizer class](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) for the Speech SDK for JavaScript exposes methods that you can use for speech translation:

* *At-start translation (async)*: Performs translation in a nonblocking (asynchronous) mode. It translates a single utterance. It determines the end of a single utterance by listening for silence at the end or until a maximum of 15 seconds of audio is processed.
* *Continuous translation (async)*: Asynchronously initiates a continuous translation operation. The user registers to events and handles various application states. To stop asynchronous continuous translation, call [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync).

To learn more about how to choose a speech recognition mode, see [Get started with speech-to-text](../../../get-started-speech-to-text.md).

### Specify a target language

To translate, you must specify both a source language and at least one target language.

You can choose a source language by using a locale listed in the [Speech translation table](../../../language-support.md#speech-translation). Find your options for translated language at the same link. 

Your options for target languages differ when you want to view text or
you want to hear synthesized translated speech. To translate from English to German, modify the translation configuration object:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### At-start recognition

Here's an example of asynchronous at-start translation via [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizeonceasync):

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

You need to write some code to handle the result. This sample evaluates [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult) for a translation to German:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Your code can also handle updates provided while the translation is processing. You can use these updates to provide visual feedback about the translation progress. [This JavaScript
Node.js example](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) shows these kinds of updates. The following code also displays details produced during the translation process:

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### Continuous translation

Continuous translation is a bit more involved than at-start recognition. It requires you to subscribe to the `recognizing`, `recognized`, and `canceled` events to get the recognition results. To stop translation, you must call [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync). 

Here's an example of how continuous translation is performed on an audio input file. Let's start by defining the input and initializing [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer):

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

In the following code, you subscribe to the events sent from `TranslationRecognizer`:

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizing): Signal for events that contain intermediate translation results.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognized): Signal for events that contain final translation results. These results indicate a successful translation attempt.
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#sessionstopped): Signal for events that indicate the end of a translation session (operation).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#canceled): Signal for events that contain canceled translation results. These events indicate a translation attempt that was canceled as a result or a direct cancellation. Alternatively, they indicate a transport or protocol failure.

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
    }
};
recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);
    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }
    recognizer.stopContinuousRecognitionAsync();
};
recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

With everything set up, you can call [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync):

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call. Stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## Choose a source language

A common task for speech translation is specifying the input (or source) language. The following example shows how you would change the input language to Italian. In your code, find your [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) instance and add the following line directly below it:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

The [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig#speechrecognitionlanguage) property expects a language-locale format string. You can provide any value in the **Locale** column in the [list of supported locales/languages](../../../language-support.md).

## Choose one or more target languages

The Speech SDK can translate to multiple target languages in parallel. 
The available target languages are somewhat different from the source language list. You specify target languages by using a language code, rather than a locale.

For a list of language codes for text targets, see 
[the speech translation table on the language support page](../../../language-support.md#speech-translation). You can also find details about translation to synthesized languages there.

The following code adds German as a target language:

```javascript
translationConfig.addTargetLanguage("de");
```

Because multiple target language translations are possible, your code must specify the target language when examining the result. The following code gets translation results for German:

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
