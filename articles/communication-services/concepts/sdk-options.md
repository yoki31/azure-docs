---
title: SDKs and REST APIs for Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Learn more about Azure Communication Services SDKs and REST APIs.
author: probableprime
manager: chpalm
services: azure-communication-services

ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
---
# SDKs and REST APIs

Azure Communication Services capabilities are conceptually organized into discrete areas based on their functional area. Most areas have fully open-sourced SDKs programmed against published REST APIs that you can use directly over the Internet. The Calling SDK uses proprietary network interfaces and is closed-source.

In the tables below we summarize these areas and availability of REST APIs and SDK libraries. We note if APIs and SDKs are intended for end-user clients or trusted service environments. APIs such as SMS should not be directly accessed by end-user devices in low trust environments.

Development of Calling and Chat applications can be accelerated by the  [Azure Communication Services UI library](./ui-library/ui-library-overview.md). The customizable UI library provides open-source UI components for Web and mobile apps, and a Microsoft Teams theme.

## SDKs
| Assembly | Protocols| Environment | Capabilities|
|--------|----------|---------|----------------------------------|
| Azure Resource Manager | [REST](/rest/api/communication/communicationservice)| Service| Provision and manage Communication Services resources|
| Common | N/A | Client & Service | Provides base types for other SDKs |
| Identity | [REST](/rest/api/communication/communicationidentity/communication-identity) | Service| Manage users, access tokens|
| Phone numbers| [REST](/rest/api/communication/phonenumbers) | Service| Acquire and manage phone numbers |
| SMS | [REST](/rest/api/communication/sms) | Service| Send and receive SMS messages|
| Chat | [REST](/rest/api/communication/) with proprietary signaling | Client & Service | Add real-time text chat to your applications |
| Calling | Proprietary transport | Client | Voice, video, screen-sharing, and other real-time communication |
| Calling Server | [REST](/rest/api/communication/callautomation/server-calls) | Service| Make and manage calls, play audio, and configure recording |
| Network Traversal | [REST](/api/communication/communication-network-traversal)| Service| Access TURN servers for low-level data transport |
| UI Library | N/A | Client | Production-ready UI components for chat and calling apps |

### Languages and publishing locations

Publishing locations for individual SDK packages are detailed below.

