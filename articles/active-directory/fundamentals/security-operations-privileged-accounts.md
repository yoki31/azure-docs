---
title: Azure Active Directory security operations for privileged accounts
description: Learn to set baselines, and then monitor and alert on potential security issues with privileged accounts in Azure Active directory.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.collection: M365-identity-device-management
---

# Security operations for privileged accounts

The security of business assets depends on the integrity of the privileged accounts that administer your IT systems. Cyber attackers use credential theft attacks and other means to target privileged accounts and gain access to sensitive data.

Traditionally, organizational security has focused on the entry and exit points of a network as the security perimeter. However, software as a service (SaaS) applications and personal devices on the internet have made this approach less effective.

Azure Active Directory (Azure AD) uses identity and access management (IAM) as the control plane. In your organization's identity layer, users assigned to privileged administrative roles are in control. The accounts used for access must be protected, whether the environment is on-premises, in the cloud, or a hybrid environment.

You're entirely responsible for all layers of security for your on-premises IT environment. When you use Azure services, prevention and response are the joint responsibilities of Microsoft as the cloud service provider and you as the customer.

* For more information on the shared responsibility model, see [Shared responsibility in the cloud](../../security/fundamentals/shared-responsibility.md).
* For more information on securing access for privileged users, see [Securing privileged access for hybrid and cloud deployments in Azure AD](../roles/security-planning.md).
* For a wide range of videos, how-to guides, and content of key concepts for privileged identity, see [Privileged Identity Management documentation](../privileged-identity-management/index.yml).

## Where to look

The log files you use for investigation and monitoring are:

* [Azure AD Audit logs](../reports-monitoring/concept-audit-logs.md)
* [Microsoft 365 Audit logs](/microsoft-365/compliance/auditing-solutions-overview)
* [Azure Key Vault insights](../../azure-monitor/insights/key-vault-insights-overview.md)

From the Azure portal, you can view the Azure AD Audit logs and download as comma-separated value (CSV) or JavaScript Object Notation (JSON) files. The Azure portal has several ways to integrate Azure AD logs with other tools that allow for greater automation of monitoring and alerting:

* [Microsoft Sentinel](../../sentinel/overview.md): Enables intelligent security analytics at the enterprise level by providing security information and event management (SIEM) capabilities.
* [Azure Monitor](../../azure-monitor/overview.md): Enables automated monitoring and alerting of various conditions. Can create or use workbooks to combine data from different sources.
* [Azure Event Hubs](../../event-hubs/event-hubs-about.md) integrated with a SIEM: Enables [Azure AD logs to be pushed to other SIEMs](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) such as Splunk, ArcSight, QRadar, and Sumo Logic via the Azure Event Hubs integration.
* [Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security): Enables you to discover and manage apps, govern across apps and resources, and check your cloud apps' compliance.
* **Microsoft Graph**: Enables you to export data and use Microsoft Graph to do more analysis. For more information on Microsoft Graph, see [Microsoft Graph PowerShell SDK and Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md).
* [Identity Protection](../identity-protection/overview-identity-protection.md): Generates three key reports you can use to help with your investigation:

   * **Risky users**: Contains information about which users are at risk, details about detections, history of all risky sign-ins, and risk history.
   * **Risky sign-ins**: Contains information about a sign-in that might indicate suspicious circumstances. For more information on investigating information from this report, see [Investigate risk](../identity-protection/howto-identity-protection-investigate-risk.md).
   * **Risk detections**: Contains information about other risks triggered when a risk is detected and other pertinent information such as sign-in location and any details from Microsoft Defender for Cloud Apps.

Although we discourage the practice, privileged accounts can have standing administration rights. If you choose to use standing privileges, and the account is compromised, it can have a strongly negative effect. We recommend you prioritize monitoring privileged accounts and include the accounts in your Privileged Identity Management (PIM) configuration. For more information on PIM, see [Start using Privileged Identity Management](../privileged-identity-management/pim-getting-started.md). Also, we recommend you validate that admin accounts:

