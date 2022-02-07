---
title: Copy and transform data from and to a REST endpoint by using Azure Data Factory 
titleSuffix: Azure Data Factory & Azure Synapse
description: Learn how to use Copy Activity to copy data and use Data Flow to transform data from a cloud or on-premises REST source to supported sink data stores, or from supported source data store to a REST sink in Azure Data Factory or Azure Synapse Analytics pipelines. 
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 01/13/2022
ms.author: makromer
---

# Copy and transform data from and to a REST endpoint by using Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

This article outlines how to use Copy Activity in Azure Data Factory to copy data from and to a REST endpoint. The article builds on [Copy Activity in Azure Data Factory](copy-activity-overview.md), which presents a general overview of Copy Activity.

The difference among this REST connector, [HTTP connector](connector-http.md), and the [Web table connector](connector-web-table.md) are:

- **REST connector** specifically supports copying data from RESTful APIs.
- **HTTP connector** is generic to retrieve data from any HTTP endpoint, for example, to download file. Before this REST connector you may happen to use HTTP connector to copy data from RESTful APIs, which is supported but less functional comparing to REST connector.
- **Web table connector** extracts table content from an HTML webpage.

## Supported capabilities

You can copy data from a REST source to any supported sink data store. You also can copy data from any supported source data store to a REST sink. For a list of data stores that Copy Activity supports as sources and sinks, see [Supported data stores and formats](copy-activity-overview.md#supported-data-stores-and-formats).

Specifically, this generic REST connector supports:

- Copying data from a REST endpoint by using the **GET** or **POST** methods and copying data to a REST endpoint by using the **POST**, **PUT** or **PATCH** methods.
- Copying data by using one of the following authentications: **Anonymous**, **Basic**, **AAD service principal**, and **managed identities for Azure resources**.
- **[Pagination](#pagination-support)** in the REST APIs.
- For REST as source, copying the REST JSON response [as-is](#export-json-response-as-is) or parse it by using [schema mapping](copy-activity-schema-and-type-mapping.md#schema-mapping). Only response payload in **JSON** is supported.

> [!TIP]
> To test a request for data retrieval before you configure the REST connector in Data Factory, learn about the API specification for header and body requirements. You can use tools like Postman or a web browser to validate.

## Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## Get started

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## Create a REST linked service using UI

Use the following steps to create a REST linked service in the Azure portal UI.

1. Browse to the Manage tab in your Azure Data Factory or Synapse workspace and select Linked Services, then click New:

    # [Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot of creating a new linked service with Azure Data Factory UI.":::

    # [Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Screenshot of creating a new linked service with Azure Synapse UI.":::

2. Search for REST and select the REST connector.

    :::image type="content" source="media/connector-rest/rest-connector.png" alt-text="Select REST connector.":::    

1. Configure the service details, test the connection, and create the new linked service.

    :::image type="content" source="media/connector-rest/configure-rest-linked-service.png" alt-text="Configure REST linked service.":::

## Connector configuration details

The following sections provide details about properties you can use to define Data Factory entities that are specific to the REST connector.

## Linked service properties

The following properties are supported for the REST linked service:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The **type** property must be set to **RestService**. | Yes |
| url | The base URL of the REST service. | Yes |
| enableServerCertificateValidation | Whether to validate server-side TLS/SSL certificate when connecting to the endpoint. | No<br /> (the default is **true**) |
| authenticationType | Type of authentication used to connect to the REST service. Allowed values are **Anonymous**, **Basic**, **AadServicePrincipal**, and **ManagedServiceIdentity**. User-based OAuth isn't supported. You can additionally configure authentication headers in `authHeader` property. Refer to corresponding sections below on more properties and examples respectively.| Yes |
| authHeaders | Additional HTTP request headers for authentication.<br/> For example, to use API key authentication, you can select authentication type as “Anonymous” and specify API key in the header. | No |
| connectVia | The [Integration Runtime](concepts-integration-runtime.md) to use to connect to the data store. Learn more from [Prerequisites](#prerequisites) section. If not specified, this property uses the default Azure Integration Runtime. |No |

### Use basic authentication

Set the **authenticationType** property to **Basic**. In addition to the generic properties that are described in the preceding section, specify the following properties:

| Property | Description | Required |
|:--- |:--- |:--- |
| userName | The user name to use to access the REST endpoint. | Yes |
| password | The password for the user (the **userName** value). Mark this field as a **SecureString** type to store it securely in Data Factory. You can also [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Example**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### Use AAD service principal authentication

Set the **authenticationType** property to **AadServicePrincipal**. In addition to the generic properties that are described in the preceding section, specify the following properties:

| Property | Description | Required |
|:--- |:--- |:--- |
| servicePrincipalId | Specify the Azure Active Directory application's client ID. | Yes |
| servicePrincipalKey | Specify the Azure Active Directory application's key. Mark this field as a **SecureString** to store it securely in Data Factory, or [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Specify the tenant information (domain name or tenant ID) under which your application resides. Retrieve it by hovering the mouse in the top-right corner of the Azure portal. | Yes |
| aadResourceId | Specify the AAD resource you are requesting for authorization, for example, `https://management.core.windows.net`.| Yes |
| azureCloudType | For service principal authentication, specify the type of Azure cloud environment to which your AAD application is registered. <br/> Allowed values are **AzurePublic**, **AzureChina**, **AzureUsGovernment**, and **AzureGermany**. By default, the data factory's cloud environment is used. | No |

**Example**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Use system-assigned managed identity authentication

Set the **authenticationType** property to **ManagedServiceIdentity**. In addition to the generic properties that are described in the preceding section, specify the following properties:

| Property | Description | Required |
|:--- |:--- |:--- |
| aadResourceId | Specify the AAD resource you are requesting for authorization, for example, `https://management.core.windows.net`.| Yes |

**Example**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### Use user-assigned managed identity authentication
Set the **authenticationType** property to **ManagedServiceIdentity**. In addition to the generic properties that are described in the preceding section, specify the following properties:

| Property | Description | Required |
|:--- |:--- |:--- |
| aadResourceId | Specify the AAD resource you are requesting for authorization, for example, `https://management.core.windows.net`.| Yes |
| credentials | Specify the user-assigned managed identity as the credential object. | Yes |


**Example**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }    
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### Using authentication headers

In addition, you can configure request headers for authentication along with the built-in authentication types.

**Example: Using API key authentication**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "x-api-key": {
                    "type": "SecureString",
                    "value": "<API key>"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## Dataset properties

This section provides a list of properties that the REST dataset supports. 

For a full list of sections and properties that are available for defining datasets, see [Datasets and linked services](concepts-datasets-linked-services.md). 

To copy data from REST, the following properties are supported:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The **type** property of the dataset must be set to **RestResource**. | Yes |
| relativeUrl | A relative URL to the resource that contains the data. When this property isn't specified, only the URL that's specified in the linked service definition is used. The HTTP connector copies data from the combined URL: `[URL specified in linked service]/[relative URL specified in dataset]`. | No |

If you were setting `requestMethod`, `additionalHeaders`, `requestBody` and `paginationRules` in dataset, it is still supported as-is, while you are suggested to use the new model in activity going forward.

**Example:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## Copy Activity properties

This section provides a list of properties supported by the REST source and sink.

For a full list of sections and properties that are available for defining activities, see [Pipelines](concepts-pipelines-activities.md). 

### REST as source

The following properties are supported in the copy activity **source** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The **type** property of the copy activity source must be set to **RestSource**. | Yes |
| requestMethod | The HTTP method. Allowed values are **GET** (default) and **POST**. | No |
| additionalHeaders | Additional HTTP request headers. | No |
| requestBody | The body for the HTTP request. | No |
| paginationRules | The pagination rules to compose next page requests. Refer to [pagination support](#pagination-support) section on details. | No |
| httpRequestTimeout | The timeout (the **TimeSpan** value) for the HTTP request to get a response. This value is the timeout to get a response, not the timeout to read response data. The default value is **00:01:40**.  | No |
| requestInterval | The time to wait before sending the request for next page. The default value is **00:00:01** |  No |

>[!NOTE]
>REST connector ignores any "Accept" header specified in `additionalHeaders`. As REST connector only support response in JSON, it will auto generate a header of `Accept: application/json`.

**Example 1: Using the Get method with pagination**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Example 2: Using the Post method**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### REST as sink

The following properties are supported in the copy activity **sink** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The **type** property of the copy activity sink must be set to **RestSink**. | Yes |
| requestMethod | The HTTP method. Allowed values are **POST** (default), **PUT**, and **PATCH**. | No |
| additionalHeaders | Additional HTTP request headers. | No |
| httpRequestTimeout | The timeout (the **TimeSpan** value) for the HTTP request to get a response. This value is the timeout to get a response, not the timeout to write the data. The default value is **00:01:40**.  | No |
| requestInterval | The interval time between different requests in millisecond. Request interval value should be a number between [10, 60000]. |  No |
| httpCompressionType | HTTP compression type to use while sending data with Optimal Compression Level. Allowed values are **none** and **gzip**. | No |
| writeBatchSize | Number of records to write to the REST sink per batch. The default value is 10000. | No |

REST connector as sink works with the REST APIs that accept JSON. The data will be sent in JSON with the following pattern. As needed, you can use the copy activity [schema mapping](copy-activity-schema-and-type-mapping.md#schema-mapping) to reshape the source data to conform to the expected payload by the REST API.

```json
[
    { <data object> },
    { <data object> },
    ...
]
```

**Example:**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## Mapping data flow properties

REST is supported in data flows for both integration datasets and inline datasets.

### Source transformation

| Property | Description | Required |
|:--- |:--- |:--- |
| requestMethod | The HTTP method. Allowed values are **GET** and **POST**. | Yes |
| relativeUrl | A relative URL to the resource that contains the data. When this property isn't specified, only the URL that's specified in the linked service definition is used. The HTTP connector copies data from the combined URL: `[URL specified in linked service]/[relative URL specified in dataset]`. | No |
| additionalHeaders | Additional HTTP request headers. | No |
| httpRequestTimeout | The timeout (the **TimeSpan** value) for the HTTP request to get a response. This value is the timeout to get a response, not the timeout to write the data. The default value is **00:01:40**.  | No |
| requestInterval | The interval time between different requests in millisecond. Request interval value should be a number between [10, 60000]. |  No |
| QueryParameters.*request_query_parameter* OR QueryParameters['request_query_parameter'] | "request_query_parameter" is user-defined, which references one query parameter name in the next HTTP request URL. | No |

### Sink transformation

| Property | Description | Required |
|:--- |:--- |:--- |
| additionalHeaders | Additional HTTP request headers. | No |
| httpRequestTimeout | The timeout (the **TimeSpan** value) for the HTTP request to get a response. This value is the timeout to get a response, not the timeout to write the data. The default value is **00:01:40**.  | No |
| requestInterval | The interval time between different requests in millisecond. Request interval value should be a number between [10, 60000]. |  No |
| httpCompressionType | HTTP compression type to use while sending data with Optimal Compression Level. Allowed values are **none** and **gzip**. | No |
| writeBatchSize | Number of records to write to the REST sink per batch. The default value is 10000. | No |

You can set the delete, insert, update, and upsert methods as well as the relative row data to send to the REST sink for CRUD operations.

:::image type="content" source="media/data-flow/data-flow-sink.png" alt-text="Data flow REST sink":::

## Sample data flow script

Notice the use of an alter row transformation prior to the sink to instruct ADF what type of action to take with your REST sink. I.e. insert, update, upsert, delete.

```
AlterRow1 sink(allowSchemaDrift: true,
	validateSchema: false,
	deletable:true,
	insertable:true,
	updateable:true,
	upsertable:true,
	rowRelativeUrl: 'periods',
	insertHttpMethod: 'PUT',
	deleteHttpMethod: 'DELETE',
	upsertHttpMethod: 'PUT',
	updateHttpMethod: 'PATCH',
	timeout: 30,
	requestFormat: ['type' -> 'json'],
	skipDuplicateMapInputs: true,
	skipDuplicateMapOutputs: true) ~> sink1
```

## Pagination support

When copying data from REST APIs, normally, the REST API limits its response payload size of a single request under a reasonable number; while to return large amount of data, it splits the result into multiple pages and requires callers to send consecutive requests to get next page of the result. Usually, the request for one page is dynamic and composed by the information returned from the response of previous page.

This generic REST connector supports the following pagination patterns: 

* Next request’s absolute or relative URL = property value in current response body
* Next request’s absolute or relative URL = header value in current response headers
* Next request’s query parameter = property value in current response body
* Next request’s query parameter = header value in current response headers
* Next request’s header = property value in current response body
* Next request’s header = header value in current response headers

**Pagination rules** are defined as a dictionary in dataset, which contains one or more case-sensitive key-value pairs. The configuration will be used to generate the request starting from the second page. The connector will stop iterating when it gets HTTP status code 204 (No Content), or any of the JSONPath expressions in "paginationRules" returns null.

**Supported keys** in pagination rules:

| Key | Description |
|:--- |:--- |
| AbsoluteUrl | Indicates the URL to issue the next request. It can be **either absolute URL or relative URL**. |
| QueryParameters.*request_query_parameter* OR QueryParameters['request_query_parameter'] | "request_query_parameter" is user-defined, which references one query parameter name in the next HTTP request URL. |
| Headers.*request_header* OR Headers['request_header'] | "request_header" is user-defined, which references one header name in the next HTTP request. |
| EndCondition:*end_condition* | "end_condition" is user-defined, which indicates the condition that will end the pagination loop in the next HTTP request. |
| MaxRequestNumber | Indicates the maximum pagination request number. Leave it as empty means no limit. |
| SupportRFC5988 | RFC 5988 is supported in the pagination rules. By default, this is set to true. It will only be honored if no other pagination rules are defined.

**Supported values** in pagination rules:

| Value | Description |
|:--- |:--- |
| Headers.*response_header* OR Headers['response_header'] | "response_header" is user-defined, which references one header name in the current HTTP response, the value of which will be used to issue next request. |
| A JSONPath expression starting with "$" (representing the root of the response body) | The response body should contain only one JSON object. The JSONPath expression should return a single primitive value, which will be used to issue next request. |

**Example:**

Facebook Graph API returns response in the following structure, in which case next page's URL is represented in ***paging.next***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

The corresponding REST copy activity source configuration especially the `paginationRules` is as follows:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

**Example: Pagination rules**

If you want to send multiple sequence requests with one variable in a range, you can define a variable such as `{offset}`, `{id}` in AbsoluteUrl, Headers, QueryParameters, and define the range rule in pagination rules. See the following examples of pagination rules:

- **Example 1**

    You have multiple requests:
    
    ```
    baseUrl/api/now/table/incident?sysparm_limit=1000&sysparm_offset=0,
    
    baseUrl/api/now/table/incident?sysparm_limit=1000&sysparm_offset=1000,
    
    ......
    
    baseUrl/api/now/table/incident?sysparm_limit=1000&sysparm_offset=10000
    ```
    You need to specify the range pagination: 
    
    `AbosoluteUrl = baseUrl/api/now/table/incident?sysparm_limit=1000&sysparm_offset={offset}`

    The pagination rule is: `QueryParameter.{offset} = RANGE:0:10000:1000`

- **Example 2**

    You have multiple requests:

    ```
    baseUrl/api/now/table/t1
    
    baseUrl/api/now/table/t2
    
    ......
    
    baseUrl/api/now/table/t100
    ```
    You need to specify the range pagination:

    `AbosoluteUrl = baseUrl/api/now/table/t{id}`

    The pagination rule is: `AbsoluteUrl.{id} = RANGE:1:100:1`

## Use OAuth
This section describes how to use a solution template to copy data from REST connector into Azure Data Lake Storage in JSON format using OAuth. 

### About the solution template

The template contains two activities:
- **Web** activity retrieves the bearer token and then pass it to subsequent Copy activity as authorization.
- **Copy** activity copies data from REST to Azure Data Lake Storage.

The template defines two parameters:
- **SinkContainer** is the root folder path where the data is copied to in your Azure Data Lake Storage. 
- **SinkDirectory** is the directory path under the root where the data is copied to in your Azure Data Lake Storage. 

### How to use this solution template

1. Go to the **Copy from REST or HTTP using OAuth** template. Create a new connection for Source Connection. 
    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png" alt-text="Create new connections":::

    Below are key steps for new linked service (REST) settings:
    
     1. Under **Base URL**, specify the url parameter for your own source REST service. 
     2. For **Authentication type**, choose *Anonymous*.
        :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png" alt-text="New REST connection":::

2. Create a new connection for Destination Connection.  
    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png" alt-text="New Gen2 connection":::

3. Select **Use this template**.
    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png" alt-text="Use this template":::

4. You would see the pipeline created as shown in the following example:
    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png" alt-text="Screenshot shows the pipeline created from the template.":::

5. Select **Web** activity. In **Settings**, specify the corresponding **URL**, **Method**, **Headers**, and **Body** to retrieve OAuth bearer token from the login API of the service that you want to copy data from. The placeholder in the template showcases a sample of Azure Active Directory (AAD) OAuth. Note AAD authentication is natively supported by REST connector, here is just an example for OAuth flow. 

    | Property | Description |
    |:--- |:--- |
    | URL |Specify the url to retrieve OAuth bearer token from. for example, in the sample here it's https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |
    | Method | The HTTP method. Allowed values are **Post** and **Get**. | 
    | Headers | Header is user-defined, which references one header name in the HTTP request. | 
    | Body | The body for the HTTP request. | 

    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png" alt-text="Pipeline":::

6. In **Copy data** activity, select *Source* tab, you could see that the bearer token (access_token)  retrieved from previous step would be passed to Copy data activity as **Authorization** under Additional headers. Confirm settings for following properties before starting a pipeline run.

    | Property | Description |
    |:--- |:--- |
    | Request method | The HTTP method. Allowed values are **Get** (default) and **Post**. | 
    | Additional headers | Additional HTTP request headers.| 

   :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png" alt-text="Copy source Authentication":::

7. Select **Debug**, enter the **Parameters**, and then select **Finish**.
   :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png" alt-text="Pipeline run"::: 

8. When the pipeline run completes successfully, you would see the result similar to the following example:
   :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png" alt-text="Pipeline run result"::: 

9. Click the "Output" icon of WebActivity in **Actions** column, you would see the access_token returned by the service.

   :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png" alt-text="Token output"::: 

10. Click the "Input" icon of CopyActivity in **Actions** column, you would see the access_token retrieved by WebActivity is passed to CopyActivity for authentication. 

    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png" alt-text="Token input":::
        
    >[!CAUTION] 
    >To avoid token being logged in plain text, enable "Secure output" in Web activity and "Secure input" in Copy activity.


## Export JSON response as-is

You can use this REST connector to export REST API JSON response as-is to various file-based stores. To achieve such schema-agnostic copy, skip the "structure" (also called *schema*) section in dataset and schema mapping in copy activity.

## Schema mapping

To copy data from REST endpoint to tabular sink, refer to [schema mapping](copy-activity-schema-and-type-mapping.md#schema-mapping).

## Next steps

For a list of data stores that Copy Activity supports as sources and sinks in Azure Data Factory, see [Supported data stores and formats](copy-activity-overview.md#supported-data-stores-and-formats).