| Area | JavaScript | .NET | Python | Java SE | iOS | Android | Other|
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | [npm](https://www.npmjs.com/package/@azure/arm-communication) | [NuGet](https://www.NuGet.org/packages/Azure.ResourceManager.Communication)| [PyPi](https://pypi.org/project/azure-mgmt-communication/)| [Maven](https://search.maven.org/search?q=azure-resourcemanager-communication)| -| -| [Go via GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Common | [npm](https://www.npmjs.com/package/@azure/communication-common) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Common/)| N/A| [Maven](https://search.maven.org/search?q=a:azure-communication-common) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common) | -|
| Identity | [npm](https://www.npmjs.com/package/@azure/communication-identity) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Identity)| [PyPi](https://pypi.org/project/azure-communication-identity/)| [Maven](https://search.maven.org/search?q=a:azure-communication-identity) | -| -| -|
| Phone Numbers | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.PhoneNumbers)| [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)| [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers) | -| -| -|
| Chat | [npm](https://www.npmjs.com/package/@azure/communication-chat)| [NuGet](https://www.NuGet.org/packages/Azure.Communication.Chat) | [PyPi](https://pypi.org/project/azure-communication-chat/) | [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | -|
| SMS| [npm](https://www.npmjs.com/package/@azure/communication-sms) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Sms)| [PyPi](https://pypi.org/project/azure-communication-sms/) | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms) | -| -| -|
| Calling| [npm](https://www.npmjs.com/package/@azure/communication-calling) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Calling) | -| - | [GitHub](https://github.com/Azure/Communication/releases) | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)| -|
|Call Automation||[NuGet](https://www.NuGet.org/packages/Azure.Communication.CallingServer/)||[Maven](https://search.maven.org/artifact/com.azure/azure-communication-callingserver)
|Network Traversal| [npm](https://www.npmjs.com/package/@azure/communication-network-traversal)|[NuGet](https://www.NuGet.org/packages/Azure.Communication.NetworkTraversal/) | [PyPi](https://pypi.org/project/azure-communication-networktraversal/) | [Maven](https://search.maven.org/search?q=a:azure-communication-networktraversal) | -|- | - |
| UI Library| [npm](https://www.npmjs.com/package/@azure/communication-react) | - | - | - | - | - | [GitHub](https://github.com/Azure/communication-ui-library), [Storybook](https://azure.github.io/communication-ui-library/?path=/story/overview--page) |
| Reference Documentation | [docs](https://azure.github.io/azure-sdk-for-js/communication.html) | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)| -| [docs](http://azure.github.io/azure-sdk-for-java/communication.html) | [docs](/objectivec/communication-services/calling/)| [docs](/java/api/com.azure.android.communication.calling)| -|

### SDK platform support details

#### iOS and Android

- Communication Services iOS SDKs target iOS version 13+, and Xcode 11+.
- Android Java SDKs target Android API level 21+ and Android Studio 4.0+

#### .NET

Except for Calling, Communication Services packages target .NET Standard 2.0, which supports the platforms listed below.

Support via .NET Framework 4.6.1
- Windows 10, 8.1, 8 and 7
- Windows Server 2012 R2, 2012 and 2008 R2 SP1

Support via .NET Core 2.0:
- Windows 10 (1607+), 7 SP1+, 8.1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12+
- Linux multiple versions/distributions
- UWP 10.0.16299 (RS3) September 2017
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

The Calling package supports UWP apps build with .NET Native or C++/WinRT on:
- Windows 10 10.0.17763
- Windows Server 2019 10.0.17763

## REST APIs
Communication Services APIs are documented alongside other [Azure REST APIs in docs.microsoft.com](/rest/api/azure/). This documentation will tell you how to structure your HTTP messages and offers guidance for using [Postman](../tutorials/postman-tutorial.md). REST interface documentation is also published in Swagger format on [GitHub](https://github.com/Azure/azure-rest-api-specs).

### REST API Throttles
Certain REST APIs and corresponding SDK methods have throttle limits you should be mindful of. Exceeding these throttle limits will trigger a`429 - Too Many Requests` error response. These limits can be increased through [a request to Azure Support](../../azure-portal/supportability/how-to-create-azure-support-request.md).

| API| Throttle|
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [All Search Telephone Number Plan APIs](/rest/api/communication/phonenumbers) | 4 requests/day|
| [Purchase Telephone Number Plan](/rest/api/communication/phonenumbers/purchasephonenumbers) | 1 purchase a month|
| [Send SMS](/rest/api/communication/sms/send) | 200 requests/minute |

## API stability expectations

> [!IMPORTANT]
> This section provides guidance on REST APIs and SDKs marked **stable**. APIs marked pre-release, preview, or beta may be changed or deprecated **without notice**.

In the future we may retire versions of the Communication Services SDKs, and we may introduce breaking changes to our REST APIs and released SDKs. Azure Communication Services will *generally* follow two supportability policies for retiring service versions:

- You'll be notified at least three years before being required to change code due to a Communication Services interface change. All documented REST APIs and SDK APIs generally enjoy at least three years warning before interfaces are decommissioned.
- You'll be notified at least one year before having to update SDK assemblies to the latest minor version. These required updates shouldn't require any code changes because they're in the same major version. Using the latest SDK is especially important for the Calling and Chat libraries that real-time components that often require security and performance updates. We strongly encourage you to keep all your Communication Services SDKs updated.

### API and SDK decommissioning examples

**You've integrated the v24 version of the SMS REST API into your application. Azure Communication releases v25.**

You'll get three years warning before these APIs stop working and are forced to update to v25. This update might require a code change.

**You've integrated the v2.02 version of the Calling SDK into your application. Azure Communication releases v2.05.**

You may be required to update to the v2.05 version of the Calling SDK within 12 months of the release of v2.05. This should be a simple replacement of the artifact without requiring a code change because v2.05 is in the v2 major version and has no breaking changes.

## Next steps

For more information, see the following SDK overviews:

- [Calling SDK Overview](../concepts/voice-video-calling/calling-sdk-features.md)
- [Chat SDK Overview](../concepts/chat/sdk-features.md)
- [SMS SDK Overview](../concepts/sms/sdk-features.md)

To get started with Azure Communication Services:

- [Create an Azure Communication Services resource](../quickstarts/create-communication-resource.md)
- Generate [User Access Tokens](../quickstarts/access-tokens.md)
