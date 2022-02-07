---
title: Microsoft Sentinel DNS normalization schema reference | Microsoft Docs
description: This article describes the Microsoft Sentinel DNS normalization schema.
author: batamig
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
---

# Microsoft Sentinel DNS normalization schema reference (Public preview)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

The DNS information model is used to describe events reported by a DNS server or a DNS security system, and is used by Microsoft Sentinel to enable source-agnostic analytics.

For more information, see [Normalization and the Advanced SIEM Information Model (ASIM)](normalization.md).

> [!IMPORTANT]
> The DNS normalization schema is currently in PREVIEW. This feature is provided without a service level agreement, and is not recommended for production workloads.
>
> The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>

## Schema overview

The ASIM DNS schema represents DNS protocol activity, which may be logged either by a DNS server or by a device sending DNS requests to a DNS server. The DNS protocol activity includes DNS queries, DNS server updates, and DNS bulk data transfers. Since the schema represents protocol activity, it is governed by RFCs and officially assigned parameter lists, which are referenced in this article when appropriate. The DNS schema does not represent DNS server audit events. 

The most important activity reported by DNS servers is a DNS query, for which the `EventType` field is set to `Query`.   

The most important fields in a DNS event are:

- [DnsQuery](#query), which reports the domain name for which the query was issued.

- The [SrcIpAddr](#srcipaddr) (aliased to [IpAddr](#ipaddr)), which represents the IP address from which the request was generated. DNS servers typically provide the SrcIpAddr field, but DNS clients sometimes do not provide this field and only provide the [SrcHostname](#srchostname) field. 

- [EventResultDetails](#eventresultdetails), which reports as to whether the request was successful and if not, why.

- When available, [DnsResponseName](#responsename), which holds the answer provided by the server to the query. ASIM does not require parsing the response, and its format varies between sources. 

    To use this field in source-agnostic content, search the content using the `has` or `contains` operators.

DNS events collected on client device may also include [User](#user) and [Process](#process) information. 

## Guidelines for collecting DNS events

DNS is a unique protocol in that it may cross a large number of computers. Also, since DNS uses UDP, requests and responses are de-coupled and are not directly related to each other.

The following image shows a simplified DNS request flow, including four segments. A real-world request can be more complex, with more segments involved.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="Simplified DNS request flow.":::

Since request and response segments are not directly connected to each other in the DNS request flow, full logging can result in significant duplication.

The most valuable segment to log is the response to the client, which provides the domain name queries, the lookup result, and the IP address of the client. While many DNS systems log only this segment, there is value in logging the other parts. For example, a DNS cache poisoning attack often takes advantage of fake responses from an upstream server.

If your data source supports full DNS logging and you've chosen to log multiple segments, you'll need to adjust your queries to prevent data duplication in Microsoft Sentinel.

For example, you might modify your query with the following normalization:

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## Parsers

For more information about ASIM parsers, see the [ASIM parsers overview](normalization-parsers-overview.md) and [Use ASIM parsers](normalization-about-parsers.md).

### Unifying parsers

To use parsers that unify all ASIM out-of-the-box parsers, and ensure that your analysis runs across all the configured sources, use the `_Im_Dns` filtering parser or the `_ASim_Dns` parameter-less parser.

Deploy unifying parsers from the [Microsoft Sentinel GitHub repository](https://aka.ms/azsentinelDNS). For more information, see [built-in ASIM parsers and workspace-deployed parsers](normalization-parsers-overview.md#built-in-asim-parsers-and-workspace-deployed-parsers).

### Out-of-the-box, source-specific parsers

Microsoft Sentinel provides the following out-of-the-box, product-specific DNS parsers:

| **Source** | **Built-in parsers** | **Workspace deployed parsers** | 
| --- | --------------------------- | ------------------------------ | 
|**Microsoft DNS Server**<br>Collected using the DNS connector<br> and the Log Analytics Agent | `_ASim_DnsMicrosoftOMS` (regular) <br> `_Im_DnsMicrosoftOMS` (filtering) <br><br>  | `ASimDnsMicrosoftOMS` (regular) <br>`vimDnsMicrosoftOMS` (filtering) <br><br> |
| **Microsoft DNS Server**<br>Collected using NXlog| `_ASim_DnsMicrosoftNXlog` (regular)<br>`_Im_DnsMicrosoftNXlog` (filtering)| `ASimDnsMicrosoftNXlog` (regular)<br> `vimDnsMicrosoftNXlog` (filtering)|
| **Azure Firewall** | `_ASim_DnsAzureFirewall` (regular)<br> `_Im_DnsAzureFirewall` (filtering) | `ASimDnsAzureFirewall` (regular)<br>`vimDnsAzureFirewall` (filtering) |
| **Sysmon for Windows**  (event 22)<br> Collected using the Log Analytics Agent<br> or the Azure Monitor Agent,<br>supporting both the<br> `Event` and `WindowsEvent` tables | `_ASim_DnsMicrosoftSysmon` (regular)<br> `_Im_DnsMicrosoftSysmon` (filtering)  | `ASimDnsMicrosoftSysmon` (regular)<br> `vimDnsMicrosoftSysmon` (filtering) |
| **Cisco Umbrella**  | `_ASim_DnsCiscoUmbrella` (regular)<br> `_Im_DnsCiscoUmbrella` (filtering)  | `ASimDnsCiscoUmbrella` (regular)<br> `vimDnsCiscoUmbrella` (filtering) |
| **Infoblox NIOS**  | `_ASim_DnsInfobloxNIOS` (regular)<br> `_Im_DnsInfobloxNIOS` (filtering) | `ASimDnsInfobloxNIOS` (regular)<br> `vimDnsInfobloxNIOS` (filtering) |
| **GCP DNS** | `_ASim_DnsGcp` (regular)<br> `_Im_DnsGcp`  (filtering) | `ASimDnsGcp` (regular)<br> `vimDnsGcp`  (filtering) |
| **Corelight Zeek DNS events** | `_ASim_DnsCorelightZeek` (regular)<br> `_Im_DnsCorelightZeek`  (filtering) |  `ASimDnsCorelightZeek` (regular)<br> `vimDnsCorelightZeek`  (filtering)
| **zScaler ZIA** |`_ASim_DnsZscalerZIA` (regular)<br> `_Im_DnsZdcalerZIA` (filtering)  | `AsimDnsZscalerZIA` (regular)<br> `vimDnsSzcalerZIA` (filtering)  |
| | | |

These parsers can be deployed from the [Microsoft Sentinel GitHub repository](https://aka.ms/azsentinelDNS).

### Add your own normalized parsers

When implementing custom parsers for the Dns information model, name your KQL functions using the following syntax:

- `vimDns<vendor><Product>` for parametrized parsers
- `ASimDns<vendor><Product>` for regular parsers

### Filtering parser parameters

The `im` and `vim*` parsers support [filtering parameters](normalization-about-parsers.md#optimized-parsers). While these parsers are optional, they can improve your query performance.

The following filtering parameters are available:

| Name     | Type      | Description |
|----------|-----------|-------------|
| **starttime** | datetime | Filter only DNS queries that ran at or after this time. |
| **endtime** | datetime | Filter only DNS queries that finished running at or before this time. |
| **srcipaddr** | string | Filter only DNS queries from this source IP address. |
| **domain_has_any**| dynamic | Filter only DNS queries where the `domain` (or `query`) has any of the listed domain names, including as part of the event domain.
| **responsecodename** | string | Filter only DNS queries for which the response code name matches the provided value. <br>For example: `NXDOMAIN` |
| **response_has_ipv4** | string | Filter only DNS queries in which the response field includes the provided IP address or IP address prefix. Use this parameter when you want to filter on a single IP address or prefix. <br><br>Results are not returned for sources that do not provide a response.|
| **response_has_any_prefix** | dynamic| Filter only DNS queries in which the response field includes any of the listed IP addresses or IP address prefixes. Prefixes should end with a `.`, for example: `10.0.`. <br><br>Use this parameter when you want to filter on a list of IP addresses or prefixes. <br><br>Results are not returned for sources that do not provide a response. |
| **eventtype**| string | Filter only DNS queries of the specified type. If no value is specified, only lookup queries are returned. |
| | | |

For example, to filter only DNS queries from the last day that failed to resolve the domain name, use:

```kql
imDns (responsecodename = 'NXDOMAIN', starttime = ago(1d), endtime=now())
```

To filter only DNS queries for a specified list of domain names, use:

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co",...]);
imDns (domain_has_any = torProxies)
```

## Normalized content

For a full list of analytics rules that use normalized DNS events, see [DNS query security content](normalization-content.md#dns-query-security-content).

## Schema details

The DNS information model is aligned with the [OSSEM DNS entity schema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md).

For more information, see the [Internet Assigned Numbers Authority (IANA) domain name system parameter reference](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).

### Common fields

Fields common to all schemas are described in the [ASIM schema overview](normalization-about-schemas.md#common). The following fields have specific guidelines for DNS Events:

| **Field** | **Class** | **Type**  | **Description** |
| --- | --- | --- | --- |
| **EventType** | Mandatory | Enumerated | Indicates the operation reported by the record. <br><br> For DNS records, this value would be the [DNS op code](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Example: `lookup`|
| **EventSubType** | Optional | Enumerated | Either **request** or **response**. <br><br>For most sources, [only the responses are logged](#guidelines-for-collecting-dns-events), and therefore the value is often **response**.  |
| <a name=eventresultdetails></a>**EventResultDetails** | Alias | | Reason or details for the result reported in the **_EventResult_** field. <br><br> Aliases the [ResponseCodeName](#responsecodename) field.|
| **EventSchemaVersion** | Mandatory | String | The version of the schema documented here is **0.1.3**. |
| **EventSchema** | Mandatory | String | The name of the schema documented here is **Dns**. |
| **Dvc** fields| -      | -    | For DNS events, device fields refer to the system reporting the DNS event. |
| | | | |


### DNS-specific fields

The fields below are specific to DNS events, although many are similar to fields in other schemas and therefore follow the same naming convention.

| **Field** | **Class** | **Type** | **Notes** |
| --- | --- | --- | --- |
| <a name="src"></a>**Src** | Recommended       | String     |    A unique identifier of the source device. <br><br>This field may alias the [SrcDvcId](#srcdvcid), [SrcHostname](#srchostname), or [SrcIpAddr](#srcipaddr) fields. <br><br>Example: `192.168.12.1`       |
| <a name="srcipaddr"></a>**SrcIpAddr** | Recommended | IP Address | The IP address of the client sending the DNS request. For a recursive DNS request, this value would typically be the reporting device, and in most cases set to `127.0.0.1`. <br><br>Example: `192.168.12.1` |
| **SrcPortNumber** | Optional | Integer | Source port of the DNS query.<br><br>Example: `54312` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | Alias to [SrcIpAddr](#srcipaddr) |
| **SrcGeoCountry** | Optional | Country | The country associated with the source IP address.<br><br>Example: `USA` |
| **SrcGeoRegion** | Optional | Region | The region within a country associated with the source IP address.<br><br>Example: `Vermont` |
| **SrcGeoCity** | Optional | City | The city associated with the source IP address.<br><br>Example: `Burlington` |
| **SrcGeoLatitude** | Optional | Latitude | The latitude of the geographical coordinate associated with the source IP address.<br><br>Example: `44.475833` |
| **SrcGeoLongitude** | Optional | Longitude | The longitude of the geographical coordinate associated with the source IP address.<br><br>Example: `73.211944` |
| **SrcRiskLevel** | Optional | Integer | The risk level associated with the source. The value should be adjusted to a range of `0` to `100`, with `0` being benign and `100` being a high risk.<br><br>Example: `90` |
| <a name="srchostname"></a>**SrcHostname** | Recommended | String | The source device hostname, excluding domain information. If no device name is available, store the relevant IP address in this field.This value is mandatory if [SrcIpAddr](#srcipaddr) is specified.<br><br>Example: `DESKTOP-1282V4D` |
| **Hostname** | Alias | | Alias to [SrcHostname](#srchostname) |
|<a name="srcdomain"></a> **SrcDomain** | Recommended | String | The domain of the source device.<br><br>Example: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recommended | Enumerated | The type of  [SrcDomain](#srcdomain), if known. Possible values include:<br>- `Windows` (such as: `contoso`)<br>- `FQDN` (such as: `microsoft.com`)<br><br>Required if [SrcDomain](#srcdomain) is used. |
| **SrcFQDN** | Optional | String | The source device hostname, including domain information when available. <br><br>**Note**: This field supports both traditional FQDN format and Windows domain\hostname format. The [SrcDomainType](#srcdomaintype) field reflects the format used. <br><br>Example: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Optional | String | The ID of the source device as reported in the record.<br><br>For example: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Optional | Enumerated | The type of [SrcDvcId](#srcdvcid), if known. Possible values include:<br> - `AzureResourceId`<br>- `MDEid`<br><br>If multiple IDs are available, use the first one from the list above, and store the others in the **SrcDvcAzureResourceId** and **SrcDvcMDEid**, respectively.<br><br>**Note**: This field is required if [SrcDvcId](#srcdvcid) is used. |
| **SrcDeviceType** | Optional | Enumerated | The type of the source device. Possible values include:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Optional | String | A machine-readable, alphanumeric, unique representation of the source user. Format and supported types include:<br>-  **SID**  (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID**  (Linux): `4578`<br>-  **AADID**  (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>Store the ID type in the [SrcUserIdType](#srcuseridtype) field. <br><br>If other IDs are available, we recommend that you normalize the field names to **SrcUserSid**, **SrcUserUid**, **SrcUserAadId**, **SrcUserOktaId** and **UserAwsId**, respectively. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `S-1-12` |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Optional | Enumerated | The type of the ID stored in the [SrcUserId](#srcuserid) field. Supported values include: `SID`, `UIS`, `AADID`, `OktaId`, and `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Optional | String | The Source username, including domain information when available. Use one of the following formats and in the following order of priority:<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Use the Simple form only if domain information is not available.<br><br>Store the Username type in the [SrcUsernameType](#srcusernametype) field. If other IDs are available, we recommend that you normalize the field names to **SrcUserUpn**, **SrcUserWindows** and **SrcUserDn**.<br><br>For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `AlbertE` |
| <a name="user"></a>**User** | Alias | | Alias to [SrcUsername](#srcusername) |
| <a name="srcusernametype"></a>**SrcUsernameType** | Optional | Enumerated | Specifies the type of the username stored in the [SrcUsername](#srcusername) field. Supported values are: `UPN`, `Windows`, `DN`, and `Simple`. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `Windows` |
| **SrcUserType** | Optional | Enumerated | The type of Actor. Allowed values are:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Note**: The value may be provided in the source record using different terms, which should be normalized to these values. Store the original value in the [EventOriginalUserType](#eventoriginalusertype) field. |
| <a name="eventoriginalusertype"></a>**SrcOriginalUserType** | Optional | String | The original source user type, if provided by the source. |
| **SrcUserDomain** | Optional | String | This field is kept for backward compatibility only. ASIM requires domain information, if available, to be part of the [SrcUsername](#srcusername) field. |
| <a name="srcprocessname"></a>**SrcProcessName**              | Optional     | String     |   The file name of the process initiating the DNS request. This name is typically considered to be the process name.  <br><br>Example: `C:\Windows\explorer.exe`  |
| <a name="process"></a>**Process**        | Alias        |            | Alias to the [SrcProcessName](#srcprocessname) <br><br>Example: `C:\Windows\System32\rundll32.exe`|
| **SrcProcessId**| Optional    | String        | The process ID (PID) of the process initiating the DNS request.<br><br>Example:  `48610176`           <br><br>**Note**: The type is defined as *string* to support varying systems, but on Windows and Linux this value must be numeric. <br><br>If you are using a Windows or Linux machine and used a different type, make sure to convert the values. For example, if you used a hexadecimal value, convert it to a decimal value.    |
| **SrcProcessGuid**              | Optional     | String     |  A generated unique identifier (GUID) of the the process initiating the DNS request.   <br><br> Example: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| <a name="dst"></a>**Dst** | Recommended       | String     |    A unique identifier of the server receiving the DNS request. <br><br>This field may alias the [DstDvcId](#dstdvcid), [DstHostname](#dsthostname), or [DstIpAddr](#dstipaddr) fields. <br><br>Example: `192.168.12.1`       |
| <a name="dstipaddr"></a>**DstIpAddr** | Optional | IP Address | The IP address of the server receiving the DNS request. For a regular DNS request, this value would typically be the reporting device, and in most cases set to `127.0.0.1`.<br><br>Example: `127.0.0.1` |
| **DstGeoCountry** | Optional | Country | The country associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `USA` |
| **DstGeoRegion** | Optional | Region | The region, or state, within a country associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `Vermont` |
| **DstGeoCity** | Optional | City | The city associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `Burlington` |
| **DstGeoLatitude** | Optional | Latitude | The latitude of the geographical coordinate associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `44.475833` |
| **DstGeoLongitude** | Optional | Longitude | The longitude of the geographical coordinate associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `73.211944` |
| **DstcRiskLevel** | Optional | Integer | The risk level associated with the destination. The value should be adjusted to a range of 0 to 100, which 0 being benign and 100 being a high risk.<br><br>Example: `90` |
| **DstPortNumber** | Optional | Integer  | Destination Port number.<br><br>Example: `53` |
| <a name="dsthostname"></a>**DstHostname** | Optional | String | The destination device hostname, excluding domain information. If no device name is available, store the relevant IP address in this field.<br><br>Example: `DESKTOP-1282V4D`<br><br>**Note**: This value is mandatory if [DstIpAddr](#dstipaddr) is specified. |
| <a name="dstdomain"></a>**DstDomain** | Optional | String | The domain of the destination device.<br><br>Example: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | Optional | Enumerated | The type of [DstDomain](#dstdomain), if known. Possible values include:<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>Required if [DstDomain](#dstdomain) is used. |
| **DstFQDN** | Optional | String | The destination device hostname, including domain information when available. <br><br>Example: `Contoso\DESKTOP-1282V4D` <br><br>**Note**: This field supports both traditional FQDN format and Windows domain\hostname format. The [DstDomainType](#dstdomaintype) reflects the format used.   |
| <a name="dstdvcid"></a>**DstDvcId** | Optional | String | The ID of the destination device as reported in the record.<br><br>Example: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | Optional | Enumerated | The type of [DstDvcId](#dstdvcid), if known. Possible values include:<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>If multiple IDs are available, use the first one from the list above, and store the others in the  **DstDvcAzureResourceId** or **DstDvcMDEid** fields, respectively.<br><br>Required if **DstDeviceId** is used.|
| **DstDeviceType** | Optional | Enumerated | The type of the destination device. Possible values include:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name=query></a>**DnsQuery** | Mandatory | FQDN | The domain that needs to be resolved. <br><br>**Note**: Some sources send this query in different formats. For example, in the DNS protocol itself, the query includes a dot (**.**)at the end, which must be removed.<br><br>While the DNS protocol allows for multiple queries in a single request, this scenario is rare, if it's found at all. If the request has multiple queries, store the first one in this field, and then and optionally keep the rest in the [AdditionalFields](normalization-about-schemas.md#additionalfields) field.<br><br>Example: `www.malicious.com` |
| **Domain** | Alias | | Alias to [DnsQuery](#query). |
| **DnsQueryType** | Optional | Integer | This field may contain [DNS Resource Record Type codes](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Example: `28`|
| **DnsQueryTypeName** | Recommended | Enumerated | The field may contain [DNS Resource Record Type](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml) names. <br><br>**Note**: IANA does not define the case for the values, so analytics must normalize the case as needed. If the source provides only a numerical query type code and not a query type name, the parser must include a lookup table to enrich with this value.<br><br>Example: `AAAA`|
| <a name=responsename></a>**DnsResponseName** | Optional | String | The content of the response, as included in the record.<br> <br> The DNS response data is inconsistent across reporting devices, is complex to parse, and has less value for source-agnostic analytics. Therefore the information model does not require parsing and normalization, and Microsoft Sentinel uses an auxiliary function to provide response information. For more information, see [Handling DNS response](#handling-dns-response).|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Mandatory | Enumerated | The [DNS response code](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Note**: IANA does not define the case for the values, so analytics must normalize the case. If the source provides only a numerical response code and not a response code name, the parser must include a lookup table to enrich with this value. <br><br> If this record represents a request and not a response, set to **NA**. <br><br>Example: `NXDOMAIN` |
| **DnsResponseCode** | Optional | Integer | The [DNS numerical response code](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Example: `3`|
| **TransactionIdHex** | Recommended | String | The DNS unique hex transaction ID. |
| **NetworkProtocol** | Optional | Enumerated | The transport protocol used by the network resolution event. The value can be **UDP** or **TCP**, and is most commonly set to **UDP** for DNS. <br><br>Example: `UDP`|
| **DnsQueryClass** | Optional | Integer | The [DNS class ID](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>In practice, only the **IN** class (ID 1) is used, making this field less valuable.|
| **DnsQueryClassName** | Optional | String | The [DNS class name](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>In practice, only the **IN** class (ID 1) is used, making this field less valuable. <br><br>Example: `IN`|
| <a name=flags></a>**DnsFlags** | Optional | List of strings | The flags field, as provided by the reporting device. If flag information is provided in multiple fields, concatenate them with comma as a separator. <br><br>Since DNS flags are complex to parse and are less often used by analytics, parsing and normalization are not required, and Microsoft Sentinel uses an auxiliary function to provide flags information. For more information, see [Handling DNS response](#handling-dns-response). <br><br>Example: `["DR"]`|
| <a name=UrlCategory></a>**UrlCategory** |  Optional | String | A DNS event source may also look up the category of the requested Domains. The field is called **_UrlCategory_** to align with the Microsoft Sentinel network schema. <br><br>**_DomainCategory_** is added as an alias that's fitting to DNS. <br><br>Example: `Educational \\ Phishing` |
| **DomainCategory** | Optional | Alias | Alias to [UrlCategory](#UrlCategory). |
| **ThreatCategory** | Optional | String | If a DNS event source also provides DNS security, it may also evaluate the DNS event. For example, it may search for the IP address or domain in a threat intelligence database, and may assign the domain or IP address with a Threat Category. |
| <a name="dnsnetworkduration"></a>**DnsNetworkDuration** | Optional | Integer | The amount of time, in milliseconds, for the completion of DNS request.<br><br>Example: `1500` |
| **Duration** | Alias | | Alias to [DnsNetworkDuration](#dnsnetworkduration) |
| **DnsFlagsAuthenticated** | Optional | Boolean | The DNS `AD` flag, which is related to DNSSEC, indicates in a response that all data included in the answer and authority sections of the response have been verified by the server according to the policies of that server. see [RFC 3655 Section 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) for more information.    |
| **DnsFlagsAuthoritative** | Optional | Boolean | The DNS `AA` flag indicates whether the response from the server was authoritative    |
| **DnsFlagsCheckingDisabled** | Optional | Boolean | The DNS `CD` flag, which is related to DNSSEC, indicates in a query that non-verified data is acceptable to the system sending the query. see [RFC 3655 Section 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) for more information.   |
| **DnsFlagsRecursionAvailable** | Optional | Boolean | The DNS `RA` flag indicates in a response that  that server supports recursive queries.   |
| **DnsFlagsRecursionDesired** | Optional | Boolean | The DNS `RD` flag indicates in a request that that client would like the server to use recursive queries.   |
| **DnsFlagsTruncates** | Optional | Boolean | The DNS `TC` flag indicates that a response was truncates as it exceeded the maximum response size.  |
| **DnsFlagsZ** | Optional | Boolean | The DNS `Z` flag is a deprecated DNS flag, which might be reported by older DNS systems.  |
|<a name="dnssessionid"></a>**DnsSessionId** | Optional | string | The DNS session identifier as reported by the reporting device. <br><br>Example: `EB4BFA28-2EAD-4EF7-BC8A-51DF4FDF5B55` |
| **SessionId** | Alias | String | Alias to [DnsSessionId](#dnssessionid) |
| | | | |

### Deprecated aliases

The following fields are aliases that, although currently deprecated, are maintained for backwards compatibility. They will be removed from the schema on December 31st, 2021.

- Query (alias to DnsQuery)
- QueryType (alias to DnsQueryType)
- QueryTypeName (alias to DnsQueryTypeName)
- ResponseName (alias to DnsReasponseName)
- ResponseCodeName (alias to DnsResponseCodeName)
- ResponseCode (alias to DnsResponseCode)
- QueryClass (alias to DnsQueryClass)
- QueryClassName (alias to DnsQueryClassName)
- Flags (alias to DnsFlags)

### Schema updates

These are the changes in version 0.1.2 of the schema:
- Added the field `EventSchema` - currently optional, but will become mandatory on January 1st, 2022.
- Added dedicated flag field augmenting the combined **[Flags](#flags)** field: `DnsFlagsAuthoritative`, `DnsFlagsCheckingDisabled`, `DnsFlagsRecursionAvailable`, `DnsFlagsRecursionDesired`, `DnsFlagsTruncates`, and `DnsFlagsZ`.

These are the changes in version 0.1.3 of the schema:
- The schema now explicitly documents `Src*`, `Dst*`, `Process*` and `User*` fields.
- Added additional `Dvc*` fields to match the latest common fields definition. 
- Added `Src` and `Dst` as aliases to a leading identifier for the source and destination systems.
- Added optional `DnsNetworkDuration` and `Duration`, an alias to it.
- Added optional Geo Location and Risk Level fields.

## Handling DNS response

In most cases, logged DNS events do not include response information, which may be large and detailed. If your record includes more response information, store it in the [ResponseName](#responsename) field as it appears in the record.

You can also provide an extra KQL function called `_imDNS<vendor>Response_`, which takes the unparsed response as input and returns dynamic value with the following structure:

```kql
[
    {
        "part": "answer"
        "query": "yahoo.com."
        "TTL": 1782
        "Class": "IN"
        "Type": "A"
        "Response": "74.6.231.21"
    }
    {
        "part": "authority"
        "query": "yahoo.com."
        "TTL": 113066
        "Class": "IN"
        "Type": "NS"
        "Response": "ns5.yahoo.com"
    }
    ...
]
```

The fields in each dictionary in the dynamic value correspond to the fields in each DNS response. The `part` entry should include either `answer`, `authority`, or `additional` to reflect the part in the response that the dictionary belongs to.

> [!TIP]
> To ensure optimal performance, call the `imDNS<vendor>Response` function only when needed, and only after an initial filtering to ensure better performance.
>

## Handling DNS flags

Parsing and normalization are not required for flag data. Instead, store the flag data provided by the reporting device in the [Flags](#flags) field. If determining the value of individual flags is straight forward, you can also use the dedicated flags fields. 

You can also provide an extra KQL function called `_imDNS<vendor>Flags_`, which takes the unparsed response, or dedicated flag fields, as input and returns a dynamic list, with Boolean values that represent each flag in the following order:

- Authenticated (AD)
- Authoritative (AA)
- Checking Disabled (CD)
- Recursion Available (RA)
- Recursion Desired (RD)
- Truncated (TC)
- Z

## Next steps

For more information, see:

- Watch the [ASIM Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) or review the [slides](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Advanced SIEM Information Model (ASIM) overview](normalization.md)
- [Advanced SIEM Information Model (ASIM) schemas](normalization-about-schemas.md)
- [Advanced SIEM Information Model (ASIM) parsers](normalization-parsers-overview.md)
- [Advanced SIEM Information Model (ASIM) content](normalization-content.md)