* Are required.
* Have the least privilege to execute the require activities.
* Are protected with multifactor authentication (MFA) at a minimum.
* Are run from privileged access workstation (PAW) or secure admin workstation (SAW) devices.

The rest of this article describes what we recommend you monitor and alert on. The article is organized by the type of threat. Where there are specific prebuilt solutions, we link to them following the table. Otherwise, you can build alerts by using the preceding tools.

Specifically, this article provides details on setting baselines and auditing sign-in and usage of privileged accounts. It also discusses tools and resources you can use to help maintain the integrity of your privileged accounts. The content is organized into the following subjects:

* Emergency "break-glass" accounts
* Privileged account sign-in
* Privileged account changes
* Privileged groups
* Privilege assignment and elevation

## Emergency access accounts

It's important that you prevent being accidentally locked out of your Azure AD tenant. You can mitigate the effect of an accidental lockout by creating emergency access accounts in your organization. Emergency access accounts are also known as break-glass accounts, as in "break glass in case of emergency" messages found on physical security equipment like fire alarms.

Emergency access accounts are highly privileged, and they aren't assigned to specific individuals. Emergency access accounts are limited to emergency or break-glass scenarios where normal privileged accounts can't be used. An example is when a Conditional Access policy is misconfigured and locks out all normal administrative accounts. Restrict emergency account use to only the times when it's absolutely necessary.

For guidance on what to do in an emergency, see [Secure access practices for administrators in Azure AD](../roles/security-planning.md).

Send a high-priority alert every time an emergency access account is used.

### Discovery

Because break-glass accounts are only used if there's an emergency, your monitoring should discover no account activity. Send a high-priority alert every time an emergency access account is used or changed. Any of the following events might indicate a bad actor is trying to compromise your environments:

* **Account used**: Monitor and alert on any activity by using this type of account, such as:
   * Sign-in.
   * Account password change.
   * Account permission or roles changed.
   * Credential or auth method added or changed.

For more information on managing emergency access accounts, see [Manage emergency access admin accounts in Azure AD](../roles/security-emergency-access.md). For detailed information on creating an alert for an emergency account, see [Create an alert rule](../roles/security-emergency-access.md).

## Privileged account sign-in

Monitor all privileged account sign-in activity by using the Azure AD Sign-in logs as the data source. In addition to sign-in success and failure information, the logs contain the following details:

* Interrupts
* Device
* Location
* Risk
* Application
* Date and time
* Is the account disabled
* Lockout
* MFA fraud
* Conditional Access failure

### Things to monitor

You can monitor privileged account sign-in events in the Azure AD Sign-in logs. Alert on and investigate the following events for privileged accounts.

