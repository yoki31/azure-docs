---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: eur
ms.custom: devx-track-js
---

One of the core features of the Speech service is the ability to recognize and transcribe human speech (often called speech-to-text). In this quickstart, you learn how to use the Speech SDK in your apps and products to perform high-quality speech-to-text conversion.

## Skip to samples on GitHub

If you want to skip straight to sample code, see the [JavaScript quickstart samples](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) on GitHub.

Alternatively, see the [React sample](https://github.com/Azure-Samples/AzureSpeechReactSample) to learn how to use the Speech SDK in a browser-based environment.

## Prerequisites

This article assumes that you have an Azure account and a Speech service subscription. If you don't have an account and a subscription, [try the Speech service for free](../../../overview.md#try-the-speech-service-for-free).

### Install the Speech SDK

Before you can do anything, you need to install the Speech SDK for Node.js. If you just want the package name to install, run `npm install microsoft-cognitiveservices-speech-sdk`. For guided installation instructions, see [Set up the development environment](../../../quickstarts/setup-platform.md?pivots=programming-language-javascript&tabs=dotnet%2clinux%2cjre%2cnodejs).

Use the following `require` statement to import the SDK:

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

For more information on `require`, see the [require documentation](https://nodejs.org/en/knowledge/getting-started/what-is-require/).

## Create a speech configuration

To call the Speech service by using the Speech SDK, you need to create a [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) instance. This class includes information about your subscription, like your key and associated location/region, endpoint, host, or authorization token. 

Create a `SpeechConfig` instance by using your key and location/region. For more information, see [Find keys and location/region](../../../overview.md#find-keys-and-locationregion).

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
```

You can initialize `SpeechConfig` in a few other ways:

* With an endpoint: pass in a Speech service endpoint. A key or authorization token is optional.
* With a host: pass in a host address. A key or authorization token is optional.
* With an authorization token: pass in an authorization token and the associated location/region.

> [!NOTE]
> Regardless of whether you're performing speech recognition, speech synthesis, translation, or intent recognition, you'll always create a configuration.

## Recognize speech from a microphone (browser only)

Recognizing speech from a microphone is *not supported in Node.js*. It's supported only in a browser-based JavaScript environment. For more information, see the [React sample](https://github.com/Azure-Samples/AzureSpeechReactSample) and the [implementation of speech-to-text from a microphone](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29) on GitHub.

> [!NOTE]
> If you want to use a *specific* audio input device, you need to specify the device ID in `AudioConfig`. Learn [how to get the device ID](../../../how-to-select-audio-input-devices.md) for your audio input device.

## Recognize speech from a file 

To recognize speech from an audio file, create an `AudioConfig` instance by using `fromWavFileInput()`, which accepts a `Buffer` object. Then initialize [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) by passing `audioConfig` and `speechConfig`.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromFile() {
    let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);

    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

## Recognize speech from an in-memory stream

For many use cases, your audio data will likely come from blob storage. Or it will already be in memory as [`ArrayBuffer`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) or similar raw data structure. The following code:

* Creates a push stream by using `createPushStream()`.
* Reads a .wav file by using `fs.createReadStream` for demonstration purposes. If you already have audio data in `ArrayBuffer`, you can skip directly to writing the content to the input stream.
* Creates an audio configuration by using the push stream.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromStream() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromStream();
```

Using a push stream as input assumes that the audio data is a raw PCM that skips any headers. The API will still work in certain cases if the header has not been skipped. But for the best results, consider implementing logic to read off the headers so that `fs` starts at the *start of the audio data*.

## Handle errors

The previous examples simply get the recognized text from `result.text`. To handle errors and other responses, you need to write some code to handle the result. The following code evaluates the [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) property and:

* Prints the recognition result: `ResultReason.RecognizedSpeech`.
* If there is no recognition match, informs the user: `ResultReason.NoMatch`.
* If an error is encountered, prints the error message: `ResultReason.Canceled`.

```javascript
switch (result.reason) {
    case sdk.ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case sdk.ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case sdk.ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == sdk.CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the key and location/region info?");
        }
        break;
    }
```

## Use continuous recognition

The previous examples use at-start recognition, which recognizes a single utterance. The end of a single utterance is determined by listening for silence at the end or until a maximum of 15 seconds of audio is processed.

In contrast, you can use continuous recognition when you want to control when to stop recognizing. It requires you to subscribe to the `Recognizing`, `Recognized`, and `Canceled` events to get the recognition results. To stop recognition, you must call [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync). Here's an example of how continuous recognition is performed on an audio input file.

Start by defining the input and initializing [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer):

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

Next, subscribe to the events sent from [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer):

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): Signal for events that contain intermediate recognition results.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): Signal for events that contain final recognition results, which indicate a successful recognition attempt.
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): Signal for events that indicate the end of a recognition session (operation).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): Signal for events that contain canceled recognition results. These results indicate a recognition attempt that was canceled as a result or a direct cancellation request. Alternatively, they indicate a transport or protocol failure.

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == sdk.ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == sdk.ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == sdk.CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the key and location/region info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

With everything set up, call [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) to start recognizing:

```javascript
recognizer.startContinuousRecognitionAsync();

// Make the following call at some point to stop recognition:
// recognizer.stopContinuousRecognitionAsync();
```

### Dictation mode

When you're using continuous recognition, you can enable dictation processing by using the corresponding function. This mode will cause the speech configuration instance to interpret word descriptions of sentence structures such as punctuation. For example, the utterance "Do you live in town question mark" would be interpreted as the text "Do you live in town?".

To enable dictation mode, use the [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) method on [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig):

```javascript
speechConfig.enableDictation();
```

## Change the source language

A common task for speech recognition is specifying the input (or source) language. The following example shows how you would change the input language to Italian. In your code, find your [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) instance and add this line directly below it:

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

The [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) property expects a language-locale format string. You can provide any value in the **Locale** column in the [list of supported locales/languages](../../../language-support.md).

