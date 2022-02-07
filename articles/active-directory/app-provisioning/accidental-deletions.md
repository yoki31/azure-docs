---
title: Enable accidental deletions prevention in Application Provisioning in Azure Active Directory
description: Enable accidental deletions prevention in Application Provisioning in Azure Active Directory.
services: active-directory
author: kenwith
manager: karenhoran
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 09/27/2021
ms.author: kenwith
ms.reviewer: arvinh
---

# Enable accidental deletions prevention in the Azure AD provisioning service (Preview)

The Azure AD provisioning service includes a feature to help avoid accidental deletions. This feature ensures that users aren't disabled or deleted in an application unexpectedly.

The feature lets you specify a deletion threshold, above which an admin 
needs to explicitly choose to allow the deletions to be processed.

> [!NOTE]
> Accidental deletions are not supported for our Workday / SuccessFactors integrations. It is also not supported for changes in scoping (e.g. changing a scoping filter or changing from "sync all users and groups" to "sync assigned users and groups". Until the accidental deletions prevention feature is fully released, you will need to access the Azure portal using this URL: https://aka.ms/AccidentalDeletionsPreview


## Configure accidental deletion prevention
To enable accidental deletion prevention:
1.  In the Azure portal, select **Azure Active Directory**.
2.  Select **Enterprise applications** and then select your app.
3.  Select **Provisioning** and then on the provisioning page select **Edit provisioning**.
4. Under **Settings**, select the **Prevent accidental deletions** checkbox and specify a deletion 
threshold. Also, be sure the notification email address is completed. If the deletion threshold his met and email will be sent.
5. Select **Save**, to save the changes.

When the deletion threshold is met, the job will go into quarantine and a notification email will be sent. The quarantined job can then be allowed or rejected. To learn more about quarantine behavior, see [Application provisioning in quarantine status](application-provisioning-quarantine-status.md).

## Known limitations
There are two key limitations to be aware of and are actively working to address:
- HR-driven provisioning from Workday and SuccessFactors do not support the accidental deletions feature. 
- Changes to your provisioning configuration (e.g. changing scoping) is not supported by the accidental deletions feature. 

## Recovering from an accidental deletion
If you encounter an accidental deletion you will see it on the provisioning status page.  It will say **Provisioning has been quarantined. See quarantine details for more information.**.

You can click either **Allow deletes** or **View provisioning logs**.

### Allowing deletions

The **Allow deletes** action will delete the objects that triggered the accidental delete threshold.  Use the following procedure to accept the deletes.  

1. Select **Allow deletes**.
2. Click **Yes** on the confirmation to allow the deletions.
3. You will see confirmation that the deletions were accepted and the status will return to healthy with the next cycle.

### Rejecting deletions

If you do not want to allow the deletions, you need to do the following:
- Investigate the source of the deletions. You can use the provisioning logs for details.
- Prevent the deletion by assigning the user / group to the app again, restoring the user / group, or updating your provisioning configuration.
- Once you've made the necessary changes to prevent the user / group from being deleted, restart provisioning. Please do not restart provisioning until you've made the necessary changes to prevent the users / groups from being deleted. 


### Test deletion prevention
You can test the feature by triggering disable / deletion events by setting the threshold to a low number, for example 3, and then changing scoping filters, un-assigning users, and deleting users from the directory (see common scenarios in next section). 

Let the provisioning job run (20 – 40 mins) and navigate back to the provisioning page. You will see the provisioning job in quarantine and can choose to allow the deletions or review the provisioning logs to understand why the deletions occurred.

## Common de-provisioning scenarios to test
- Delete a user / put them into the recycle bin.
- Block sign in for a user.
- Unassign a user or group from the application.
- Remove a user from a group that’s providing them access to the app.

To learn more about de-provisioning scenarios, see [How Application Provisioning Works](how-provisioning-works.md#de-provisioning).

## Frequently Asked Questions

### What scenarios count toward the deletion threshold?
When a user is set to be removed from the target application, it will be counted against the 
deletion threshold. Scenarios that could lead to a user being removed from the target 
application could include: unassigning the user from the application and soft / hard deleting a user in the directory. Groups 
evaluated for deletion count towards the deletion threshold. In addition to deletions, the same functionality also works for disables.

### What is the interval that the deletion threshold is evaluated on?
It is evaluated each cycle. If the number of deletions does not exceed the threshold during a 
single cycle, the “circuit breaker” won’t be triggered. If multiple cycles are needed to reach a 
steady state, the deletion threshold will be evaluated per cycle.

### How are these deletion events logged?
You can find users that should be disabled / deleted but haven’t due to the deletion threshold. 
Navigation to **Provisioning logs** and then filter **Action** with *StagedAction* or *StagedDelete*.


## Next steps 

- [How application provisioning works](how-provisioning-works.md)
- [Plan an application provisioning deployment](plan-auto-user-provisioning.md)