| What to monitor | Risk level |  Where |  Filter/subfilter | Notes |
| - | - | - | - | - |
| Sign-in failure, bad password threshold | High | Azure AD Sign-ins log | Status = Failure<br>-and-<br>error code = 50126 | Define a baseline threshold and then monitor and adjust to suit your organizational behaviors and limit false alerts from being generated.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PrivilegedAccountsSigninFailureSpikes.yaml) |
| Failure because of Conditional Access requirement |High | Azure AD Sign-ins log | Status = Failure<br>-and-<br>error code = 53003<br>-and-<br>Failure reason = Blocked by Conditional Access | This event can be an indication an attacker is trying to get into the account.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/SigninLogs/UserAccounts-CABlockedSigninSpikes.yaml) |
| Privileged accounts that don't follow naming policy| | Azure subscription | [List Azure role assignments using the Azure portal - Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| List role assignments for subscriptions and alert where the sign-in name doesn't match your organization's format. An example is the use of ADM_ as a prefix. |
| Interrupt |  High, medium | Azure AD Sign-ins | Status = Interrupted<br>-and-<br>error code = 50074<br>-and-<br>Failure reason = Strong auth required<br>Status = Interrupted<br>-and-<br>Error code = 500121<br>Failure reason = Authentication failed during strong authentication request | This event can be an indication an attacker has the password for the account but can't pass the MFA challenge.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/AADPrivilegedAccountsFailedMFA.yaml) |
| Privileged accounts that don't follow naming policy| High | Azure AD directory | [List Azure AD role assignments](../roles/view-assignments.md)| List role assignments for Azure AD roles and alert where the UPN doesn't match your organization's format. An example is the use of ADM_ as a prefix. |
| Discover privileged accounts not registered for MFA | High | Microsoft Graph API| Query for IsMFARegistered eq false for admin accounts. [List credentialUserRegistrationDetails - Microsoft Graph beta](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http) | Audit and investigate to determine if the event is intentional or an oversight. |
| Account lockout | High | Azure AD Sign-ins log | Status = Failure<br>-and-<br>error code = 50053 | Define a baseline threshold, and then monitor and adjust to suit your organizational behaviors and limit false alerts from being generated.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PrivilegedAccountsLockedOut.yaml) |
| Account disabled or blocked for sign-ins | Low | Azure AD Sign-ins log | Status = Failure<br>-and-<br>Target = User UPN<br>-and-<br>error code = 50057 | This event could indicate someone is trying to gain access to an account after they've left the organization. Although the account is blocked, it's still important to log and alert on this activity.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/SigninLogs/UserAccounts-BlockedAccounts.yaml) |
| MFA fraud alert or block | High | Azure AD Sign-ins log/Azure Log Analytics | Sign-ins>Authentication details Result details = MFA denied, fraud code entered | Privileged user has indicated they haven't instigated the MFA prompt, which could indicate an attacker has the password for the account.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/SigninLogs/MFARejectedbyUser.yaml) |
| MFA fraud alert or block | High | Azure AD Audit log log/Azure Log Analytics | Activity type = Fraud reported - User is blocked for MFA or fraud reported - No action taken (based on tenant-level settings for fraud report) | Privileged user has indicated they haven't instigated the MFA prompt, which could indicate an attacker has the password for the account.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/SigninLogs/MFARejectedbyUser.yaml) |
| Privileged account sign-ins outside of expected controls |  | Azure AD Sign-ins log | Status = Failure<br>UserPricipalName = \<Admin account\><br>Location = \<unapproved location\><br>IP address = \<unapproved IP\><br>Device info = \<unapproved Browser, Operating System\> | Monitor and alert on any entries that you've defined as unapproved.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/SigninLogs/SuspiciousSignintoPrivilegedAccount.yaml) |
| Outside of normal sign-in times | High | Azure AD Sign-ins log | Status = Success<br>-and-<br>Location =<br>-and-<br>Time = Outside of working hours | Monitor and alert if sign-ins occur outside of expected times. It's important to find the normal working pattern for each privileged account and to alert if there are unplanned changes outside of normal working times. Sign-ins outside of normal working hours could indicate compromise or possible insider threats.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/AnomolousSignInsBasedonTime.yaml) |
| Identity protection risk | High | Identity Protection logs | Risk state = At risk<br>-and-<br>Risk level = Low, medium, high<br>-and-<br>Activity = Unfamiliar sign-in/TOR, and so on | This event indicates there's some abnormality detected with the sign-in for the account and should be alerted on. |
| Password change | High | Azure AD Audit logs | Activity actor = Admin/self-service<br>-and-<br>Target = User<br>-and-<br>Status = Success or failure | Alert on any admin account password changes, especially for global admins, user admins, subscription admins, and emergency access accounts. Write a query targeted at all privileged accounts.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PrivilegedAccountPasswordChanges.yaml) |
| Change in legacy authentication protocol | High | Azure AD Sign-ins log | Client App = Other client, IMAP, POP3, MAPI, SMTP, and so on<br>-and-<br>Username = UPN<br>-and-<br>Application = Exchange (example) | Many attacks use legacy authentication, so if there's a change in auth protocol for the user, it could be an indication of an attack. |
| New device or location | High | Azure AD Sign-ins log | Device info = Device ID<br>-and-<br>Browser<br>-and-<br>OS<br>-and-<br>Compliant/Managed<br>-and-<br>Target = User<br>-and-<br>Location | Most admin activity should be from [privileged access devices](/security/compass/privileged-access-devices), from a limited number of locations. For this reason, alert on new devices or locations.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/SigninLogs/SuspiciousSignintoPrivilegedAccount.yaml) |
| Audit alert setting is changed | High | Azure AD Audit logs | Service = PIM<br>-and-<br>Category = Role management<br>-and-<br>Activity = Disable PIM alert<br>-and-<br>Status = Success | Changes to a core alert should be alerted if unexpected. |

