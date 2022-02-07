---
title: Microsoft Sentinel Web Session normalization schema reference (Public preview) | Microsoft Docs
description: This article displays the Microsoft Sentinel Web Session normalization schema.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
ms.topic: reference
ms.date: 11/17/2021
ms.author: ofshezaf

---

# Microsoft Sentinel Web Session normalization schema reference (Public preview)

The Web Session normalization schema is used to describe an IP network activity. For example, IP network activities are reported by web servers, web proxies, and web security gateways.

For more information about normalization in Microsoft Sentinel, see [Normalization and the Advanced SIEM Information Model (ASIM)](normalization.md).

> [!IMPORTANT]
> The Network normalization schema is currently in PREVIEW. This feature is provided without a service level agreement, and is not recommended for production workloads.
>
> The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>

## Schema overview

The Web Session normalization schema represents any HTTP network session, and is specifically suitable to provide support for common source types, including:

- Web servers
- Web proxies
- Web security gateways

The ASIM Web Session schema represents HTTP and HTTPS protocol activity. Since the schema represents protocol activity, it is governed by RFCs and officially assigned parameter lists, which are referenced in this article when appropriate. 

The Web Session schema does not represent audit events from source devices. For example, an event modifying a Web Security Gateway policy cannot be represented by the Web Session schema.

Since HTTP sessions are application layer sessions that utilize TCP/IP as the underlying network layer session, the Web Session schema is a super set of the [ASIM Network Session schema](network-normalization-schema.md).

The most important fields in a Web Session schema are:

