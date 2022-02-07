---
title: "Text Translation REST API reference guide"
titleSuffix: Azure Cognitive Services
description: View a list of with links to the Text Translation REST APIs.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/10/2021
ms.author: lajanuar
---

# Text Translation REST API

Text Translation is a cloud-based feature of the Azure Translator service and is part of the Azure Cognitive Service family of REST APIs. The Text Translation API translates text between language pairs across all [supported languages and dialects](../../language-support.md). The available methods are listed in the table below:

| Request| Method| Description|
|---------|--------------|---------|
| [**languages**](v3-0-languages.md) | **GET** | Returns the set of languages currently supported by the **translation**, **transliteration**, and **dictionary** methods. This request does not require authentication headers and you do not need a Translator resource to view the supported language set.|
|[**translate**](v3-0-translate.md) | **POST**| Translate specified source language text into the target language text.|
|[**transliterate**](v3-0-transliterate.md) |  **POST** | Map source language script or alphabet to a target language script or alphabet.
|[**detect**](v3-0-detect.md) | **POST** | Identify the source language. |
|[**breakSentence**](v3-0-break-sentence.md) | **POST** | Returns an array of integers representing the length of sentences in a source text. |
| [**dictionary/lookup**](v3-0-dictionary-lookup.md) | **POST** | Returns alternatives for single word translations. |
| [**dictionary/examples**](v3-0-dictionary-examples.md) | **POST** | Returns how a term is used in context. |

> [!div class="nextstepaction"]
> [Create a Translator resource in the Azure portal.](../translator-how-to-signup.md)

> [!div class="nextstepaction"]
> [Quickstart: REST API and your programming language](../quickstart-translator.md)
