---
title: Authenticate to Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Learn about the various ways an app or service can authenticate to Communication Services.
author: probableprime

manager: chpalm
services: azure-communication-services

ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: identity
---

# Authenticate to Azure Communication Services

Every client interaction with Azure Communication Services needs to be authenticated. In a typical architecture, see [client and server architecture](./client-and-server-architecture.md), *access keys* or *Azure AD authentication* are used for server-side authentication.

Another type of authentication uses *user access tokens* to authenticate against services that require user participation. For example, the chat or calling service utilizes *user access tokens* to allow users to be added in a thread and have conversations with each other.

## Authentication Options

The following table shows the Azure Communication Services SDKs and their authentication options:

| SDK    | Authentication option                               |
| ----------------- | ----------------------------------------------------|
| Identity          | Access Key or Azure AD authentication               |
| SMS               | Access Key or Azure AD authentication               |
| Phone Numbers     | Access Key or Azure AD authentication               |
| Calling           | User Access Token                                   |
| Chat              | User Access Token                                   |

Each authorization option is briefly described below:

### Access Key

Access key authentication is suitable for service applications running in a trusted service environment. Your access key can be found in the Azure Communication Services portal. The service application uses it as a credential to initialize the corresponding SDKs. See an example of how it is used in the [Identity SDK](../quickstarts/access-tokens.md). 

Since the access key is part of the connection string of your resource, authentication with a connection string is equivalent to authentication with an access key.

If you wish to call ACS' APIs manually using an access key, then you will need to sign the request. Signing the request is explained, in detail, within a [tutorial](../tutorials/hmac-header-tutorial.md).

### Azure AD authentication

The Azure platform provides role-based access (Azure RBAC) to control access to the resources. Azure RBAC security principal represents a user, group, service principal, or managed identity that is requesting access to Azure resources. Azure AD authentication provides superior security and ease of use over other authorization options. For example, by using managed identity, you avoid having to store your account access key within your code, as you do with Access Key authorization. While you can continue to use Access Key authorization with communication services applications, Microsoft recommends moving to Azure AD where possible. 

To set up a service principal, [create a registered application from the Azure CLI](../quickstarts/identity/service-principal-from-cli.md). Then, the endpoint and credentials can be used to authenticate the SDKs. See examples of how [service principal](../quickstarts/identity/service-principal.md) is used.

Communication services support Azure AD authentication but do not support managed identity for Communication services resources. You can find more details, about the managed identity support in the [Azure Active Directory documentation](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### User Access Tokens

User access tokens are generated using the Identity SDK and are associated with users created in the Identity SDK. See an example of how to [create users and generate tokens](../quickstarts/access-tokens.md). Then, user access tokens are used to authenticate participants added to conversations in the Chat or Calling SDK. For more information, see [add chat to your app](../quickstarts/chat/get-started.md). User access token authentication is different compared to access key and Azure AD authentication in that it is used to authenticate a user rather than a secured Azure resource.

## Using identity for monitoring and metrics

The user identity is intended to act as a primary key for logs and metrics collected through Azure Monitor. If you'd like to get a view of all of a specific user's calls, for example, you should set up your authentication in a way that maps a specific Azure Communication Services identity (or identities) to a singular user. Learn more about [log analytics](../concepts/analytics/log-analytics.md), and [metrics](../concepts/metrics.md) available to you.

## Next steps

> [!div class="nextstepaction"]
> [Create and manage Communication Services resources](../quickstarts/create-communication-resource.md)
> [Create an Azure Active Directory service principal application from the Azure CLI](../quickstarts/identity/service-principal-from-cli.md)
> [Create User Access Tokens](../quickstarts/access-tokens.md)

For more information, see the following articles:
- [Learn about client and server architecture](../concepts/client-and-server-architecture.md)