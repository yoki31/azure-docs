---
title: Microsoft Sentinel user management normalization schema reference (Public Preview) | Microsoft Docs
description: This article describes the Microsoft Sentinel user management normalization schema.
author: oshezaf
ms.topic: reference
ms.date: 02/06/2022
ms.author: ofshezaf
---

# Microsoft Sentinel user management normalization schema reference (preview)

The Microsoft Sentinel user management normalization schema is used to describe user management activities, such as creating a user or a group, changing user attribute, or adding a user to a group. Such events are reported, for example, by operating systems, directory services, identity management systems, and any other system reporting on its local user management activity.

For more information about normalization in Microsoft Sentinel, see [Normalization and the Advanced SIEM Information Model (ASIM)](normalization.md).

> [!IMPORTANT]
> The user management normalization schema is currently in *preview*. This feature is provided without a service level agreement. We don't recommend it for production workloads.
>
> The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>



## Schema overview

The ASIM user management schema describes user management activities. The activities typically include the following entities:
- **Actor** - the user performing the management activity.
- **Acting Process** - the process used by the actor to perform the management activity.
- **Src** - when the activity is performed over the network, the source device from which the activity was initiated.
- **Target User** - the user who's account is managed.
- **Group** the target user is added or removed from, or being modified. 

Some activities, such as **UserCreated**, **GroupCreated**, **UserModified**, and *GroupModified**, set or update user properties. The property set or updated is documented in the following fields:
- [EventSubType](#eventsubtype) - the name of the value that was set or updated. [UpdatedPropertyName](#updatedpropertyname) is an alias to **EventSubType** when [EventSubType](#eventsubtype) refers to one of the relevant event types.
- [PreviousPropertyValue](#previouspropertyvalue) - the previous value of the property.
- [NewPropertyValue](#newpropertyvalue) - the updated value of the property.

## Schema details

### Common fields

> [!IMPORTANT]
> Fields common to all schemas are described in the [ASIM schema overview](normalization-about-schemas.md#common). The following list mentions only fields that have specific guidelines for user management events.
>

| Field               | Class       | Type       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventType** | Mandatory | Enumerated | Describes the operation reported by the record.<br><br> For User Management activity, the supported values are:<br> - `UserCreated`<br> - `UserDeleted`<br> - `UserModified`<br> - `UserLocked`<br> - `UserUnlocked`<br> - `UserDisabled`<br> - `UserEnabled`<br> - `PasswordChanged`<br> - `PasswordReset`<br> - `GroupCreated`<br> - `GroupDeleted`<br> - `GroupModified`<br> - `UserAddedToGroup`<br> - `UserRemovedFromGroup`<br> - `GroupEnumerated`<br> - `UserRead`<br> - `GroupRead`<br> |
| <a name="eventsubtype"></a>**EventSubType** | Optional | Enumerated | The following sub-types are supported:<br> - `UserRead`: Password, Hash<br> - `UserCreated`, `GroupCreated`, `UserModified`, `GroupModified`. For more information, see [UpdatedPropertyName](#updatedpropertyname) |
| **EventResult** | Mandatory | Enumerated | While failure is possible, most systems report only successful user management events. The expected value for successful events is `Success`. |
| **EventResultDetails** | Optional | Enumerated | The valid values are `NotAuthorized` and `Other`. |
| **EventSeverity** | Mandatory | Enumerated | While any valid severity value is allowed, the severity of user management events is typically `Informational`. |
| **EventSchema** | Mandatory | String | The name of the schema documented here is `UserManagement`. |
| **EventSchemaVersion**  | Mandatory   | String     | The version of the schema. The version of the schema documented here is `0.1.1`.        |
| **Dvc** fields| | | For user management events, device fields refer to the system reporting the event. This is usually the system on which the user is managed. |
| | | | |

### Updated property fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="updatedpropertyname"></a>**UpdatedPropertyName** | Alias | | Alias to [EventSubType](#eventsubtype) when the Event Type is `UserCreated`, `GroupCreated`, `UserModified`, or `GroupModified`.<br><br>Supported values are:<br>- `MultipleProperties`: Used when the activity updates multiple properties<br>- `Previous<PropertyName>`, where `<PropertyName>` is one of the supported values for `UpdatedPropertyName`. <br>- `New<PropertyName>`, where `<PropertyName>` is one of the supported values for `UpdatedPropertyName`. |
| <a name="previouspropertyvalue"></a>**PreviousPropertyValue** | Optional | String | The previous value that was stored in the specified property. |
| <a name="newpropertyvalue"></a>**NewPropertyValue** | Optional | String | The new value stored in the specified property. |
|||||

### Target user fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="targetuserid"></a>**TargetUserId** | Optional | String | A machine-readable, alphanumeric, unique representation of the target user. <br><br>Supported formats and types include:<br>- **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>Store the ID type in the [TargetUserIdType](#targetuseridtype) field. If other IDs are available, we recommend that you normalize the field names to **TargetUserSid**, **TargetUserUid**, **TargetUserAADID**, **TargetUserOktaId**, and **TargetUserAwsId**, respectively. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `S-1-12` |
| <a name="targetuseridtype"></a>**TargetUserIdType** | Optional | Enumerated | The type of the ID stored in the [TargetUserId](#targetuserid) field. <br><br>Supported values are `SID`, `UID`, `AADID`, `OktaId`, and `AWSId`. |
| <a name="targetusername"></a>**TargetUsername** | Optional | String | The target username, including domain information when available. <br><br>Use one of the following formats and in the following order of priority:<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Use the Simple form only if domain information isn't available.<br><br>Store the Username type in the [TargetUsernameType](#targetusernametype) field. If other IDs are available, we recommend that you normalize the field names to **TargetUserUpn**, **TargetUserWindows**, and **TargetUserDn**. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `AlbertE` |
| <a name="targetusernametype"></a>**TargetUsernameType** | Optional | Enumerated | Specifies the type of the username stored in the [TargetUsername](#targetusername) field. Supported values include `UPN`, `Windows`, `DN`, and `Simple`. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `Windows` |
| **TargetUserType** | Optional | Enumerated | The type of target user. Supported values include:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Note**: The value might be provided in the source record by using different terms, which should be normalized to these values. Store the original value in the [TargetOriginalUserType](#targetoriginalusertype) field. |
| <a name="targetoriginalusertype"></a>**TargetOriginalUserType** | Optional | String | The original destination user type, if provided by the source. |
|||||

### Actor fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="actoruserid"></a>**ActorUserId** | Optional | String | A machine-readable, alphanumeric, unique representation of the Actor. <br><br>Supported formats and types include:<br>- **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux): `4578`<br>- **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>- **OktaId**: `00urjk4znu3BcncfY0h7`<br>- **AWSId**: `72643944673`<br><br>Store the ID type in the [ActorUserIdType](#actoruseridtype) field. If other IDs are available, we recommend that you normalize the field names to **ActorUserSid**, **ActorUserUid**, **ActorUserAadId**, **ActorUserOktaId**, and **ActorAwsId**, respectively. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: S-1-12 |
| <a name="actoruseridtype"></a>**ActroUserIdType** | Optional | Enumerated | The type of the ID stored in the [ActorUserId](#actoruserid) field. Supported values include `SID`, `UID`, `AADID`, `OktaId`, and `AWSId`. |
| <a name="actorusername"></a>**ActorUsername** | Mandatory | String | The Actor username, including domain information when available. <br><br>Use one of the following formats and in the following order of priority:<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Use the Simple form only if domain information isn't available.<br><br>Store the Username type in the [ActorUsernameType](#actorusernametype) field. If other IDs are available, we recommend that you normalize the field names to **ActorUserUpn**, **ActorUserWindows**, and **ActorUserDn**.<br><br>For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `AlbertE` |
| <a name="user"></a>**User** | Alias | | Alias to [ActorUsername](#actorusername). |
| <a name="actorusernametype"></a>**ActorUsernameType** | Mandatory | Enumerated | Specifies the type of the username stored in the [ActorUsername](#actorusername) field. Supported values are `UPN`, `Windows`, `DN`, and `Simple`. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `Windows` |
| **ActorUserType** | Optional | Enumerated | The type of the Actor. Allowed values are:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Note**: The value might be provided in the source record by using different terms, which should be normalized to these values. Store the original value in the [ActorOriginalUserType](#actororiginalusertype) field. |
| <a name="actororiginalusertype"></a>**ActorOriginalUserType** | | | The original actor user type, if provided by the source. |
| **ActorSessionId** | Optional     | String     |   The unique ID of the login session of the Actor.  <br><br>Example: `999`<br><br>**Note**: The type is defined as *string* to support varying systems, but on Windows this value must be numeric. <br><br>If you are using a Windows machine and used a different type, make sure to convert the values. For example, if you used a hexadecimal value, convert it to a decimal value.   |
|||||

### Group fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="groupid"></a>**GroupId** | Optional | String | A machine-readable, alphanumeric, unique representation of the group, for activities involving a group. <br><br>Supported formats and types include:<br>- **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux): `4578`<br><br>Store the ID type in the [GroupIdType](#groupidtype) field. If other IDs are available, we recommend that you normalize the field names to **GroupSid** or **GroupUid**, respectively. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `S-1-12` |
| <a name="groupidtype"></a>**GroupIdType** | Optional | Enumerated | The type of the ID stored in the [GroupId](#groupid) field. <br><br>Supported values are `SID`, and `UID`. |
| <a name="groupname"></a>**GroupName** | Optional | String | The group name, including domain information when available, for activities involving a group. <br><br>Use one of the following formats and in the following order of priority:<br>- **Upn/Email**: `grp@contoso.com`<br>- **Windows**: `Contoso\grp`<br>- **DN**: `CN=grp,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `grp`. Use the Simple form only if domain information isn't available.<br><br>Store the group name type in the [GroupNameType](#groupnametype) field. If other IDs are available, we recommend that you normalize the field names to **GroupUpn**, **GorupNameWindows**, and **GroupDn**.<br><br>Example: `Contoso\Finance` |
| <a name="groupnametype"></a>**GroupNameType** | Optional | Enumerated | Specifies the type of the group name stored in the [GroupName](#groupname) field. Supported values include `UPN`, `Windows`, `DN`, and `Simple`.<br><br>Example: `Windows` |
| **GroupType** | Optional | Enumerated | The type of the group, for activities involving a group. Supported values include:<br>- `Local Distribution`<br>- `Local Security Enabled`<br>- `Global Distribution`<br>- `Global Security Enabled`<br>- `Universal Distribution`<br>- `Universal Security Enabled`<br>- `Other`<br><br>**Note**: The value might be provided in the source record by using different terms, which should be normalized to these values. Store the original value in the [GroupOriginalType](#grouporiginaltype) field. |
| <a name="grouporiginaltype"></a>**GroupOriginalType** | Optional | String | The original group type, if provided by the source. |
|||||

### Source fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="src"></a>**Src** | Recommended       | String     |    A unique identifier of the source device. <br><br>This field might alias the [SrcDvcId](#srcdvcid), [SrcHostname](#srchostname), or [SrcIpAddr](#srcipaddr) fields. <br><br>Example: `192.168.12.1`       |
| <a name="srcipaddr"></a>**SrcIpAddr** | Recommended | IP address | The IP address of the source device. This value is mandatory if **SrcHostname** is specified.<br><br>Example: `77.138.103.108` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | Alias to [SrcIpAddr](#srcipaddr). |
| <a name="srchostname"></a> **SrcHostname** | Recommended | String | The source device hostname, excluding domain information.<br><br>Example: `DESKTOP-1282V4D` |
|<a name="srcdomain"></a> **SrcDomain** | Recommended | String | The domain of the source device.<br><br>Example: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recommended | Enumerated | The type of [SrcDomain](#srcdomain), if known. Possible values include:<br>- `Windows` (such as `contoso`)<br>- `FQDN` (such as `microsoft.com`)<br><br>Required if [SrcDomain](#srcdomain) is used. |
| **SrcFQDN** | Optional | String | The source device hostname, including domain information when available. <br><br>**Note**: This field supports both traditional FQDN format and Windows domain\hostname format. The [SrcDomainType](#srcdomaintype) field reflects the format used. <br><br>Example: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Optional | String | The ID of the source device as reported in the record.<br><br>Example: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Optional | Enumerated | The type of [SrcDvcId](#srcdvcid), if known. Possible values include:<br> - `AzureResourceId`<br>- `MDEid`<br><br>If multiple IDs are available, use the first one from the preceding list, and store the others in **SrcDvcAzureResourceId** and **SrcDvcMDEid**, respectively.<br><br>**Note**: This field is required if [SrcDvcId](#srcdvcid) is used. |
| **SrcDeviceType** | Optional | Enumerated | The type of the source device. Possible values include:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| **SrcGeoCountry** | Optional | Country | The country associated with the source IP address.<br><br>Example: `USA` |
| **SrcGeoRegion** | Optional | Region | The region within a country associated with the source IP address.<br><br>Example: `Vermont` |
| **SrcGeoCity** | Optional | City | The city associated with the source IP address.<br><br>Example: `Burlington` |
| **SrcGeoLatitude** | Optional | Latitude | The latitude of the geographical coordinate associated with the source IP address.<br><br>Example: `44.475833` |
| **SrcGeoLongitude** | Optional | Longitude | The longitude of the geographical coordinate associated with the source IP address.<br><br>Example: `73.211944` |
| | | | |

### Acting Application

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| **ActingAppId** | Optional | String | The ID of the application used by the actor to perform the activity, including a process, browser, or service. <br><br>For example: `0x12ae8` |
| **ActiveAppName** | Optional | String | The name of the application used by the actor to perform the activity, including a process, browser, or service. <br><br>For example: `C:\Windows\System32\svchost.exe` |
| **ActingAppType** | Optional | Enumerated | The type of acting application. Supported values include: <br>- `Process` <br>- `Browser` <br>- `Resource` <br>- `Other` |
| **HttpUserAgent** |	Optional	| String |	When authentication is performed over HTTP or HTTPS, this field's value is the user_agent HTTP header provided by the acting application when performing the authentication.<br><br>For example: `Mozilla/5.0 (iPhone; CPU iPhone OS 12_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0 Mobile/15E148 Safari/604.1` |
|||||

### Additional fields and aliases

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="hostname"></a>**Hostname** | Alias | | Alias to [DvcHostname](normalization-about-schemas.md#dvchostname). |
|||||


## Next steps

For more information, see:

- Watch the [ASIM Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) or review the [slides](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Advanced SIEM Information Model (ASIM) overview](normalization.md)
- [Advanced SIEM Information Model (ASIM) schemas](normalization-about-schemas.md)
- [Advanced SIEM Information Model (ASIM) parsers](normalization-parsers-overview.md)
- [Advanced SIEM Information Model (ASIM) content](normalization-content.md)

