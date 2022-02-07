---
title: How to create an Azure support request
description: Customers who need assistance can use the Azure portal to find self-service solutions and to create and manage support requests.
ms.topic: how-to
ms.custom: support-help-page
ms.date: 02/01/2022
---

# Create an Azure support request

Azure enables you to create and manage support requests, also known as support tickets. You can create and manage requests in the [Azure portal](https://portal.azure.com), which is covered in this article. You can also create and manage requests programmatically, using the [Azure support ticket REST API](/rest/api/support), or by using [Azure CLI](/cli/azure/azure-cli-support-request).

> [!NOTE]
> The Azure portal URL is specific to the Azure cloud where your organization is deployed.
>
>- Azure portal for commercial use is: [https://portal.azure.com](https://portal.azure.com)
>- Azure portal for Germany is: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>- Azure portal for the United States government is: [https://portal.azure.us](https://portal.azure.us)

Azure provides unlimited support for subscription management, which includes billing, quota adjustments, and account transfers. For technical support, you need a support plan. For more information, see [Compare support plans](https://azure.microsoft.com/support/plans).

## Getting started

You can get to **Help + support** in the Azure portal. It's available from the Azure portal menu, the global header, or the resource menu for a service. Before you can file a support request, you must have appropriate permissions.

### Azure role-based access control

To create a support request, you must have the [Owner](../../role-based-access-control/built-in-roles.md#owner), [Contributor](../../role-based-access-control/built-in-roles.md#contributor),  [Support Request Contributor](../../role-based-access-control/built-in-roles.md#support-request-contributor) role, or a custom role with [Microsoft.Support/*](/azure/role-based-access-control/resource-provider-operations#microsoftsupport), at the subscription level.

To create a support request without a subscription, for example an Azure Active Directory scenario, you must be an [Admin](../../active-directory/roles/permissions-reference.md).

> [!IMPORTANT]
> If a support request requires investigation into multiple subscriptions, you must have the required access for each subscription involved ([Owner](../../role-based-access-control/built-in-roles.md#owner), [Contributor](../../role-based-access-control/built-in-roles.md#contributor), [Reader](../../role-based-access-control/built-in-roles.md#reader), [Support Request Contributor](../../role-based-access-control/built-in-roles.md#support-request-contributor), or a custom role with the [Microsoft.Support/supportTickets/read](/azure/role-based-access-control/resource-provider-operations#microsoftsupport) permission).

### Go to Help + support from the global header

To start a support request from anywhere in the Azure portal:

1. Select the **?** in the global header, then select **Help + support**.

   :::image type="content" source="media/how-to-create-azure-support-request/helpandsupportnewlower.png" alt-text="Screenshot of the Help menu in the Azure portal.":::

1. Select **Create a support request**. Follow the prompts to provide information about your problem. We'll suggest some possible solutions, gather details about the issue, and help you submit and track the support request.

   :::image type="content" source="media/how-to-create-azure-support-request/newsupportrequest2lower.png" alt-text="Screenshot of the Help + support page with Create a support request link.":::

### Go to Help + support from a resource menu

To start a support request in the context of the resource you're currently working with:

1. From the resource menu, in the **Support + troubleshooting** section, select **New Support Request**.

   :::image type="content" source="media/how-to-create-azure-support-request/incontext2lower.png" alt-text="Screenshot of the New Support Request option in the resource pane.":::

1. Follow the prompts to provide us with information about the problem you're having. When you start the support request process from a resource, some options are pre-selected for you.

## Create a support request

We'll walk you through some steps to gather information about your problem and help you solve it. Each step is described in the following sections.

### Problem description

The first step of the support request process is to select an issue type. You'll then be prompted for more information, which can vary depending on what type of issue you selected. If you select **Technical**, you'll need to specify the service that your issue relates to. Depending on the service, you'll see additional options for **Problem type** and **Problem subtype**.

> [!IMPORTANT]
> In most cases, you'll need to specify a subscription. Choose the subscription where you are experiencing the problem. The support engineer assigned to your case will be able to access the subscription you specify here. You can tell them about additional subscriptions in your description (or by [sending a message](how-to-manage-azure-support-request.md#send-a-message) later), but the support engineer will only be able to work on [subscriptions to which you have access](#azure-role-based-access-control).

:::image type="content" source="media/how-to-create-azure-support-request/basics2lower.png" alt-text="Screenshot of the Problem description step of the support request process.":::

Once you've provided all of these details, select **Next**.

### Recommended solution

Based on the information you provided, we'll show you recommended solutions you can use to try and resolve the problem. In some cases, we may even run a quick diagnostic. Solutions are written by Azure engineers and will solve most common problems.

If you're still unable to resolve the issue, continue creating your support request by selecting **Next**.

### Additional details

Next, we collect additional details about the problem. Providing thorough and detailed information in this step helps us route your support request to the right engineer.

1. Complete the **problem details** so that we have more information about your issue. If possible, tell us when the problem started and any steps to reproduce it. You can upload a file, such as a log file or output from diagnostics. For more information on file uploads, see [File upload guidelines](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. In the **Advanced diagnostic information** section, select **Yes** or **No**. Selecting **Yes** allows Azure support to gather [advanced diagnostic information](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) from your Azure resources. If you prefer not to share this information, select **No**. See the [Advanced diagnostic information logs](#advanced-diagnostic-information-logs) section for more details about the types of files we might collect.

  In some cases, there will be additional options to choose from. For example, for certain types of Virtual Machine problem types, you can choose whether to [allow access to a virtual machine's memory](#memory-dump-collection).

1. In the **Support method** section, select the severity of impact. The maximum severity level depends on your [support plan](https://azure.microsoft.com/support/plans).

1. Provide your preferred contact method, your availability, and your preferred support language.

1. Next, complete the **Contact info** section so we know how to contact you.

Select **Next** when you've completed all of the necessary information.

### Review + create

Before you create your request, review all of the details that you'll send to support. You can select **Previous** to return to any tab if you need to make changes. When you're satisfied the support request is complete, select **Create**.

A support engineer will contact you using the method you indicated. For information about initial response times, see [Support scope and responsiveness](https://azure.microsoft.com/support/plans/response/).

### Advanced diagnostic information logs

When you allow collection of [advanced diagnostic information](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/), Microsoft support can collect information that can help solve your problem more quickly. This non-exhaustive list includes examples of the most common files collected under advanced diagnostic information for different services or environments.

- [Microsoft Azure PaaS VM logs](/troubleshoot/azure/virtual-machines/sdp352ef8720-e3ee-4a12-a37e-cc3b0870f359-windows-vm)
- [Microsoft Azure IaaS VM logs](https://github.com/azure/azure-diskinspect-service/blob/master/docs/manifest_by_file.md)
- [Microsoft Azure Service Fabric logs](/troubleshoot/azure/general/fabric-logs)
- [StorSimple support packages and device logs](https://support.microsoft.com/topic/storsimple-support-packages-and-device-logs-cb0a1c7e-6125-a5a7-f212-51439781f646)
- [SQL Server on Azure Virtual Machines logs](/troubleshoot/azure/general/sql-vm-logs)
- [Azure Active Directory logs](/troubleshoot/azure/active-directory/support-data-collection-diagnostic-logs)
- [Azure Stack Edge support package and device logs](/troubleshoot/azure/general/azure-stack-edge-support-package-device-logs)
- [Azure Synapse Analytics logs](/troubleshoot/azure/general/synapse-analytics-apache-spark-pools-diagnostic-logs)

### Memory dump collection

When you create a support case for certain Virtual Machine (VM) problem types, you will be asked whether you'll allow support to access your virtual machine's memory. If you do so, we may collect a memory dump to help diagnose the problem.

A complete memory dump is the largest kernel-mode dump file. This file includes all of the physical memory that is used by Windows. A complete memory dump does not, by default, include physical memory that is used by the platform firmware.

The dump is copied from the compute node (Azure host) to another server for debugging within the same datacenter. Customer data is protected, since the data does not leave Azure's secure boundary.

The dump file is created by generating a Hyper-V save state of the VM. This will pause the VM for up to 10 minutes, after which time the VM is resumed. The VM is not restarted as part of this process.

## Next steps

To learn more about self-help support options in Azure, watch this video:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Follow these links to learn more:

* [How to manage an Azure support request](how-to-manage-azure-support-request.md)
* [Azure support ticket REST API](/rest/api/support)
* Engage with us on [Twitter](https://twitter.com/azuresupport)
* Get help from your peers in the [Microsoft Q&A question page](/answers/products/azure)
* Learn more in [Azure Support FAQ](https://azure.microsoft.com/support/faq)
