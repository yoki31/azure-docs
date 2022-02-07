---
title: Enable B2B external collaboration settings - Azure AD
description: Learn how to enable Active Directory B2B external collaboration and manage who can invite guest users. Use the Guest Inviter role to delegate invitations.

services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 01/31/2022

ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal

ms.collection: M365-identity-device-management
---

# Configure external collaboration settings

External collaboration settings let you specify what roles in your organization can invite external users for B2B collaboration. These settings also include options for [allowing or blocking specific domains](allow-deny-list.md), and options for restricting what external guest users can see in your Azure AD directory. The following options are available:

- **Determine guest user access**: Azure AD allows you to restrict what external guest users can see in your Azure AD directory. For example, you can limit guest users' view of group memberships, or allow guests to view only their own profile information.

- **Specify who can invite guests**: By default, all users in your organization, including B2B collaboration guest users, can invite external users to B2B collaboration. If you want to limit the ability to send invitations, you can turn invitations on or off for everyone, or limit invitations to certain roles.

- **Enable guest self-service sign-up via user flows**: For applications you build, you can create user flows that allow a user to sign up for an app and create a new guest account. You can enable the feature in your external collaboration settings, and then [add a self-service sign-up user flow to your app](self-service-sign-up-user-flow.md).

- **Allow or block domains**: You can use collaboration restrictions to allow or deny invitations to the domains you specify. For details, see [Allow or block domains](allow-deny-list.md).

For B2B collaboration with other Azure AD organizations, you should also review your [cross-tenant access settings](cross-tenant-access-settings-b2b-collaboration.md) to ensure your inbound and outbound B2B collaboration and scope access to specific users, groups, and applications.

### To configure external collaboration settings:

1. Sign in to the [Azure portal](https://portal.azure.com) using a Global administrator or Security administrator account and open the **Azure Active Directory** service.
1. Select **External Identities** > **External collaboration settings**.

1. Under **Guest user access**, choose the level of access you want guest users to have:
  
   - **Guest users have the same access as members (most inclusive)**: This option gives guests the same access to Azure AD resources and directory data as member users.

   - **Guest users have limited access to properties and memberships of directory objects**: (Default) This setting blocks guests from certain directory tasks, like enumerating users, groups, or other directory resources. Guests can see membership of all non-hidden groups.

   - **Guest user access is restricted to properties and memberships of their own directory objects (most restrictive)**: With this setting, guests can access only their own profiles. Guests are not allowed to see other users' profiles, groups, or group memberships.

1. Under **Guest invite settings**, choose the appropriate settings:

    ![Guest invite settings](./media/external-collaboration-settings-configure/guest-invite-settings.png)

   - **Anyone in the organization can invite guest users including guests and non-admins (most inclusive)**: To allow guests in the organization to invite other guests including those who are not members of an organization, select this radio button.
   - **Member users and users assigned to specific admin roles can invite guest users including guests with member permissions**: To allow member users and users who have specific administrator roles to invite guests, select this radio button.
   - **Only users assigned to specific admin roles can invite guest users**: To allow only those users with administrator roles to invite guests, select this radio button. The administrator roles include [Global Administrator](../roles/permissions-reference.md#global-administrator), [User Administrator](../roles/permissions-reference.md#user-administrator), and [Guest Inviter](../roles/permissions-reference.md#guest-inviter).
   - **No one in the organization can invite guest users including admins (most restrictive)**: To deny everyone in the organization from inviting guests, select this radio button.
     > [!NOTE]
     > If **Members can invite** is set to **No** and **Admins and users in the guest inviter role can invite** is set to **Yes**, users in the **Guest Inviter** role will still be able to invite guests.

1. Under **Enable guest self-service sign up via user flows**, select **Yes** if you want to be able to create user flows that let users sign up for apps. For more information about this setting, see [Add a self-service sign-up user flow to an app](self-service-sign-up-user-flow.md).

    ![Self-service sign up via user flows setting](./media/external-collaboration-settings-configure/self-service-sign-up-setting.png)

1. Under **Collaboration restrictions**, you can choose whether to allow or deny invitations to the domains you specify and enter specific domain names in the text boxes. For multiple domains, enter each domain on a new line. For more information, see [Allow or block invitations to B2B users from specific organizations](allow-deny-list.md).

    ![Collaboration restrictions settings](./media/external-collaboration-settings-configure/collaboration-restrictions.png)
## Assign the Guest Inviter role to a user

With the Guest Inviter role, you can give individual users the ability to invite guests without assigning them a global administrator or other admin role. Assign the Guest inviter role to individuals. Then make sure you set **Admins and users in the guest inviter role can invite** to **Yes**.

Here's an example that shows how to use PowerShell to add a user to the Guest Inviter role:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## Next steps

See the following articles on Azure AD B2B collaboration:

- [What is Azure AD B2B collaboration?](what-is-b2b.md)
- [Add B2B collaboration guest users without an invitation](add-user-without-invite.md)
- [Adding a B2B collaboration user to a role](./add-users-administrator.md)