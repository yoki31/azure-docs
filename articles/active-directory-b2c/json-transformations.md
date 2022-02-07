---
title: JSON claims transformation examples for custom policies
titleSuffix: Azure AD B2C
description: JSON claims transformation examples for the Identity Experience Framework (IEF) schema of Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG

ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/17/2022
ms.author: kengaderdus
ms.subservice: B2C
---

# JSON claims transformations

This article provides examples for using the JSON claims transformations of the Identity Experience Framework  schema in Azure Active Directory B2C (Azure AD B2C). For more information, see [claims transformations](claimstransformations.md).

## CreateJsonArray

Create a JSON single element array from a claim value.

| Element | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | The claim to be added to the output claim. |
| OutputClaim | outputClaim | string | The JSON string that is produced after this claims transformation has been invoked. |

### Example of CreateJsonArray

The following example creates a JSON single array. 

```xml
<ClaimsTransformation Id="createlJsonPayload" TransformationMethod="CreateJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="result" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Input claims:
  - **inputClaim**: someone@example.com
- Output claims:
  - **outputClaim**: ["someone@contoso.com"]

## GenerateJson

Use either claim values or constants to generate a JSON string. The path string following dot notation is used to indicate where to insert the data into a JSON string. After splitting by dots, any integers are interpreted as the index of a JSON array and non-integers are interpreted as the index of a JSON object.

| Element | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Any string following dot notation | string | The JsonPath of the JSON where the claim value will be inserted into. |
| InputParameter | Any string following dot notation | string | The JsonPath of the JSON where the constant string value will be inserted into. |
| OutputClaim | outputClaim | string | The generated JSON string. |

### Example of GenerateJson

The following example generates a JSON string based on the claim value of "email" and "OTP" and constant strings. 

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

The following claims transformation outputs a JSON string claim that will be the body of the request sent to SendGrid (a third-party email provider). The JSON object's structure is defined by the IDs in dot notation of the InputParameters and the TransformationClaimTypes of the InputClaims. Numbers in the dot notation imply arrays. The values come from the InputClaims' values and the InputParameters' "Value" properties.

- Input claims:
  - **email**,  transformation claim type  **personalizations.0.to.0.email**: "someone@example.com"
  - **otp**, transformation claim type **personalizations.0.dynamic_template_data.otp** "346349"
- Input parameter:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **from.email**: "service@contoso.com"
  - **personalizations.0.subject** "Contoso account email verification code"
- Output claim:
  - **outputClaim**: 

      ```json
      {
        "personalizations": [
          {
            "to": [
              {
                "email": "someone@example.com"
              }
            ],
            "dynamic_template_data": {
              "otp": "346349",
              "verify-email" : "someone@example.com"
            },
            "subject": "Contoso account email verification code"
          }
        ],
        "template_id": "d-989077fbba9746e89f3f6411f596fb96",
        "from": {
          "email": "service@contoso.com"
        }
      }
      ```

### Another example of GenerateJson

The following example generates a JSON string based on the claim values and constant strings.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

The following claims transformation outputs a JSON string claim that will be the body of the request sent to a REST API. The JSON object's structure is defined by the IDs in dot notation of the InputParameters and the TransformationClaimTypes of the InputClaims. The values come from the InputClaims' values and the InputParameters' "Value" properties.

- Input claims:
  - **email**,  transformation claim type  **customerEntity.email**: "john.s@contoso.com"
  - **objectId**, transformation claim type **customerEntity.userObjectId** "01234567-89ab-cdef-0123-456789abcdef"
  - **givenName**, transformation claim type **customerEntity.firstName** "John"
  - **surname**, transformation claim type **customerEntity.lastName** "Smith"
- Input parameter:
  - **customerEntity.role.name**: "Administrator"
  - **customerEntity.role.id** 1
- Output claim:
  - **requestBody**:

    ```json
    {
       "customerEntity":{
          "email":"john.s@contoso.com",
          "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
          "firstName":"John",
          "lastName":"Smith",
          "role":{
             "name":"Administrator",
             "id": 1
          }
       }
    }
    ```

## GetClaimFromJson

Get a specified element from a JSON data.

| Element | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | The claims that are used by the claims transformation to get the item. |
| InputParameter | claimToExtract | string | the name of the JSON element to be extracted. |
| OutputClaim | extractedClaim | string | The claim that is produced after this claims transformation has been invoked, the element value specified in the _claimToExtract_ input parameter. |

### Example of GetClaimFromJson

In the following example, the claims transformation extracted the `emailAddress` element from the JSON data: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Input claims:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Input parameter:
  - **claimToExtract**: emailAddress
- Output claims:
  - **extractedClaim**: someone@example.com

### Another example of GetClaimFromJson

The GetClaimFromJson claims transformation gets a single element from a JSON data. In the preceding example, the emailAddress. To get the displayName, create another claims transformation. For example:

```xml
<ClaimsTransformation Id="GetDispalyNameClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="displayName" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Input claims:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Input parameter:
  - **claimToExtract**: displayName
