---
title: Azure API for FHIR monthly releases
description: This article provides details about the Azure API for FHIR monthly features and enhancements.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 01/11/2022
ms.custom: references_regions
ms.author: cavoeg
---

# Release notes: Azure API for FHIR

Azure API for FHIR provides a fully managed deployment of the Microsoft FHIR Server for Azure. The server is an implementation of the [FHIR](https://hl7.org/fhir) standard. This document provides details about the features and enhancements made to Azure API for FHIR.

## December 2021

### **Features and enhancements**

|Enhancements &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |Related information |
| :------------------- | ---------------: |
|Added Publisher to `CapabiilityStatement.name` |You can now find the publisher in the capability statement at `CapabilityStatement.name`. [#2319](https://github.com/microsoft/fhir-server/pull/2319) |
|Log `FhirOperation` linked to anonymous calls to Request metrics |We weren’t logging operations that didn’t require authentication. We extended the ability to get `FhirOperation` type in `RequestMetrics` for anonymous calls. [#2295](https://github.com/microsoft/fhir-server/pull/2295) |

### **Bug fixes**

|Bug fixes |Related information |
| :----------------------------------- | ---------------: |
|Fixed 500 error when `SearchParameter` Code is null |Fixed an issue with `SearchParameter` if it had a null value for Code, the result would be a 500. Now it will result in an  `InvalidResourceException` like the other values do. [#2343](https://github.com/microsoft/fhir-server/pull/2343) |
|Returned `BadRequestException` with valid message when input JSON body is invalid |For invalid JSON body requests, the FHIR server was returning a 500 error. Now we will return a `BadRequestException` with a valid message instead of 500. [#2239](https://github.com/microsoft/fhir-server/pull/2239) |
|`_sort` can cause `ChainedSearch` to return incorrect results |Previously, the sort options from the chained search's `SearchOption` object was not cleared, causing the sorting options to be passed through to the chained sub-search, which are not valid. This could result in no results when there should be results. This bug is now fixed  [#2347](https://github.com/microsoft/fhir-server/pull/2347). It addressed GitHub bug [#2344](https://github.com/microsoft/fhir-server/issues/2344). |



## November 2021

### **Features and enhancements**

|Enhancements &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |Related information |
| :------------------- | ---------------: |
|Process Patient-everything links  |We've expanded the Patient-everything capabilities to process patient links [#2305](https://github.com/microsoft/fhir-server/pull/2305). For more information, see [Patient-everything in FHIR](../../healthcare-apis/fhir/patient-everything.md#processing-patient-links) documentation.  |
|Added software name and version to capability statement |In the capability statement, the software name now distinguishes if you're using Azure API for FHIR or Azure Healthcare APIs. The software version will now specify which open-source [release package](https://github.com/microsoft/fhir-server/releases) is live in the managed service [#2294](https://github.com/microsoft/fhir-server/pull/2294). Addresses: [#1778](https://github.com/microsoft/fhir-server/issues/1778) and [#2241](https://github.com/microsoft/fhir-server/issues/2241) |
|Log 500's to `RequestMetric` |Previously, 500s or any unknown/unhandled errors were not getting logged in `RequestMetric`. They're now getting logged [#2240](https://github.com/microsoft/fhir-server/pull/2240). For more information, see [Enable diagnostic settings in Azure API for FHIR](../../healthcare-apis/azure-api-for-fhir/enable-diagnostic-logging.md) |
|Compress continuation tokens |In certain instances, the continuation token was too long to be able to follow the [next link](../../healthcare-apis/azure-api-for-fhir/overview-of-search.md#pagination) in searches and would result in a 404. To resolve this, we compressed the continuation token to ensure it stays below the size limit [#2279](https://github.com/microsoft/fhir-server/pull/2279). Addresses issue [#2250](https://github.com/microsoft/fhir-server/issues/2250). |

### **Bug fixes**

|Bug fixes |Related information |
| :----------------------------------- | ---------------: |
|Resolved 500 error when the date was passed with a time zone. |This fixes a 500 error when a date with a time zone was passed into a datetime field [#2270](https://github.com/microsoft/fhir-server/pull/2270). |
|Resolved issue when posting a bundle with incorrect Media Type returned a 500 error. |Previously when posting a search with a key that contains certain characters, a 500 error was returned. This fixes this issue [#2264](https://github.com/microsoft/fhir-server/pull/2264), and it addresses [#2148](https://github.com/microsoft/fhir-server/issues/2148). |


## October 2021

### **Bug fixes**

| Infinite loop bug | Related information          |
| :----------------- | ----------------------------: |
|Fixed issue where [Conditional Delete](./././../azure-api-for-fhir/fhir-rest-api-capabilities.md#conditional-delete) could result in an infinite loop. | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |

## September 2021 

### **Features and enhancements**

|Enhancements &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |Related information |
| :------------------- | ---------------: |

|Added support for conditional patch | [Conditional patch](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| :----------------------------------- | ------: |
|Conditional patch |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Added conditional patch audit event. |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|Allow JSON patch in bundles | [JSON patch in bundles](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| :----------------------------------- | ------: |
|Allows for search history bundles with Patch requests. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Enabled JSON patch in bundles using Binary resources. |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|New audit event sub-types |Related information |
| :----------------------------------- | ---------------: |
|Added new audit [OperationName subtypes](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details).| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|[Reindex improvements](how-to-run-a-reindex.md) |Related information |
| ----------------------------------- | ---------------: |
|Added [boundaries for reindex](how-to-run-a-reindex.md) parameters. |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Update error message for reindex parameter boundaries. |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Added final reindex count check. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### **Bug fixes**

|Bug fixes |Related information |
| :----------------------------------- | ---------------: |
|Wider catch for exceptions when applying patch. |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|Fixes history with PATCH in STU3.| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|Custom search bugs |Related information |
| ----------------------------------- | ---------------: |
|Addresses the delete failure with Custom Search parameters. | [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|Added retry logic while Deleting Search parameter. | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Set max item count in search options in SearchParameterDefinitionManager. | [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|Provides better exception if there's a bad expression in search parameter. |[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|Resolved retry 503 error |Related information |
| :----------------------------------- | ---------------: |
|Retry 503 error from Cosmos DB. |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|Fixes processing 429s from StoreProcedures. |[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|GitHub issues closed |Related information |
| :----------------------------------- | ---------------: |
|Unable to create custom search parameter for the CarePlan medical device. |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|Unclear error message for conditional create with no ID. | [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### IoT connector for FHIR (preview)

|Bug fixes &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|Related information |
| :----------------------------------- | ---------------: |
|Fixed broken link.| Updated link to the IoT connector Azure documentation in the Azure API for FHIR portal. |

## Next steps

For information about the features and bug fixes in Azure Healthcare APIs (FHIR service, DICOM service, and IoT connector), see

>[!div class="nextstepaction"]
>[Release notes: Azure Healthcare APIs](../release-notes.md)