## Changes by privileged accounts

Monitor all completed and attempted changes by a privileged account. This data enables you to establish what's normal activity for each privileged account and alert on activity that deviates from the expected. The Azure AD Audit logs are used to record this type of event. For more information on Azure AD Audit logs, see [Audit logs in Azure Active Directory](../reports-monitoring/concept-audit-logs.md).

### Azure Active Directory Domain Services

Privileged accounts that have been assigned permissions in Azure AD Domain Services can perform tasks for Azure AD Domain Services that affect the security posture of your Azure-hosted virtual machines (VMs) that use Azure AD Domain Services. Enable security audits on VMs and monitor the logs. For more information on enabling Azure AD Domain Services audits and for a list of sensitive privileges, see the following resources:

* [Enable security audits for Azure Active Directory Domain Services](../../active-directory-domain-services/security-audit-events.md)
* [Audit Sensitive Privilege Use](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)

| What to monitor                                                         | Risk level | Where               | Filter/subfilter                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Notes                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------------------------------------------------------------|------------|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Attempted and completed changes                                  | High       | Azure AD Audit logs | Date and time<br>-and-<br>Service<br>-and-<br>Category and name of the activity (what)<br>-and-<br>Status = Success or failure<br>-and-<br>Target<br>-and-<br>Initiator or actor (who)                                                                                                                                                                                                                                                                                                | Any unplanned changes should be alerted on immediately. These logs should be retained to assist in any investigation. Any tenant-level changes should be investigated immediately (link out to Infra doc) that would lower the security posture of your tenant. An example is excluding accounts from MFA or Conditional Access. Alert on any [additions or changes to applications](security-operations-applications.md). |
| **EXAMPLE**<br>Attempted or completed change to high-value apps or services | High       | Audit log           | Service<br>-and-<br>Category and name of the activity                                                                                                                                                                                                                                                                                                                                                                                                                                | <li>Date and time <li>Service <li>Category and name of the activity <li>Status = Success or failure <li>Target <li>Initiator or actor (who)                                                                                                                                                                                                                                                                                        |
| Privileged changes in Azure AD Domain Services                                             | High       | Azure AD Domain Services               | Look for event [4673](/windows/security/threat-protection/auditing/event-4673) | [Enable security audits for Azure Active Directory Domain Services](../../active-directory-domain-services/security-audit-events.md)<br>[Audit Sensitive Privilege use](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use). See the article for a list of all privileged events.                                                                                                                            |

## Changes to privileged accounts

Investigate changes to privileged accounts' authentication rules and privileges, especially if the change provides greater privilege or the ability to perform tasks in your Azure AD environment.

