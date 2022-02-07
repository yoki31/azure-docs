---
title: Use Azure Cognitive Services Containers on-premises
titleSuffix: Azure Cognitive Services
description: Learn how to use Docker containers to use Cognitive Services on-premises.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
ms.service: cognitive-services
ms.topic: article
ms.date: 01/12/2022
ms.author: aahi
keywords: on-premises, Docker, container, Kubernetes
#Customer intent: As a potential customer, I want to know more about how Cognitive Services provides and supports Docker containers for each service.
---

# Azure Cognitive Services containers

Azure Cognitive Services provides several [Docker containers](https://www.docker.com/what-container) that let you use the same APIs that are available in Azure, on-premises. Using these containers gives you the flexibility to bring Cognitive Services closer to your data for compliance, security or other operational reasons. Container support is currently available for a subset of Azure Cognitive Services.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Containerization is an approach to software distribution in which an application or service, including its dependencies & configuration, is packaged together as a container image. With little or no modification, a container image can be deployed on a container host. Containers are isolated from each other and the underlying operating system, with a smaller footprint than a virtual machine. Containers can be instantiated from container images for short-term tasks, and removed when no longer needed.

## Features and benefits

- **Immutable infrastructure**: Enable DevOps teams' to leverage a consistent and reliable set of known system parameters, while being able to adapt to change. Containers provide the flexibility to pivot within a predictable ecosystem and avoid configuration drift.
- **Control over data**: Choose where your data gets processed by Cognitive Services. This can be essential if you can't send data to the cloud but need access to Cognitive Services APIs. Support consistency in hybrid environments – across data, management, identity, and security.
- **Control over model updates**: Flexibility in versioning and updating of models deployed in their solutions.
- **Portable architecture**: Enables the creation of a portable application architecture that can be deployed on Azure, on-premises and the edge. Containers can be deployed directly to [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), or to a [Kubernetes](https://kubernetes.io/) cluster deployed to [Azure Stack](/azure-stack/operator). For more information, see [Deploy Kubernetes to Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **High throughput / low latency**: Provide customers the ability to scale for high throughput and low latency requirements by enabling Cognitive Services to run physically close to their application logic and data. Containers do not cap transactions per second (TPS) and can be made to scale both up and out to handle demand if you provide the necessary hardware resources.
- **Scalability**: With the ever growing popularity of containerization and container orchestration software, such as Kubernetes; scalability is at the forefront of technological advancements. Building on a scalable cluster foundation, application development caters to high availability.

## Containers in Azure Cognitive Services

Azure Cognitive Services containers provide the following set of Docker containers, each of which contains a subset of functionality from services in Azure Cognitive Services. You can find instructions and image locations in the tables below. A list of [container images](containers/container-image-tags.md) is also available.

### Decision containers

| Service |  Container | Description | Availability |
|--|--|--|--|
| [Anomaly detector][ad-containers] | **Anomaly Detector** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | The Anomaly Detector API enables you to monitor and detect abnormalities in your time series data with machine learning. | Generally available |

### Language containers

| Service |  Container | Description | Availability |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([image](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Loads a trained or published Language Understanding model, also known as a LUIS app, into a docker container and provides access to the query predictions from the container's API endpoints. You can collect query logs from the container and upload these back to the [LUIS portal](https://www.luis.ai) to improve the app's prediction accuracy. | Generally available |
| [Language service][ta-containers-keyphrase] | **Key Phrase Extraction** ([image](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extracts key phrases to identify the main points. For example, for the input text "The food was delicious and there were wonderful staff", the API returns the main talking points: "food" and "wonderful staff". | Generally available. <br> This container can also [run in disconnected environments](containers/disconnected-containers.md). |
| [Language service][ta-containers-language] |  **Text Language Detection** ([image](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | For up to 120 languages, detects which language the input text is written in and report a single language code for every document submitted on the request. The language code is paired with a score indicating the strength of the score. | Generally available. <br> This container can also [run in disconnected environments](containers/disconnected-containers.md). |
| [Language service][ta-containers-sentiment] | **Sentiment Analysis** ([image](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analyzes raw text for clues about positive or negative sentiment. This version of sentiment analysis returns sentiment labels (for example *positive* or *negative*) for each document and sentence within it. |  Generally available. This <br> container can also [run in disconnected environments](containers/disconnected-containers.md). |
| [Language service][ta-containers-health] |  **Text Analytics for health** | Extract and label medical information from unstructured clinical text. | Generally available |
| [Translator][tr-containers] | **Translator** | Translate text in several languages and dialects. | Gated preview - [request access](https://aka.ms/csgate-translator). <br> This container can also [run in disconnected environments](containers/disconnected-containers.md). | 

### Speech containers

> [!NOTE]
> To use Speech containers, you will need to complete an [online request form](https://aka.ms/csgate).

| Service |  Container | Description | Availability |
|--|--|--|
| [Speech Service API][sp-containers-stt] |  **Speech-to-text** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcribes continuous real-time speech into text. | Generally available. <br> This container can also [run in disconnected environments](containers/disconnected-containers.md). |
| [Speech Service API][sp-containers-cstt] | **Custom Speech-to-text** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcribes continuous real-time speech into text using a custom model. | Generally available |
| [Speech Service API][sp-containers-tts] | **Text-to-speech** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Converts text to natural-sounding speech. | Generally available |
| [Speech Service API][sp-containers-ctts] | **Custom Text-to-speech** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Converts text to natural-sounding speech using a custom model. | Gated preview |
| [Speech Service API][sp-containers-ntts] | **Neural Text-to-speech** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Converts text to natural-sounding speech using deep neural network technology, allowing for more natural synthesized speech. | Generally available. |
| [Speech Service API][sp-containers-lid] | **Speech language detection** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Determines the language of spoken audio. | Gated preview |

### Vision containers


| Service |  Container | Description | Availability |
|--|--|--|--|
| [Computer Vision][cv-containers] | **Read OCR** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | The Read OCR container allows you to extract printed and handwritten text from images and documents with support for JPEG, PNG, BMP, PDF, and TIFF file formats. For more information, see the [Read API documentation](./computer-vision/overview-ocr.md). | Generally Available. This container can also [run in disconnected environments](containers/disconnected-containers.md). |
| [Spatial Analysis][spa-containers] | **Spatial analysis** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analyzes real-time streaming video to understand spatial relationships between people, their movement, and interactions with objects in physical environments. | Preview |

<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Additionally, some containers are supported in the Cognitive Services [multi-service resource](cognitive-services-apis-create-account.md) offering. You can create one single Cognitive Services All-In-One resource and use the same billing key across supported services for the following services:

* Computer Vision
* LUIS
* Language service

## Prerequisites

You must satisfy the following prerequisites before using Azure Cognitive Services containers:

**Docker Engine**: You must have Docker Engine installed locally. Docker provides packages that configure the Docker environment on [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), and [Windows](https://docs.docker.com/docker-for-windows/). On Windows, Docker must be configured to support Linux containers. Docker containers can also be deployed directly to [Azure Kubernetes Service](../aks/index.yml) or [Azure Container Instances](../container-instances/index.yml).

Docker must be configured to allow the containers to connect with and send billing data to Azure.

**Familiarity with Microsoft Container Registry and Docker**: You should have a basic understanding of both Microsoft Container Registry and Docker concepts, like registries, repositories, containers, and container images, as well as knowledge of basic `docker` commands.

For a primer on Docker and container basics, see the [Docker overview](https://docs.docker.com/engine/docker-overview/).

Individual containers can have their own requirements, as well, including server and memory allocation requirements.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## Developer samples

Developer samples are available at our [GitHub repository](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## Next steps

Learn about [container recipes](containers/container-reuse-recipe.md) you can use with the Cognitive Services.

Install and explore the functionality provided by containers in Azure Cognitive Services:

* [Anomaly Detector containers][ad-containers]
* [Computer Vision containers][cv-containers]
* [Language Understanding (LUIS) containers][lu-containers]
* [Speech Service API containers][sp-containers]
* [Language service containers][ta-containers]
* [Translator containers][tr-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: language-service/overview.md#deploy-on-premises-using-docker-containers
[ta-containers-keyphrase]: language-service/key-phrase-extraction/how-to/use-containers.md
[ta-containers-language]: language-service/language-detection/how-to/use-containers.md
[ta-containers-sentiment]: language-service/sentiment-opinion-mining/how-to/use-containers.md
[ta-containers-health]: language-service/text-analytics-for-health/how-to/use-containers.md
[tr-containers]: translator/containers/translator-how-to-install-container.md
[request-access]: https://aka.ms/csgate