- [Url](#url), which reports the url that the client requested from the server.
- The [SrcIpAddr](network-normalization-schema.md#srcipaddr) (aliased to [IpAddr](network-normalization-schema.md#ipaddr)), which represents the IP address from which the request was generated. 
- [EventResultDetails](#eventresultdetails), which reports the HTTP Status Code.

Web Session events may also include [User](network-normalization-schema.md#user) and [Process](process-events-normalization-schema.md) information for the user and process initiating the request. 

## Parsers

For more information about ASIM parsers, see the [ASIM parsers overview](normalization-parsers-overview.md) and [Use ASIM parsers](normalization-about-parsers.md).

### Unifying parsers

To use the unifying parsers that unify all of the out-of-the-box parsers, and ensure that your analysis runs across all the configured sources, use the following KQL functions as the table name in your query.

Deploy ASIM parsers from the [Microsoft Sentinel GitHub repository](https://aka.ms/DeployASIM).

#### <a name="imwebsession"></name>imWebSession

Aggregative parser that uses *union* to include normalized events from all *Web Session* sources. 

Example: Network sessions fields that support [HTTP session fields](#http-session-fields)

- Update this parser if you want to add or remove sources from source-agnostic analytics.
- Use this function in your source-agnostic queries.

#### ASimWebSession

Similar to the [imWebSession](#imwebsession) function, but without parameter support, and therefore does not force the **Logs** page time picker to use the `custom` value.

- Update these parsers if you want to add or remove sources from source-agnostic analytics.
- Use this function in your source-agnostic queries if you don't plan to use parameters.

#### vimWebSession\<vendor\>\<product\>

Source-specific parsers implement normalization for a specific source.

- Add a source-specific parser for a source when there is no out-of-the-box normalizing parser. Update the `im` aggregative parser to include reference to your new parser. 
- Update a source-specific parser to resolve parsing and normalization issues.
- Use a source-specific parser for source-specific analytics.

#### ASimWebSession\<vendor\>\<product\>

Source-specific parsers implement normalization for a specific source.

Unlike the `vim*` functions, the `ASim*` functions do not support parameters.

- Add a source-specific parser for a source when there is no out-of-the-box normalizing parser. Update the aggregative `ASim` parser to include reference to your new parser.
- Update a source-specific parser to resolve parsing and normalization issues.
- Use an `ASim` source-specific parser for interactive queries when not using parameters.


### Add your own normalized parsers

When implementing custom parsers for the Web Session information model, name your KQL functions using the following syntax:

- `vimWebSession<vendor><Product>` for parametrized parsers
- `ASimWebSession<vendor><Product>` for regular parsers

Then, add the new parser to `imWebSession` or `ASimWebSession` respectively.

### Filtering parser parameters

The `im` and `vim*` parsers support [filtering parameters](normalization-about-parsers.md#optimized-parsers). While these parsers are optional, they can improve your query performance.

The following filtering parameters are available:

| Name     | Type      | Description |
|----------|-----------|-------------|
| **starttime** | datetime | Filter only Web sessions that **started** at or after this time. |
| **endtime** | datetime | Filter only Web sessions that **started** running at or before this time. |
| **srcipaddr_has_any_prefix** | dynamic | Filter only Web sessions for which the [source IP address field](network-normalization-schema.md#srcipaddr) prefix is in one of the listed values. Note that the list of values can include IP addresses as well as IP address prefixes. Prefixes should end with a `.`, for example: `10.0.`. |
| **url_has_any** | dynamic | Filter only Web sessions for which the [URL field](#url) has any of the values listed. If specified, and the session is not a web session, no result will be returned.|  
| **httpuseragent_has_any** | dynamic | Filter only web sessions for which the [user agent field](#httpuseragent) has any of the values listed. If specified, and the session is not a web session, no result will be returned. | 
| **ventresultdetails_in** | dynamic | Filter only web sessions for which the HTTP status code, stored in the [EventResultDetails](#eventresultdetails) field, is any of the values listed. | 
| **eventresult** | string | Filter only network sessions with a specific **EventResult** value. |
| | | |

For example, to filter only Web sessions for a specified list of domain names, use:

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co",...]);
imWebSession (hurl_has_any = torProxies)
```

## Schema details

The Web Session information model is aligned is the [OSSEM Network entity schema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md) and the [OSSEM HTTP entity schema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/http.md).

To conform with industry best practices, the Web Session schema uses the descriptors **Src** and **Dst** to identify the session source and destination devices, without including the token **Dvc** in the field name.

So, for example, the source device hostname and IP address are named **SrcHostname** and **SrcIpAddr** respectively, and not **Src*Dvc*Hostname** and **Src*Dvc*IpAddr**. The prefix **Dvc** is only used for the reporting or intermediary device, as applicable.

Fields that describe the user and application associated with the source and destination devices also use the **Src** and **Dst** descriptors.

Other ASIM schemas typically use **Target** instead of **Dst**.


### Common fields

Fields common to all schemas are described in the [ASIM schema overview](normalization-about-schemas.md#common). The following fields have specific guidelines for Web Session events:

| Field               | Class       | Type       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventType** | Mandatory | Enumerated | Describes the operation reported by the record and should be set to `HTTPsession`. |
| **EventResult** | Mandatory | Enumerated | Describes the event result, normalized to one of the following values: <br> - `Success` <br> - `Partial` <br> - `Failure` <br> - `NA` (not applicable) <br><br>For an HTTP session, `Success` is defined as a status code lower than `400`, and `Failure` is defined as a status code higher than `400`. For a list of HTTP status codes refer to [W3 Org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).<br><br>The source may provide only a value for the [EventResultDetails](#eventresultdetails)  field, which must be analyzed to get the  **EventResult**  value. |
| <a name="eventresultdetails"></a>**EventResultDetails** | Optional | String | For HTTP sessions, the value should be the HTTP status code. <br><br>**Note**: The value may be provided in the source record using different terms, which should be normalized to these values. The original value should be stored in the **EventOriginalResultDetails** field.|
| **EventSchema** | Mandatory | String | The name of the schema documented here is `WebSession`. |
| **EventSchemaVersion**  | Mandatory   | String     | The version of the schema. The version of the schema documented here is `0.1`         |
| **Dvc** fields|        |      | For Web Session events,  device fields refer to the system reporting the Web Session event.  |
| | | | |

### Network session fields

HTTP sessions are application layer sessions that utilize TCP/IP as the underlying network layer session. The Web Session schema is a super set of [ASIM Network Session schema](network-normalization-schema.md) and all [Network Session Fields](network-normalization-schema.md#network-session-fields) are also included in the Web Session schema.

The following ASIM Network Session schema have specific guidelines when used for a Web Session event:
- The alias IpAddr should preferably alias [SrcNatIpAddr](network-normalization-schema.md#srcnatipaddr) rather than [SrcIpAddr](network-normalization-schema.md#srcipaddr).
- The alias User should refer to the [SrcUsername](network-normalization-schema.md#srcusername) and not to [DstUsername](network-normalization-schema.md#dstusername).
- The field [EventOriginalResultDetails](normalization-about-schemas.md#eventoriginalresultdetails) can hold any result reported by the source in addition to the HTTP status code stored in [EventResultDetails](#eventresultdetails).
- For Web Sessions, the primary destination field is the [Url Field](#url). The [DstDomain](network-normalization-schema.md#dstdomain) is optional rather than recommended. Specifically, if not available, there is no need to extract it from the URL in the parser.

### <a name="Intermediary"></a>Intermediary device fields

Web Session events are commonly reported by intermediate devices which terminate the HTTP connection from the client and initiate a new connection, acting as a proxy, with the server. To represent the intermediate device, use the [ASIM Network Session schema](network-normalization-schema.md) [Intermediary device fields](network-normalization-schema.md#Intermediary)


### <a name="http-session-fields"></a>HTTP session fields

The following are additional fields that are specific to web sessions:

| Field | Class | Type | Description |
| --- | --- | --- | --- |
| <a name="url"></a>**Url** | Mandatory | String | The full HTTP request URL, including parameters.<br><br>Example: `https://contoso.com/fo/?k=v&amp;q=u#f` |
| **UrlCategory** | Optional | String | The defined grouping of a URL or the domain part of the URL. The category is commonly provided by web security gateways and is based on the content of the site the URL points to.<br><br>Example: search engines, adult, news, advertising, and parked domains. |
| **UrlOriginal** | Optional | String | The original value of the URL, when the URL was modified by the reporting device and both values are provided. |
| **HttpVersion** | Optional | String | The HTTP Request Version.<br><br>Example: `2.0` |
| **HttpRequestMethod** | Recommended | Enumerated | The HTTP Method. The values are as defined in [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4) and [RFC 5789](https://datatracker.ietf.org/doc/html/rfc5789#section-2), and include `GET`, `HEAD`, `POST`, `PUT`, `DELETE`, `CONNECT`, `OPTIONS`, `TRACE`, and `PATCH`.<br><br>Example: `GET` |
| **HttpStatusCode** | Alias | | The HTTP Status Code. Alias to [EventResultDetails](#eventresultdetails). |
| <a name="httpcontenttype"></a>**HttpContentType** | Optional | String | The HTTP Response content type header. <br><br>**Note**: The **HttpContentType** field may include both the content format and additional parameters, such as the encoding used to get the actual format.<br><br> Example: `text/html; charset=ISO-8859-4` |
| **HttpContentFormat** | Optional | String | The content format part of the [HttpContentType](#httpcontenttype) <br><br> Example: `text/html` |
| **HttpReferrer** | Optional | String | The HTTP referrer header.<br><br>**Note**: ASIM, in sync with OSSEM, uses the correct spelling for *referrer*, and not the original HTTP header spelling.<br><br>Example: `https://developer.mozilla.org/docs` |
| <a name="httpuseragent"></a>**HttpUserAgent** | Optional | String | The HTTP user agent header.<br><br>Example:<br> `Mozilla/5.0` (Windows NT 10.0; WOW64)<br>`AppleWebKit/537.36` (KHTML, like Gecko)<br>`Chrome/83.0.4103.97 Safari/537.36` |
| **UserAgent** | Alias | | Alias to [HttpUserAgent](#httpuseragent) |
| **HttpRequestXff** | Optional | IP Address | The HTTP X-Forwarded-For header.<br><br>Example: `120.12.41.1` |
| **HttpRequestTime** | Optional | Integer | The amount of time, in milliseconds, it took to send the request to the server, if applicable.<br><br>Example: `700` |
| **HttpResponseTime** | Optional | Integer | The amount of time, in milliseconds, it took to receive a response in the server, if applicable.<br><br>Example: `800` |
| **FileName** | Optional | String | For HTTP uploads, the name of the uploaded file. |
| **FileMD5** | Optional | MD5 | For HTTP uploads, the MD5 hash of the uploaded file.<br><br>Example: `75a599802f1fa166cdadb360960b1dd0` |
| **FileSHA1** | Optional | SHA1 | For HTTP uploads, the SHA1 hash of the uploaded file.<br><br>Example:<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
| **FileSHA256** | Optional | SHA256 | For HTTP uploads, the SHA256 hash of the uploaded file.<br><br>Example:<br>`e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **FileSHA512** | Optional | SHA512 | For HTTP uploads, the SHA512 hash of the uploaded file. |
| <a name="hash"></a>**Hash** | Alias || Alias to the available Hash field. | 
| **FileHashType** | Optional | Enumerated | The type of the hash in the [Hash](#hash) field. Possible values include: `MD5`, `SHA1`, `SHA256`, and `SHA512`. |
| **FileSize** | Optional | Integer | For HTTP uploads, the size in bytes of the uploaded file. |
| **FileContentType** | Optional | String | For HTTP uploads, the content type of the uploaded file. |
| **RuleName** | Optional | String | The name or ID of the rule by which [DvcAction](normalization-about-schemas.md#dvcaction) was decided upon.<br><br>Example: `AnyAnyDrop`|
| **RuleNumber** | Optional | Integer | The number of the rule by which [DvcAction](normalization-about-schemas.md#dvcaction) was decided upon.<br><br> Example: `23`|
| **Rule** | Mandatory | String | Either `NetworkRuleName` or `NetworkRuleNumber`|
| **ThreatId** | Optional | String | The ID of the threat or malware identified in the Web session.<br><br>Example:&nbsp;`Tr.124`|
| **ThreatName** | Optional | String | The name of the threat or malware identified in the Web session.<br><br>Example:&nbsp;`EICAR Test File`|
| **ThreatCategory** | Optional | String | The category of the threat or malware identified in the Web session.<br><br>Example:&nbsp;`Trojan`|
| **ThreatRiskLevel** | Optional | Integer | The risk level associated with the Session. The level should be a number between **0** and a **100**.<br><br>**Note**: The value may be provided in the source record using a different scale, which should be normalized to this scale. The original value should be stored in [ThreatRiskLevelOriginal](#threatriskleveloriginal). |
| <a name="threatriskleveloriginal"></a>**ThreatRiskLevelOriginal** | Optional | String | The risk level as reported by the reporting device. |
| | | | |

### Other fields

If the event is reported by one of the endpoints of the web session, it may include information about the process that initiated or terminated the session. In such cases, the [ASIM Process Event schema](process-events-normalization-schema.md) to normalize this information.

## Next steps

For more information, see:

- Watch the [ASIM Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) or review the [slides](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Advanced SIEM Information Model (ASIM) overview](normalization.md)
- [Advanced SIEM Information Model (ASIM) schemas](normalization-about-schemas.md)
- [Advanced SIEM Information Model (ASIM) parsers](normalization-parsers-overview.md)
- [Advanced SIEM Information Model (ASIM) content](normalization-content.md)

