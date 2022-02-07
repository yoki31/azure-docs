---
title: The regulatory compliance dashboard in Microsoft Defender for Cloud
description: Learn how to add and remove regulatory standards from the regulatory compliance dashboard in Defender for Cloud
ms.topic: how-to
ms.date: 11/09/2021
---
# Customize the set of standards in your regulatory compliance dashboard

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud continually compares the configuration of your resources with requirements in industry standards, regulations, and benchmarks. The **regulatory compliance dashboard** provides insights into your compliance posture based on how you're meeting specific compliance requirements.

> [!TIP]
> Learn more about Defender for Cloud's regulatory compliance dashboard in the [frequently asked questions](regulatory-compliance-dashboard.md#faq---regulatory-compliance-dashboard).

## How are regulatory compliance standards represented in Defender for Cloud?

Industry standards, regulatory standards, and benchmarks are represented in Defender for Cloud's regulatory compliance dashboard. Each standard is an initiative defined in Azure Policy.

To see compliance data mapped as assessments in your dashboard, add a compliance standard to your management group or subscription from within the **Security policy** page. To learn more about Azure Policy and initiatives, see [Working with security policies](tutorial-security-policy.md).

When you've assigned a standard or benchmark to your selected scope, the standard appears in your regulatory compliance dashboard with all associated compliance data mapped as assessments. You can also download summary reports for any of the standards that have been assigned.

Microsoft tracks the regulatory standards themselves and automatically improves its coverage in some of the packages over time. When Microsoft releases new content for the initiative, it will appear automatically in your dashboard as new policies mapped to controls in the standard.


## What regulatory compliance standards are available in Defender for Cloud?

By default, every subscription has the **Azure Security Benchmark** assigned. This is the Microsoft-authored, Azure-specific guidelines for security and compliance best practices based on common compliance frameworks. [Learn more about Azure Security Benchmark](/security/benchmark/azure/introduction).

Available regulatory standards:

- PCI-DSS v3.2.1:2018
- SOC TSP
- NIST SP 800-53 R4
- NIST SP 800 171 R2
- UK OFFICIAL and UK NHS
- Canada Federal PBMM
- Azure CIS 1.1.0
- HIPAA/HITRUST
- SWIFT CSP CSCF v2020
- ISO 27001:2013
- New Zealand ISM Restricted
- CMMC Level 3
- Azure CIS 1.3.0
- NIST SP 800-53 R5
- FedRAMP H
- FedRAMP M

> [!TIP]
> Standards are added to the dashboard as they become available. The preceding list might not contain recently added standards.

## Add a regulatory standard to your dashboard

The following steps explain how to add a package to monitor your compliance with one of the supported regulatory standards.

### Prerequisites
To add standards to your dashboard:

- The subscription must have Defender for Cloud's enhanced security features enabled
- The user must have owner or policy contributor permissions

### Add a standard

1. From Defender for Cloud's menu, select **Regulatory compliance** to open the regulatory compliance dashboard. Here you can see the compliance standards currently assigned to the currently selected subscriptions.   

1. From the top of the page, select **Manage compliance policies**. The Policy Management page appears.

1. Select the subscription or management group for which you want to manage the regulatory compliance posture. 

    > [!TIP]
    > We recommend selecting the highest scope for which the standard is applicable so that compliance data is aggregated and tracked for all nested resources. 

1. To add the standards relevant to your organization, expand the **Industry & regulatory standards** section and select **Add more standards**.

1. From the **Add regulatory compliance standards** page, you can search for any of the available standards:
    
    ![Adding regulatory standards to Microsoft Defender for Cloud's regulatory compliance dashboard.](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Select **Add** and enter all the necessary details for the specific initiative such as scope, parameters, and remediation.

1. From Defender for Cloud's menu, select **Regulatory compliance** again to go back to the regulatory compliance dashboard.

    Your new standard appears in your list of Industry & regulatory standards. 

    > [!NOTE]
    > It may take a few hours for a newly added standard to appear in the compliance dashboard.

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-dashboard.png" alt-text="Regulatory compliance dashboard." lightbox="./media/regulatory-compliance-dashboard/compliance-dashboard.png":::

## Remove a standard from your dashboard

You can continue to customize the regulatory compliance dashboard, to focus only on the standards that are applicable to you, by removing any of the supplied regulatory standards that aren't relevant to your organization.

To remove a standard:

1. From Defender for Cloud's menu, select **Security policy**.

1. Select the relevant subscription from which you want to remove a standard.

    > [!NOTE]
    > You can remove a standard from a subscription, but not from a management group. 

    The security policy page opens. For the selected subscription, it shows the default policy, the industry and regulatory standards, and any custom initiatives you've created.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Remove a regulatory standard from your regulatory compliance dashboard in Microsoft Defender for Cloud.":::

1. For the standard you want to remove, select **Disable**. A confirmation window appears.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Confirm that you really want to remove the regulatory standard you selected.":::

1. Select **Yes**. The standard will be removed. 


## Next steps

In this article, you learned how to **add compliance standards** to monitor your compliance with regulatory and industry standards.

For related material, see the following pages:

- [Azure Security Benchmark](/security/benchmark/azure/introduction)
- [Defender for Cloud regulatory compliance dashboard](regulatory-compliance-dashboard.md) - Learn how to track and export your regulatory compliance data with Defender for Cloud and external tools
- [Working with security policies](tutorial-security-policy.md)
