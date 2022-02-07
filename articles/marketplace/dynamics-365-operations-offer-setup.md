---
title: Create a Dynamics 365 Operations Apps offer on Microsoft AppSource (Azure Marketplace)
description: Create a Dynamics 365 Operations Apps offer on Microsoft AppSource (Azure Marketplace).
ms.service: marketplace 
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 12/03/2021
---

# Create a Dynamics 365 Operations Apps offer

This article describes how to create a [Dynamics 365 Operations Apps](https://dynamics.microsoft.com/finance-and-operations) offer. This offer type is an enterprise resource planning (ERP) service that supports advanced operations, finance, manufacturing, and supply chain management. All offers for Dynamics 365 go through our certification process.

Before you start, create a commercial marketplace account in [Partner Center](./create-account.md) and ensure it is enrolled in the commercial marketplace program.

## Before you begin

Review [Plan a Dynamics 365 offer](marketplace-dynamics-365.md). It will explain the technical requirements for this offer and list the information and assets you’ll need when you create it.

## Create a new offer

1. Sign in to [Partner Center](https://go.microsoft.com/fwlink/?linkid=2166002).

1. On the Home page, select the **Marketplace offers** tile.

    [ ![Illustrates the Marketplace offers tile on the Partner Center Home page.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. On the Marketplace offers page, select **+ New offer** > **Dynamics 365 Operations Apps**.

    [ ![Screenshot of the 'New offer' button on the Marketplace offers page, highlighting the Dynamics 365 Operations Apps offer type.](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png) ](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png#lightbox)

> [!IMPORTANT]
> After an offer is published, any edits you make to it in Partner Center appear on Microsoft AppSource only after you republish the offer. Be sure to always republish an offer after changing it.

## New offer

Enter an **Offer ID**. This is a unique identifier for each offer in your account.

- This ID is visible to customers in the web address for the offer and in Azure Resource Manager templates, if applicable.
- Use only lowercase letters and numbers. The ID can include hyphens and underscores, but no spaces, and is limited to 50 characters. For example, if you enter **test-offer-1**, the offer web address will be `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- The Offer ID can't be changed after you select **Create**.

Enter an **Offer alias**. This is the name used for the offer in Partner Center.

- This name isn't used on AppSource. It is different from the offer name and other values shown to customers.
- This name can't be changed after you select **Create**.

Select **Create** to generate the offer. Partner Center opens the **Offer setup** page.

## Alias

Enter a descriptive name that we'll use to refer to this offer solely within Partner Center. The offer alias (pre-populated with what you entered when you created the offer) won't be used in the marketplace and is different than the offer name shown to customers. If you want to update the offer name later, see the [Offer listing](dynamics-365-operations-offer-listing.md) page.

## Setup details

For **How do you want potential customers to interact with this listing offer?**, select **Contact me**.

Collect customer contact information by connecting your Customer Relationship Management (CRM) system. The customer will be asked for permission to share their information. These customer details, along with the offer name, ID, and marketplace source where the customer found your offer, will be sent to the CRM system that you've configured. For more information about configuring your CRM, see [Customer leads](#customer-leads).

## Test drive

A test drive is a great way to showcase your offer to potential customers by giving them access to a preconfigured environment for a fixed number of hours. Offering a test drive results in an increased conversion rate and generates highly qualified leads. To learn more, start with [What is a test drive?](what-is-test-drive.md).

> [!TIP]
> A test drive is different from a free trial. You can offer either a test drive, free trial, or both. They both provide customers with your solution for a fixed period-of-time. But, a test drive also includes a hands-on, self-guided tour of your product’s key features and benefits being demonstrated in a real-world implementation scenario.

To enable a test drive, select the **Enable a test drive** check box and select the **Type of test drive**. You will configure the test drive later. With test drive, you must also configure your offer to a CRM system for customer leads (see next section). To remove test drive from your offer, clear this check box.

## Customer leads

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

For more information, see [Customer leads from your commercial marketplace offer](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## Business Applications ISV Program

Your offer is initially enrolled in the Standard tier.

Before publishing your offer, complete the [Co-sell module](https://aka.ms/BizAppsISVProgram).

Select **Save draft** before continuing to the next tab in the left-nav menu, **Properties**.

## Next steps

- Configure offer [Properties](dynamics-365-operations-properties.md)
- [Offer listing best practices](gtm-offer-listing-best-practices.md)