| What to monitor| Risk level| Where| Filter/subfilter| Notes |
| - | - | - | - | - |
| Privileged account creation| Medium| Azure AD Audit logs| Service = Core Directory<br>-and-<br>Category = User management<br>-and-<br>Activity type = Add user<br>-correlate with-<br>Category type = Role management<br>-and-<br>Activity type = Add member to role<br>-and-<br>Modified properties = Role.DisplayName| Monitor creation of any privileged accounts. Look for correlation that's of a short time span between creation and deletion of accounts.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/UserAssignedPrivilegedRole.yaml) |
| Changes to authentication methods| High| Azure AD Audit logs| Service = Authentication Method<br>-and-<br>Activity type = User registered security information<br>-and-<br>Category = User management| This change could be an indication of an attacker adding an auth method to the account so they can have continued access.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/AuthenticationMethodsChangedforPrivilegedAccount.yaml) |
| Alert on changes to privileged account permissions| High| Azure AD Audit logs| Category = Role management<br>-and-<br>Activity type = Add eligible member (permanent)<br>-and-<br>Activity type = Add eligible member (eligible)<br>-and-<br>Status = Success or failure<br>-and-<br>Modified properties = Role.DisplayName| This alert is especially for accounts being assigned roles that aren't known or are outside of their normal responsibilities. |
| Unused privileged accounts| Medium| Azure AD Access Reviews| | Perform a monthly review for inactive privileged user accounts. |
| Accounts exempt from Conditional Access| High| Azure Monitor Logs<br>-or-<br>Access Reviews| Conditional Access = Insights and reporting| Any account exempt from Conditional Access is most likely bypassing security controls and is more vulnerable to compromise. Break-glass accounts are exempt. See information on how to monitor break-glass accounts in a subsequent section of this article.|

For more information on how to monitor for exceptions to Conditional Access policies, see [Conditional Access insights and reporting](../conditional-access/howto-conditional-access-insights-reporting.md).

For more information on discovering unused privileged accounts, see [Create an access review of Azure AD roles in Privileged Identity Management](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md).

## Assignment and elevation

Having privileged accounts that are permanently provisioned with elevated abilities can increase the attack surface and risk to your security boundary. Instead, employ just-in-time access by using an elevation procedure. This type of system allows you to assign eligibility for privileged roles. Admins elevate their privileges to those roles only when they perform tasks that need those privileges. Using an elevation process enables you to monitor elevations and non-use of privileged accounts.

### Establish a baseline

To monitor for exceptions, you must first create a baseline. Determine the following information for:

* **Admin accounts**:

   * Your privileged account strategy
   * Use of on-premises accounts to administer on-premises resources
   * Use of cloud-based accounts to administer cloud-based resources
   * Approach to separating and monitoring administrative permissions for on-premises and cloud-based resources

* **Privileged role protection**:

   * Protection strategy for roles that have administrative privileges
   * Organizational policy for using privileged accounts
   * Strategy and principles for maintaining permanent privilege versus providing time-bound and approved access

The following concepts and information will help you determine policies:

* **Just-in-time admin principles**: Use the Azure AD logs to capture information for performing administrative tasks that are common in your environment. Determine the typical amount of time needed to complete the tasks.
* **Just-enough admin principles**: [Determine the least-privileged role](../roles/delegate-by-task.md), which might be a custom role, that's needed for administrative tasks.
* **Establish an elevation policy**: After you have insight into the type of elevated privilege needed and how long is needed for each task, create policies that reflect elevated privileged usage for your environment. As an example, define a policy to limit Global admin access to one hour.

 After you establish your baseline and set policy, you can configure monitoring to detect and alert usage outside of policy.

### Discovery

Pay particular attention to and investigate changes in assignment and elevation of privilege.

### Things to monitor

You can monitor privileged account changes by using Azure AD Audit logs and Azure Monitor logs. Specifically, include the following changes in your monitoring process.

