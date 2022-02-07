---
title: Monitor and troubleshoot sign-ins with continuous access evaluation in Azure AD
description: Troubleshoot and respond to changes in user state faster with continuous access evaluation in Azure AD

services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/25/2022

ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: vmahtani

ms.collection: M365-identity-device-management
---
# Monitor and troubleshoot continuous access evaluation

Administrators can monitor and troubleshoot sign in events where [continuous access evaluation (CAE)](concept-continuous-access-evaluation.md) is applied in multiple ways.

## Continuous access evaluation sign-in reporting

Administrators will have the opportunity to monitor user sign-ins where CAE is applied. This pane can be located by via the following instructions:

1.	Sign in to the **Azure portal** as a Conditional Access Administrator, Security Administrator, or Global Administrator.
1.	Browse to **Azure Active Directory** > **Sign-ins**. 
1.	Apply the **Is CAE Token** filter. 

[ ![Add a filter to the Sign-ins log to see where CAE is being applied or not](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png#lightbox)

From here, admins will be presented with information about their user’s sign-in events. Select any sign-in to see details about the session, like which Conditional Access policies were applied and is CAE enabled. 

There are multiple sign-in requests for each authentication. Some will be shown on the interactive tab, while others will be shown on the non-interactive tab. CAE will only be displayed as true for one of the requests, and it can be on the interactive tab or non-interactive tab. Admins need to check both tabs to confirm whether the user's authentication is CAE enabled or not. 

### Searching for specific sign-in attempts

Use filters to narrow your search. For example, if a user signed in to Teams, use the Application filter and set it to Teams. Admins may need to check the sign-ins from both interactive and non-interactive tabs to locate the specific sign-in. To further narrow the search, admins may apply multiple filters.

## Continuous access evaluation workbooks

The continuous access evaluation insights workbook allows administrators to view and monitor CAE usage insights for their tenants. The table displays authentication attempts with IP mismatches. This workbook can be found as template under the Conditional Access category. 

### Accessing the CAE workbook template

Log Analytics integration must be completed before workbooks are displayed. For more information about how to stream Azure AD sign-in logs to a Log Analytics workspace, see the article [Integrate Azure AD logs with Azure Monitor logs](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).
 
1.	Sign in to the **Azure portal** as a Conditional Access Administrator, Security Administrator, or Global Administrator. 
1.	Browse to **Azure Active Directory** > **Workbooks**.
1.	Under **Public Templates**, search for **Continuous access evaluation insights**.

[ ![Find the CAE insights workbook in the gallery to continue monitoring](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png#lightbox)

The **Continuous access evaluation insights** workbook contains the following table:

### Potential IP address mismatch between Azure AD and resource provider  

![Workbook table 1 showing potential IP address mismatches](./media/howto-continuous-access-evaluation-troubleshoot/continuous-access-evaluation-insights-workbook-table-1.png)

The potential IP address mismatch between Azure AD & resource provider table allows admins to investigate sessions where the IP address detected by Azure AD doesn't match with the IP address detected by the resource provider. 

This workbook table sheds light on these scenarios by displaying the respective IP addresses and whether a CAE token was issued during the session. 

#### IP address configuration

Your identity provider and resource providers may see different IP addresses. This mismatch may happen because of the following examples:

- Your network implements split tunneling.
- Your resource provider is using an IPv6 address and Azure AD is using an IPv4 address.
- Because of network configurations, Azure AD sees one IP address from the client and your resource provider sees a different IP address from the client.

If this scenario exists in your environment, to avoid infinite loops, Azure AD will issue a one-hour CAE token and won't enforce client location change during that one-hour period. Even in this case, security is improved compared to traditional one-hour tokens since we're still evaluating the other events besides client location change events.

Admins can view records filtered by time range and application. Admins can compare the number of mismatched IPs detected with the total number of sign-ins during a specified time period. 

To unblock users, administrators can add specific IP addresses to a trusted named location.

1.	Sign in to the **Azure portal** as a Conditional Access Administrator, Security Administrator, or Global Administrator. 
1.	Browse to **Azure Active Directory** > **Security** > **Conditional Access** > **Named locations**. Here you can create or update trusted IP locations.

> [!NOTE]
> Before adding an IP address as a trusted named location, confirm that the IP address does in fact belong to the intended organization.

For more information about named locations, see the article [Using the location condition](location-condition.md#named-locations)
 
## Next steps

- [Integrate Azure AD logs with Azure Monitor logs](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [Using the location condition](location-condition.md#named-locations)
- [Continuous access evaluation](concept-continuous-access-evaluation.md)
