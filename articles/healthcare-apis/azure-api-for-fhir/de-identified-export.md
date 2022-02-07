---
title: Exporting de-identified data (preview) for Azure API for FHIR
description: This article describes how to set up and use de-identified export for Azure API for FHIR
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 01/28/2022
ms.author: ginle
---
# Exporting de-identified data (preview) for Azure API for FHIR

> [!Note] 
> Results when using the de-identified export will vary based on factors such as data inputted, and functions selected by the customer. Microsoft is unable to evaluate the de-identified export outputs or determine the acceptability for customer's use cases and compliance needs. The de-identified export is not guaranteed to meet any specific legal, regulatory, or compliance requirements.

The $export command can also be used to export de-identified data from the FHIR server. It uses the anonymization engine from [Tools for Health Data Anonymization](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md), and takes anonymization config details in query parameters. You can create your own anonymization config file or use the [sample config file](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) for HIPAA Safe Harbor method as a starting point. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> Currently, Azure API for FHIR only supports de-identified export at the system level ($export).

|Query parameter            | Example |Optionality| Description|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.json|Required for de-identified export |Name of the configuration file. See the configuration file format [here](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md#configuration-file-format). This file should be kept inside a container named **anonymization** within the same Azure storage account that is configured as the export location. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Optional for de-identified export|This is the Etag of the configuration file. You can get the Etag using Azure storage explorer from the blob property|

> [!IMPORTANT]
> Both raw export as well as de-identified export writes to the same Azure storage account specified as part of export configuration. It is recommended that you use different containers corresponding to different de-identified config and manage user access at the container level.

## Next steps

In this article, you've learned how to set up and use de-identified export. Next, to learn how to export FHIR data using $export for Azure API for FHIR, see
 
>[!div class="nextstepaction"]
>[Export data](export-data.md)