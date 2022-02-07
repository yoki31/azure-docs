---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: eur
---
One of the core features of the Speech service is the ability to recognize and transcribe human speech (often called speech-to-text). In this quickstart, you learn how to use the Speech SDK in your apps and products to perform high-quality speech-to-text conversion.

## Skip to samples on GitHub

If you want to skip straight to sample code, see the [C++ quickstart samples](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) on GitHub.

## Prerequisites

This article assumes that you have an Azure account and a Speech service subscription. If you don't have an account and a subscription, [try the Speech service for free](../../../overview.md#try-the-speech-service-for-free).

### Install the Speech SDK

Before you can do anything, you need to install the Speech SDK. Depending on your platform, use the following instructions:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## Create a speech configuration

To call the Speech service using the Speech SDK, you need to create a [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) instance. This class includes information about your subscription, like your key and associated location/region, endpoint, host, or authorization token. 

Create a `SpeechConfig` instance by using your key and region. For more information, see [Find keys and location/region](../../../overview.md#find-keys-and-locationregion).

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
```

You can initialize `SpeechConfig` in a few other ways:

* With an endpoint: pass in a Speech service endpoint. A key or authorization token is optional.
* With a host: pass in a host address. A key or authorization token is optional.
* With an authorization token: pass in an authorization token and the associated region.

> [!NOTE]
> Regardless of whether you're performing speech recognition, speech synthesis, translation, or intent recognition, you'll always create a configuration.

## Recognize speech from a microphone

To recognize speech by using your device microphone, create an [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) instance by using `FromDefaultMicrophoneInput()`. Then initialize [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer) by passing `audioConfig` and `config`.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

If you want to use a *specific* audio input device, you need to specify the device ID in `AudioConfig`. Learn [how to get the device ID](../../../how-to-select-audio-input-devices.md) for your audio input device.

## Recognize speech from a file

If you want to recognize speech from an audio file instead of using a microphone, you still need to create an `AudioConfig` instance. But instead of calling `FromDefaultMicrophoneInput()`, you call `FromWavFileInput()` and pass the file path:

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## Recognize speech by using the Recognizer class

The [Recognizer class](/cpp/cognitive-services/speech/speechrecognizer) for the Speech SDK for C++ exposes a few methods that you can use for speech recognition.

### At-start recognition

At-start recognition asynchronously recognizes a single utterance. The end of a single utterance is determined by listening for silence at the end or until a maximum of 15 seconds of audio is processed. Here's an example of asynchronous at-start recognition via [`RecognizeOnceAsync`](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync):

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

You need to write some code to handle the result. This sample evaluates [`result->Reason`](/cpp/cognitive-services/speech/recognitionresult#reason) and:

* Prints the recognition result: `ResultReason::RecognizedSpeech`.
* If there is no recognition match, informs the user: `ResultReason::NoMatch`.
* If an error is encountered, prints the error message: `ResultReason::Canceled`.

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the speech key and location/region info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### Continuous recognition

Continuous recognition is a bit more involved than at-start recognition. It requires you to subscribe to the `Recognizing`, `Recognized`, and `Canceled` events to get the recognition results. To stop recognition, you must call [StopContinuousRecognitionAsync](/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Here's an example of how continuous recognition is performed on an audio input file.

Start by defining the input and initializing [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer):

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Next, create a variable to manage the state of speech recognition. Declare `promise<void>` because at the start of recognition, you can safely assume that it's not finished:

```cpp
promise<void> recognitionEnd;
```

Next, subscribe to the events that [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer) sends:

* [`Recognizing`](/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Signal for events that contain intermediate recognition results.
* [`Recognized`](/cpp/cognitive-services/speech/asyncrecognizer#recognized): Signal for events that contain final recognition results, which indicate a successful recognition attempt.
* [`SessionStopped`](/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Signal for events that indicate the end of a recognition session (operation).
* [`Canceled`](/cpp/cognitive-services/speech/asyncrecognizer#canceled): Signal for events that contain canceled recognition results. These results indicate a recognition attempt that was canceled as a result or a direct cancellation request. Alternatively, they indicate a transport or protocol failure.

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the speech key and location/region info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

With everything set up, call [`StopContinuousRecognitionAsync`](/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync) to start recognizing:

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### Dictation mode

When you're using continuous recognition, you can enable dictation processing by using the corresponding function. This mode will cause the speech configuration instance to interpret word descriptions of sentence structures such as punctuation. For example, the utterance "Do you live in town question mark" would be interpreted as the text "Do you live in town?".

To enable dictation mode, use the [`EnableDictation`](/cpp/cognitive-services/speech/speechconfig#enabledictation) method on [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig):

```cpp
config->EnableDictation();
```

## Change the source language

A common task for speech recognition is specifying the input (or source) language. The following example shows how you would change the input language to German. In your code, find your [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) instance and add this line directly below it:

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) is a parameter that takes a string as an argument. You can provide any value in the [list of supported locales/languages](../../../language-support.md).

