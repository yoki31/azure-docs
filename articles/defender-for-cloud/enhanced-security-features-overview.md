---
title: Understand the enhanced security features of Microsoft Defender for Cloud 
description: Learn about the benefits of enabling enhanced security in Microsoft Defender for Cloud
ms.topic: overview
ms.date: 11/14/2021
---

# Microsoft Defender for Cloud's enhanced security features

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

The enhanced security features are free for the first 30 days. At the end of 30 days, if you decide to continue using the service, we'll automatically start charging for usage.

You can upgrade from the **Environment settings** page, as described in [Quickstart: Enable enhanced security features](enable-enhanced-security.md). For pricing details in your local currency or region, see the [pricing page](https://azure.microsoft.com/pricing/details/defender-for-cloud/).

:::image type="content" source="media/enhanced-security-features-overview/defender-plans-top.png" alt-text="Enabling Microsoft Defender for Cloud's enhanced security features.":::

## What are the benefits of enabling enhanced security features?

Defender for Cloud is offered in two modes:

- **Without enhanced security features** (Free) - Defender for Cloud is enabled for free on all your Azure subscriptions when you visit the workload protection dashboard in the Azure portal for the first time, or if enabled programmatically via API. Using this free mode provides the secure score and its related features: security policy, continuous security assessment, and actionable security recommendations to help you protect your Azure resources.

- **Defender for Cloud with all enhanced security features** - Enabling enhanced security extends the capabilities of the free mode to workloads running in private and other public clouds, providing unified security management and threat protection across your hybrid cloud workloads. Some of the major benefits include:

    - **Microsoft Defender for Endpoint** - Microsoft Defender for servers includes [Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) for comprehensive endpoint detection and response (EDR). Learn more about the benefits of using Microsoft Defender for Endpoint together with Defender for Cloud in [Use Defender for Cloud's integrated EDR solution](integration-defender-for-endpoint.md).
    - **Vulnerability assessment for virtual machines, container registries, and SQL resources** - Easily enable vulnerability assessment solutions to discover, manage, and resolve vulnerabilities. View, investigate, and remediate the findings directly from within Defender for Cloud.
    - **Multi-cloud security** - Connect your accounts from Amazon Web Services (AWS) and Google Cloud Platform (GCP) to protect resources and workloads on those platforms with a range of Microsoft Defender for Cloud security features.
    - **Hybrid security** – Get a unified view of security across all of your on-premises and cloud workloads. Apply security policies and continuously assess the security of your hybrid cloud workloads to ensure compliance with security standards. Collect, search, and analyze security data from multiple sources, including firewalls and other partner solutions.
    - **Threat protection alerts** - Advanced behavioral analytics and the Microsoft Intelligent Security Graph provide an edge over evolving cyber-attacks. Built-in behavioral analytics and machine learning can identify attacks and zero-day exploits. Monitor networks, machines, data stores (SQL servers hosted inside and outside Azure, Azure SQL databases, Azure SQL Managed Instance, and Azure Storage) and cloud services for incoming attacks and post-breach activity. Streamline investigation with interactive tools and contextual threat intelligence.
    - **Track compliance with a range of standards** - Defender for Cloud continuously assesses your hybrid cloud environment to analyze the risk factors according to the controls and best practices in [Azure Security Benchmark](/security/benchmark/azure/introduction). When you enable the enhanced security features, you can apply a range of other industry standards, regulatory standards, and benchmarks according to your organization's needs. Add standards and track your compliance with them from the [regulatory compliance dashboard](update-regulatory-compliance-packages.md).
    - **Access and application controls** - Block malware and other unwanted applications by applying machine learning powered recommendations adapted to your specific workloads to create allow and blocklists. Reduce the network attack surface with just-in-time, controlled access to management ports on Azure VMs. Access and application controls drastically reduce exposure to brute force and other network attacks.
    - **Container security features** - Benefit from vulnerability management and real-time threat protection on your containerized environments. Charges are based on the number of unique container images pushed to your connected registry. After an image has been scanned once, you won't be charged for it again unless it's modified and pushed once more.
    - **Breadth threat protection for resources connected to Azure** - Cloud-native threat protection for the Azure services common to all of your resources: Azure Resource Manager, Azure DNS, Azure network layer, and Azure Key Vault. Defender for Cloud has unique visibility into the Azure management layer and the Azure DNS layer, and can therefore protect cloud resources that are connected to those layers.


## FAQ - Pricing and billing 

- [How can I track who in my organization enabled a Microsoft Defender plan in Defender for Cloud?](#how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud)
- [What are the plans offered by Defender for Cloud?](#what-are-the-plans-offered-by-defender-for-cloud)
- [How do I enable Defender for Cloud's enhanced security for my subscription?](#how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription)
- [Can I enable Microsoft Defender for servers on a subset of servers in my subscription?](#can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [If I already have a license for Microsoft Defender for Endpoint can I get a discount for Defender for servers?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers)
- [My subscription has Microsoft Defender for servers enabled, do I pay for not-running servers?](#my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Will I be charged for machines without the Log Analytics agent installed?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [If a Log Analytics agent reports to multiple workspaces, will I be charged twice?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [If a Log Analytics agent reports to multiple workspaces, is the 500-MB free data ingestion available on all of them?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Is the 500-MB free data ingestion calculated for an entire workspace or strictly per machine?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [What data types are included in the 500-MB data daily allowance?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### How can I track who in my organization enabled a Microsoft Defender plan in Defender for Cloud?
Azure Subscriptions may have multiple administrators with permissions to change the pricing settings. To find out which user made a change, use the Azure Activity Log.

:::image type="content" source="media/enhanced-security-features-overview/logged-change-to-pricing.png" alt-text="Azure Activity log showing a pricing change event.":::

If the user's info isn't listed in the **Event initiated by** column, explore the event's JSON for the relevant details.

:::image type="content" source="media/enhanced-security-features-overview/tracking-pricing-changes-in-activity-log.png" alt-text="Azure Activity log JSON explorer.":::


### What are the plans offered by Defender for Cloud? 
The free offering from Microsoft Defender for Cloud offers the secure score and related tools. Enabling enhanced security turns on all of the Microsoft Defender plans to provide a range of security benefits for all your resources in Azure, hybrid, and multi-cloud environments.  

### How do I enable Defender for Cloud's enhanced security for my subscription? 
You can use any of the following ways to enable enhanced security for your subscription: 

| Method                                          | Instructions                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Defender for Cloud pages of the Azure portal    | [Enable enhanced protections](enable-enhanced-security.md)                                                                                         |
| REST API                                        | [Pricings API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Bundle Pricings](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### Can I enable Microsoft Defender for servers on a subset of servers in my subscription?
No. When you enable [Microsoft Defender for servers](defender-for-servers-introduction.md) on a subscription, all the machines in the subscription will be protected by Defender for servers.

An alternative is to enable Microsoft Defender for servers at the Log Analytics workspace level. If you do this, only servers reporting to that workspace will be protected and billed. However, several capabilities will be unavailable. These include just-in-time VM access, network detections, regulatory compliance, adaptive network hardening, adaptive application control, and more. 

### If I already have a license for Microsoft Defender for Endpoint can I get a discount for Defender for servers?
If you've already got a license for **Microsoft Defender for Endpoint for Servers**, you won't have to pay for that part of your Microsoft Defender for servers license. Learn more about [this license](/microsoft-365/security/defender-endpoint/minimum-requirements#licensing-requirements).

To request your discount, [contact Defender for Cloud's support team](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). You'll need to provide the relevant workspace ID, region, and number of Microsoft Defender for Endpoint for servers licenses applied for machines in the given workspace.

The discount will be effective starting from the approval date, and won't take place retroactively.
### My subscription has Microsoft Defender for servers enabled, do I pay for not-running servers? 
No. When you enable [Microsoft Defender for servers](defender-for-servers-introduction.md) on a subscription, you won't be charged for any machines that are in the deallocated power state while they're in that state. Machines are billed according to their power state as shown in the following table:

| State        | Description                                                                                                                                      | Instance usage billed |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Starting     | VM is starting up.                                                                                                                               | Not billed            |
| Running      | Normal working state for a VM                                                                                                                    | Billed                |
| Stopping     | This is a transitional state. When completed, it will show as Stopped.                                                                           | Billed                |
| Stopped      | The VM has been shut down from within the guest OS or using the PowerOff APIs. Hardware is still allocated to the VM and it remains on the host. | Billed                |
| Deallocating | Transitional state. When completed, the VM will show as Deallocated.                                                                             | Not billed            |
| Deallocated  | The VM has been stopped successfully and removed from the host.                                                                                  | Not billed            |

:::image type="content" source="media/enhanced-security-features-overview/deallocated-virtual-machines.png" alt-text="Azure Virtual Machines showing a deallocated machine.":::

### Will I be charged for machines without the Log Analytics agent installed?
Yes. When you enable [Microsoft Defender for servers](defender-for-servers-introduction.md) on a subscription, the machines in that subscription get a range of protections even if you haven't installed the Log Analytics agent. This is applicable for Azure virtual machines, Azure virtual machine scale sets instances, and Azure Arc-enabled servers.

### If a Log Analytics agent reports to multiple workspaces, will I be charged twice? 
Yes. If you've configured your Log Analytics agent to send data to two or more different Log Analytics workspaces (multi-homing), you'll be charged for every workspace that has a 'Security' or 'AntiMalware' solution installed. 

### If a Log Analytics agent reports to multiple workspaces, is the 500-MB free data ingestion available on all of them?
Yes. If you've configured your Log Analytics agent to send data to two or more different Log Analytics workspaces (multi-homing), you'll get 500-MB free data ingestion. It's calculated per node, per reported workspace, per day, and available for every workspace that has a 'Security' or 'AntiMalware' solution installed. You'll be charged for any data ingested over the 500-MB limit.

### Is the 500-MB free data ingestion calculated for an entire workspace or strictly per machine?
You'll get 500-MB free data ingestion per day, for every Windows machine connected to the workspace. Specifically for security data types directly collected by Defender for Cloud. 

This data is a daily rate averaged across all nodes. So even if some machines send 100-MB and others send 800-MB, if the total doesn't exceed the **[number of machines] x 500-MB** free limit, you won't be charged extra.

### What data types are included in the 500-MB data daily allowance?
Defender for Cloud's billing is closely tied to the billing for Log Analytics. [Microsoft Defender for servers](defender-for-servers-introduction.md) provides a 500 MB/node/day allocation for Windows machines against the following subset of [security data types](/azure/azure-monitor/reference/tables/tables-category#security):
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- SysmonEvent
- ProtectionStatus
- Update and UpdateSummary data types when the Update Management solution is not running on the workspace or solution targeting is enabled

If the workspace is in the legacy Per Node pricing tier, the Defender for Cloud and Log Analytics allocations are combined and applied jointly to all billable ingested data.

## Next steps
This article explained Defender for Cloud's pricing options. For related material, see:

- [How to optimize your Azure workload costs](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Pricing details according to currency or region](https://azure.microsoft.com/pricing/details/defender-for-cloud/)
- You may want to manage your costs and limit the amount of data collected for a solution by limiting it to a particular set of agents. Use [solution targeting](../azure-monitor/insights/solution-targeting.md) to apply a scope to the solution and target a subset of computers in the workspace. If you're using solution targeting, Defender for Cloud lists the workspace as not having a solution.
