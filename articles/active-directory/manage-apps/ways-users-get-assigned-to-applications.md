---
title: Understand how users are assigned to apps
description: Understand how users get assigned to an app that is using Azure Active Directory for identity management.
titleSuffix: Azure AD
services: active-directory
author: eringreenlee
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: ergreenl
ms.reviewer: davidmu
---

# Understand how users are assigned to apps

This article help you to understand how users get assigned to an application in your tenant.

## How do users get assigned an application in Azure AD?

There are several ways a user can be assigned an application. Assignment can be performed by an administrator, a business delegate, or sometimes, the user themselves. Below describes the ways users can get assigned to applications:

* An administrator [assigns a user](./assign-user-or-group-access-portal.md) to the application directly
* An administrator [assigns a group](./assign-user-or-group-access-portal.md) that the user is a member of to the application, including:

  * A group that was synchronized from on-premises
  * A static security group created in the cloud
  * A [dynamic security group](../enterprise-users/groups-dynamic-membership.md) created in the cloud
  * A Microsoft 365 group created in the cloud
  * The [All Users](../fundamentals/active-directory-groups-create-azure-portal.md) group
* An administrator enables [Self-service Application Access](./manage-self-service-access.md) to allow a user to add an application using [My Apps](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510) **Add App** feature **without business approval**
* An administrator enables [Self-service Application Access](./manage-self-service-access.md) to allow a user to add an application using [My Apps](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510) **Add App** feature, but only **with prior approval from a selected set of business approvers**
* An administrator enables [Self-service Group Management](../enterprise-users/groups-self-service-management.md) to allow a user to join a group that an application is assigned to **without business approval**
* An administrator enables [Self-service Group Management](../enterprise-users/groups-self-service-management.md) to allow a user to join a group that an application is assigned to, but only **with prior approval from a selected set of business approvers**
* An administrator assigns a license to a user directly, for a Microsoft service such as [Microsoft 365](https://products.office.com/)
* An administrator assigns a license to a group that the user is a member of, for a Microsoft service such as [Microsoft 365](https://products.office.com/)
* A user [consents to an application](consent-and-permissions-overview.md#user-consent) on behalf of themselves.

## Next steps

* [Quickstart Series on Application Management](view-applications-portal.md)
* [What is application management?](what-is-application-management.md)
* [What is single sign-on?](what-is-single-sign-on.md)
