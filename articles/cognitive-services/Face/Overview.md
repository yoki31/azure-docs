---
title: What is the Azure Face service?
titleSuffix: Azure Cognitive Services
description: The Azure Face service provides AI algorithms that you use to detect, recognize, and analyze human faces in images.
author: PatrickFarley
manager: nitinme

ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: facial recognition, facial recognition software, facial analysis, face matching, face recognition app, face search by image, facial recognition search
#Customer intent: As the developer of an app that deals with images of humans, I want to learn what the Face service does so I can determine if I should use its features.
---

# What is the Azure Face service?

> [!WARNING]
> On June 11, 2020, Microsoft announced that it will not sell facial recognition technology to police departments in the United States until strong regulation, grounded in human rights, has been enacted. As such, customers may not use facial recognition features or functionality included in Azure Services, such as Face or Video Indexer, if a customer is, or is allowing use of such services by or for, a police department in the United States. When you create a new Face resource, you must acknowledge and agree in the Azure Portal that you will not use the service by or for a police department in the United States and that you have reviewed the Responsible AI documentation and will use this service in accordance with it.

The Azure Face service provides AI algorithms that detect, recognize, and analyze human faces in images. Facial recognition software is important in many different scenarios, such as identity verification, touchless access control, and face blurring for privacy.

This documentation contains the following types of articles:
* The [quickstarts](./Quickstarts/client-libraries.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](./concepts/face-detection.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./enrollment-overview.md) are longer guides that show you how to use this service as a component in broader business solutions.

## Example use cases

Identity verification: Verify someone's identity against a government-issued ID card like a passport or driver's license or other enrollment image. You can use this verification to grant access to digital or physical services or recover an account. Specific access scenarios include opening a new account, verifying a worker, or administering an online assessment. Identity verification can be done once when a person is onboarded, and repeated when they access a digital or physical service.

Touchless access control: Compared to today’s methods like cards or tickets, opt-in face identification enables an enhanced access control experience while reducing the hygiene and security risks from card sharing, loss, or theft. Facial recognition assists the check-in process with a human in the loop for check-ins in airports, stadiums, theme parks, buildings, reception kiosks at offices, hospitals, gyms, clubs, or schools.

Face redaction: Redact or blur detected faces of people recorded in a video to protect their privacy.


## Face detection and analysis

Face detection is required as a first step in all the other scenarios. The Detect API detects human faces in an image and returns the rectangle coordinates of their locations. It also returns a unique ID that represents the stored face data. This is used in later operations to identify or verify faces.

Optionally, face detection can extract a set of face-related attributes, such as head pose, age, emotion, facial hair, and glasses. These attributes are general predictions, not actual classifications. Some attributes are useful to ensure that your application is getting high-quality face data when users add themselves to a Face service. For example, your application could advise users to take off their sunglasses if they're wearing sunglasses.

> [!NOTE]
> The face detection feature is also available through the [Computer Vision service](../computer-vision/overview.md). However, if you want to use other Face operations like Identify, Verify, Find Similar, or Face grouping, you should use this service instead.

For more information on face detection and analysis, see the [Face detection](concepts/face-detection.md) concepts article. Also see the [Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) reference documentation.


## Identity verification

Modern enterprises and apps can use the Face identification and Face verification operations to verify that a user is who they claim to be.

### Identification

Face identification can address "one-to-many" matching of one face in an image to a set of faces in a secure repository. Match candidates are returned based on how closely their face data matches the query face. This scenario is used in granting building or airport access to a certain group of people or verifying the user of a device.

The following image shows an example of a database named `"myfriends"`. Each group can contain up to 1 million different person objects. Each person object can have up to 248 faces registered.

![A grid with three columns for different people, each with three rows of face images](./Images/person.group.clare.jpg)

After you create and train a group, you can do identification against the group with a new detected face. If the face is identified as a person in the group, the person object is returned.

### Verification

The verification operation answers the question, "Do these two faces belong to the same person?". 

Verification is also a "one-to-one" matching of a face in an image to a single face from a secure repository or photo to verify that they're the same individual. Verification can be used for Identity Verification, such as a banking app that enables users to open a credit account remotely by taking a new picture of themselves and sending it with a picture of their photo ID.

For more information about identity verification, see the [Facial recognition](concepts/face-recognition.md) concepts guide or the [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) and [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) API reference documentation.


## Find similar faces

The Find Similar operation does face matching between a target face and a set of candidate faces, finding a smaller set of faces that look similar to the target face. This is useful for doing a face search by image. 

The service supports two working modes, **matchPerson** and **matchFace**. The **matchPerson** mode returns similar faces after filtering for the same person by using the [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). The **matchFace** mode ignores the same-person filter. It returns a list of similar candidate faces that may or may not belong to the same person.

The following example shows the target face:

![A woman smiling](./Images/FaceFindSimilar.QueryFace.jpg)

And these images are the candidate faces:

![Five images of people smiling. Images A and B show the same person.](./Images/FaceFindSimilar.Candidates.jpg)

To find four similar faces, the **matchPerson** mode returns A and B, which show the same person as the target face. The **matchFace** mode returns A, B, C, and D, which is exactly four candidates, even if some aren't the same person as the target or have low similarity. For more information, see the [Facial recognition](concepts/face-recognition.md) concepts guide or the [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) reference documentation.

## Group faces

The Group operation divides a set of unknown faces into several smaller groups based on similarity. Each group is a disjoint proper subset of the original set of faces. It also returns a single "messyGroup" array that contains the face IDs for which no similarities were found.

All of the faces in a returned group are likely to belong to the same person, but there can be several different groups for a single person. Those groups are differentiated by another factor, such as expression, for example. For more information, see the [Facial recognition](concepts/face-recognition.md) concepts guide or the [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) reference documentation.

## Sample apps

The following sample applications show a few ways to use the Face service:

- [FamilyNotes UWP app](https://github.com/Microsoft/Windows-appsample-familynotes) is a Universal Windows Platform (UWP) app that uses face identification along with speech, Cortana, ink, and camera in a family note-sharing scenario.

## Data privacy and security

As with all of the Cognitive Services resources, developers who use the Face service must be aware of Microsoft's policies on customer data. For more information, see the [Cognitive Services page](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) on the Microsoft Trust Center.

## Next steps

Follow a quickstart to code the basic components of a face recognition app in the language of your choice.

- [Client library quickstart](quickstarts/client-libraries.md).