| What to monitor| Risk level| Where| Filter/subfilter| Notes |
| - | - | - | - | - |
| Added to eligible privileged role| High| Azure AD Audit Logs| Service = PIM<br>-and-<br>Category = Role management​<br>-and-<br>Activity type = Add member to role completed (eligible)<br>-and-<br>Status = Success or failure​<br>-and-<br>Modified properties = Role.DisplayName| Any account eligible for a role is now being given privileged access. If the assignment is unexpected or into a role that isn't the responsibility of the account holder, investigate.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/UserAssignedPrivilegedRole.yaml) |
| Roles assigned out of PIM| High| Azure AD Audit Logs| Service = PIM<br>-and-<br>Category = Role management​<br>-and-<br>Activity type = Add member to role (permanent)<br>-and-<br>Status = Success or failure<br>-and-<br>Modified properties = Role.DisplayName| These roles should be closely monitored and alerted. Users shouldn't be assigned roles outside of PIM where possible.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/PrivlegedRoleAssignedOutsidePIM.yaml) |
| Elevations| Medium| Azure AD Audit Logs| Service = PIM<br>-and-<br>Category = Role management<br>-and-<br>Activity type = Add member to role completed (PIM activation)<br>-and-<br>Status = Success or failure <br>-and-<br>Modified properties = Role.DisplayName| After a privileged account is elevated, it can now make changes that could affect the security of your tenant. All elevations should be logged and, if happening outside of the standard pattern for that user, should be alerted and investigated if not planned. |
| Approvals and deny elevation| Low| Azure AD Audit Logs| Service = Access Review<br>-and-<br>Category = UserManagement<br>-and-<br>Activity type = Request approved or denied<br>-and-<br>Initiated actor = UPN| Monitor all elevations because it could give a clear indication of the timeline for an attack.<br>[Azure Sentinel template](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/PIMElevationRequestRejected.yaml) |
| Changes to PIM settings| High| Azure AD Audit Logs| Service = PIM<br>-and-<br>Category = Role management<br>-and-<br>Activity type = Update role setting in PIM<br>-and-<br>Status reason = MFA on activation disabled (example)| One of these actions could reduce the security of the PIM elevation and make it easier for attackers to acquire a privileged account. |
| Elevation not occurring on SAW/PAW| High| Azure AD Sign In logs| Device ID <br>-and-<br>Browser<br>-and-<br>OS<br>-and-<br>Compliant/Managed<br>Correlate with:<br>Service = PIM<br>-and-<br>Category = Role management<br>-and-<br>Activity type = Add member to role completed (PIM activation)<br>-and-<br>Status = Success or failure<br>-and-<br>Modified properties = Role.DisplayName| If this change is configured, any attempt to elevate on a non-PAW/SAW device should be investigated immediately because it could indicate an attacker is trying to use the account. |
| Elevation to manage all Azure subscriptions| High| Azure Monitor| Activity Log tab <br>Directory Activity tab <br> Operations Name = Assigns the caller to user access admin <br> -and- <br> Event category = Administrative <br> -and-<br>Status = Succeeded, start, fail<br>-and-<br>Event initiated by| This change should be investigated immediately if it isn't planned. This setting could allow an attacker access to Azure subscriptions in your environment. |

For more information about managing elevation, see [Elevate access to manage all Azure subscriptions and management groups](../../role-based-access-control/elevate-access-global-admin.md). For information on monitoring elevations by using information available in the Azure AD logs, see [Azure Activity log](../../azure-monitor/essentials/activity-log.md), which is part of the Azure Monitor documentation.

For information about configuring alerts for Azure roles, see [Configure security alerts for Azure resource roles in Privileged Identity Management](../privileged-identity-management/pim-resource-roles-configure-alerts.md).

 ## Next steps

See these security operations guide articles:

* [Azure AD security operations overview](security-operations-introduction.md)
* [Security operations for user accounts](security-operations-user-accounts.md)
* [Security operations for privileged accounts](security-operations-privileged-accounts.md)
* [Security operations for Privileged Identity Management](security-operations-privileged-identity-management.md)
* [Security operations for applications](security-operations-applications.md)
* [Security operations for devices](security-operations-devices.md)
* [Security operations for infrastructure](security-operations-infrastructure.md)