- Output claims:
  - **extractedClaim**: Someone

## GetClaimsFromJsonArray

Get a list of specified elements from Json data.

| Element | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | The claim with the JSON payload. This claim is used by the claims transformation to get the claims. |
| InputParameter | errorOnMissingClaims | boolean | Specifies whether to throw an error if one of the claims is missing. |
| InputParameter | includeEmptyClaims | string | Specify whether to include empty claims. |
| InputParameter | jsonSourceKeyName | string | Element key name |
| InputParameter | jsonSourceValueName | string | Element value name |
| OutputClaim | Collection | string, int, boolean, and datetime |List of claims to extract. The name of the claim should be equal to the one specified in _jsonSourceClaim_ input claim. |

### Example of GetClaimsFromJsonArray

In the following example, the claims transformation extracts the following claims: email (string), displayName (string), membershipNum (int), active (boolean) and  birthDate (datetime) from the JSON data.

```xml
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipID" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthDate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Input claims:
  - **jsonSourceClaim**:
      
      ```json
      [
        {
          "key": "email",
          "value": "someone@example.com"
        },
        {
          "key": "displayName",
          "value": "Someone"
        },
        {
          "key": "membershipID",
          "value": 6353399
        },
        {
          "key": "active",
          "value": true
        },
        {
          "key": "birthDate",
          "value": "2005-09-23T00:00:00Z"
        }
      ]
      ```

- Input parameters:
  - **errorOnMissingClaims**: false
  - **includeEmptyClaims**: false
  - **jsonSourceKeyName**: key
  - **jsonSourceValueName**: value
- Output claims:
  - **email**: "someone@example.com"
  - **displayName**: "Someone"
  - **membershipID**: 6353399
  - **active**: true
  - **birthDate**: 2005-09-23T00:00:00Z

## GetNumericClaimFromJson

Gets a specified numeric (long) element from a JSON data.

| Element | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | The claim with the JSON payload. This claim is used by the claims transformation to get the numeric claim. |
| InputParameter | claimToExtract | string | The name of the JSON element to extract. |
| OutputClaim | extractedClaim | long | The claim that is produced after this claims transformation has been invoked, the element's value specified in the _claimToExtract_ input parameters. |

### Example of GetNumericClaimFromJson

In the following example, the claims transformation extracts the `id` element from the JSON data.

```xml
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Input claims:
  - **inputJson**: 

    ```json
    {
      "emailAddress": "someone@example.com",
      "displayName": "Someone",
      "id": 6353399
    }
    ```

- Input parameters
  - **claimToExtract**:  id
- Output claims:
  - **extractedClaim**: 6353399

## GetSingleItemFromJson

Gets the first element from a JSON data.

| Element | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | The claim with the JSON payload. This claim is used by the claims transformation to get the item from the JSON data. |
| OutputClaim | key | string | The first element key in the JSON. |
| OutputClaim | value | string | The first element value in the JSON. |

### Example of GetSingleItemFromJson

In the following example, the claims transformation extracts the first element (given name) from the JSON data.

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

- Input claims:
  - **inputJson**: 

    ```json
    {
      "givenName": "Emily",
      "lastName": "Smith"
    }
    ```

- Output claims:
  - **key**: givenName
  - **value**: Emilty


## GetSingleValueFromJsonArray

Gets the first element from a JSON data array.

| Element | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | The claim with the JSON payload. This claim is used by the claims transformation to get the value from the JSON array. |
| OutputClaim | extractedClaim | string | The claim that is produced after this claims transformation has been invoked, the first element in the JSON array. |

### Example of GetSingleValueFromJsonArray

In the following example, the claims transformation extracts the first element (email address) from the JSON array  `["someone@example.com", "Someone", 6353399]`.

```xml
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Input claims:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- Output claims:
  - **extractedClaim**: someone@example.com

## XmlStringToJsonString

Convert XML data to JSON format.

| Element | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | string | The claim with the XML payload. This claim is used by the claims transformation to convert the data from XML to JSON format. |
| OutputClaim | json | string | The claim that is produced after this claims transformation has been invoked, the data in JSON format. |

```xml
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

### Example of XmlStringToJsonString

In the following example, the claims transformation converts the following XML data to JSON format.


Input claim:

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Output claim:

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

## Next steps

- Find more [claims transformation samples](https://github.com/azure-ad-b2c/unit-tests/tree/main/claims-transformation) on the Azure AD B2C community GitHub repo