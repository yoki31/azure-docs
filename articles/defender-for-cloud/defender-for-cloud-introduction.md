---
title: Microsoft Defender for Cloud - an introduction
description: Use Microsoft Defender for Cloud to protect your Azure, hybrid, and multi-cloud resources and workloads.
ms.topic: overview
ms.custom: mvc
ms.date: 12/12/2021
---
# What is Microsoft Defender for Cloud?

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud is a tool for security posture management and threat protection. It strengthens the security posture of your cloud resources, and with its integrated Microsoft Defender plans, Defender for Cloud protects workloads running in Azure, hybrid, and other cloud platforms.

Defender for Cloud provides the tools needed to harden your resources, track your security posture, protect against cyber attacks, and streamline security management. Because it's natively integrated, deployment of Defender for Cloud is easy, providing you with simple auto provisioning to secure your resources by default.

Defender for Cloud fills three vital needs as you manage the security of your resources and workloads in the cloud and on-premises:

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-synopsis.png" alt-text="Understanding the core functionality of Microsoft Defender for Cloud.":::

|Security requirement  | Defender for Cloud solution|
|---------|---------|
|**Continuous assessment** - Understand your current security posture.   | **Secure score** - A single score so that you can tell, at a glance, your current security situation: the higher the score, the lower the identified risk level.       |
|**Secure** - Harden all connected resources and services. | **Security recommendations** - Customized and prioritized hardening tasks to improve your posture. You implement a recommendation by following the detailed remediation steps provided in the recommendation. For many recommendations, Defender for Cloud offers a "Fix" button for automated implementation!|
|**Defend** - Detect and resolve threats to those resources and services. | **Security alerts** - With the enhanced security features enabled, Defender for Cloud detects threats to your resources and workloads. These alerts appear in the Azure portal and Defender for Cloud can also send them by email to the relevant personnel in your organization. Alerts can also be streamed to SIEM, SOAR, or IT Service Management solutions as required. |

## Posture management and workload protection

Microsoft Defender for Cloud's features cover the two broad pillars of cloud security: cloud security posture management and cloud workload protection.

### Cloud security posture management (CSPM)

In Defender for Cloud, the posture management features provide:

- **Visibility** - to help you understand your current security situation
- **Hardening guidance** - to help you efficiently and effectively improve your security

The central feature in Defender for Cloud that enables you to achieve those goals is **secure score**. Defender for Cloud continually assesses your resources, subscriptions, and organization for security issues. It then aggregates all the findings into a single score so that you can tell, at a glance, your current security situation: the higher the score, the lower the identified risk level.

When you open Defender for Cloud for the first time, it will meet the visibility and strengthening goals as follows:

1. **Generate a secure score** for your subscriptions based on an assessment of your connected resources compared with the guidance in [Azure Security Benchmark](/security/benchmark/azure/overview). Use the score to understand your security posture, and the compliance dashboard to review your compliance with the built-in benchmark. When you've enabled the enhanced security features, you can customize the standards used to assess your compliance, and add other regulations (such as NIST and Azure CIS) or organization-specific security requirements.

1. **Provide hardening recommendations** based on any identified security misconfigurations and weaknesses. Use these security recommendations to strengthen the security posture of your organization's Azure, hybrid, and multi-cloud resources.

[Learn more about secure score](secure-score-security-controls.md).

### Cloud workload protection (CWP)

Defender for Cloud offers security alerts that are powered by [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). It also includes a range of advanced, intelligent, protections for your workloads. The workload protections are provided through Microsoft Defender plans specific to the types of resources in your subscriptions. For example, you can enable **Microsoft Defender for Storage** to get alerted about suspicious activities related to your Azure Storage accounts.

## Azure, hybrid, and multi-cloud protections

Because Defender for Cloud is an Azure-native service, many Azure services are monitored and protected without needing any deployment.

When necessary, Defender for Cloud can automatically deploy a Log Analytics agent to gather security-related data. For Azure machines, deployment is handled directly. For hybrid and multi-cloud environments, Microsoft Defender plans are extended to non Azure machines with the help of [Azure Arc](https://azure.microsoft.com/services/azure-arc/). CSPM features are extended to multi-cloud machines without the need for any agents (see [Defend resources running on other clouds](#defend-resources-running-on-other-clouds)).

### Azure-native protections

Defender for Cloud helps you detect threats across:

- **Azure PaaS services** - Detect threats targeting Azure services including Azure App Service, Azure SQL, Azure Storage Account, and more data services. You can also perform anomaly detection on your Azure activity logs using the native integration with Microsoft Defender for Cloud Apps (formerly known as Microsoft Cloud App Security).

- **Azure data services** - Defender for Cloud includes capabilities that help you automatically classify your data in Azure SQL. You can also get assessments for potential vulnerabilities across Azure SQL and Storage services, and recommendations for how to mitigate them.

- **Networks** - Defender for Cloud helps you limit exposure to brute force attacks. By reducing access to virtual machine ports, using the just-in-time VM access, you can harden your network by preventing unnecessary access. You can set secure access policies on selected ports, for only authorized users, allowed source IP address ranges or IP addresses, and for a limited amount of time.

### Defend your hybrid resources

In addition to defending your Azure environment, you can add Defender for Cloud capabilities to your hybrid cloud environment to protect your non-Azure servers. To help you focus on what matters the most​, you'll get customized threat intelligence and prioritized alerts according to your specific environment.

To extend protection to on-premises machines, deploy [Azure Arc](https://azure.microsoft.com/services/azure-arc/) and enable Defender for Cloud's enhanced security features. Learn more in [Add non-Azure machines with Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

### Defend resources running on other clouds

Defender for Cloud can protect resources in other clouds (such as AWS and GCP).

For example, if you've [connected an Amazon Web Services (AWS) account](quickstart-onboard-aws.md) to an Azure subscription, you can enable any of these protections:

- **Defender for Cloud's CSPM features** extend to your AWS resources. This agentless plan assesses your AWS resources according to AWS-specific security recommendations and these are included in your secure score. The resources will also be assessed for compliance with built-in standards specific to AWS (AWS CIS, AWS PCI DSS, and AWS Foundational Security Best Practices). Defender for Cloud's [asset inventory page](asset-inventory.md) is a multi-cloud enabled feature helping you manage your AWS resources alongside your Azure resources.
- **Microsoft Defender for Kubernetes** extends its container threat detection and advanced defenses to your **Amazon EKS Linux clusters**.
- **Microsoft Defender for servers** brings threat detection and advanced defenses to your Windows and Linux EC2 instances. This plan includes the integrated license for Microsoft Defender for Endpoint, security baselines and OS level assessments, vulnerability assessment scanning, adaptive application controls (AAC), file integrity monitoring (FIM), and more.

Learn more about connecting your [AWS](quickstart-onboard-aws.md) and [GCP](quickstart-onboard-gcp.md) accounts to Microsoft Defender for Cloud.

## Vulnerability assessment and management

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-assess.png" alt-text="Focus on the assessment features of Microsoft Defender for Cloud.":::

Defender for Cloud includes vulnerability assessment solutions for your virtual machines, container registries, and SQL servers as part of the enhanced security features. Some of the scanners are powered by Qualys. But you don't need a Qualys license, or even a Qualys account - everything's handled seamlessly inside Defender for Cloud.

Microsoft Defender for servers includes automatic, native integration with Microsoft Defender for Endpoint. Learn more, [Protect your endpoints with Defender for Cloud's integrated EDR solution: Microsoft Defender for Endpoint](integration-defender-for-endpoint.md). With this integration enabled, you'll have access to the vulnerability findings from **Microsoft threat and vulnerability management**. Learn more in [Investigate weaknesses with Microsoft Defender for Endpoint's threat and vulnerability management](deploy-vulnerability-assessment-tvm.md).

Review the findings from these vulnerability scanners and respond to them all from within Defender for Cloud. This broad approach brings Defender for Cloud closer to being the single pane of glass for all of your cloud security efforts.

Learn more on the following pages:

- [Defender for Cloud's integrated Qualys scanner for Azure and hybrid machines](deploy-vulnerability-assessment-vm.md)
- [Identify vulnerabilities in images in Azure container registries](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)

## Optimize and improve security by configuring recommended controls

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-secure.png" alt-text="Focus on the 'secure' features of Microsoft Defender for Cloud.":::

It's a security basic to know and make sure your workloads are secure, and it starts with having tailored security policies in place. Because policies in Defender for Cloud are built on top of Azure Policy controls, you're getting the full range and flexibility of a **world-class policy solution**. In Defender for Cloud, you can set your policies to run on management groups, across subscriptions, and even for a whole tenant.

Defender for Cloud continuously discovers new resources that are being deployed across your workloads and assesses whether they are configured according to security best practices. If not, they're flagged and you get a prioritized list of recommendations for what you need to fix. Recommendations help you reduce the attack surface across each of your resources.

The list of recommendations is enabled and supported by the Azure Security Benchmark. This Microsoft-authored, Azure-specific, benchmark provides a set of guidelines for security and compliance best practices based on common compliance frameworks. Learn more in [Introduction to Azure Security Benchmark](/security/benchmark/azure/introduction).

In this way, Defender for Cloud enables you not just to set security policies, but to *apply secure configuration standards across your resources*.

:::image type="content" source="./media/defender-for-cloud-introduction/recommendation-example.png" alt-text="Defender for Cloud recommendation example.":::

To help you understand how important each recommendation is to your overall security posture, Defender for Cloud groups the recommendations into security controls and adds a **secure score** value to each control. This is crucial in enabling you to **prioritize your security work**.

:::image type="content" source="./media/defender-for-cloud-introduction/sc-secure-score.png" alt-text="Defender for Cloud secure score.":::

## Defend against threats

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-defend.png" alt-text="Focus on the 'defend'' features of Microsoft Defender for Cloud.":::

Defender for Cloud provides:

- **Security alerts** - When Defender for Cloud detects a threat in any area of your environment, it generates a security alert. These alerts describe details of the affected resources, suggested remediation steps, and in some cases an option to trigger a logic app in response. Whether an alert is generated by Defender for Cloud, or received by Defender for Cloud from an integrated  security product, you can export it. To export your alerts to Microsoft Sentinel, any third-party SIEM, or any other external tool, follow the instructions in [Stream alerts to a SIEM, SOAR, or IT Service Management solution](export-to-siem.md). Defender for Cloud's threat protection includes fusion kill-chain analysis, which automatically correlates alerts in your environment based on cyber kill-chain analysis, to help you better understand the full story of an attack campaign, where it started and what kind of impact it had on your resources. [Defender for Cloud's supported kill chain intents are based on version 7 of the MITRE ATT&CK matrix](alerts-reference.md#intentions).

- **Advanced threat protection features** for virtual machines, SQL databases, containers, web applications, your network, and more - Protections include securing the management ports of your VMs with [just-in-time access](just-in-time-access-overview.md), and [adaptive application controls](adaptive-application-controls.md) to create allowlists for what apps should and shouldn't run on your machines.

The **Defender plans** page of Microsoft Defender for Cloud offers the following plans for comprehensive defenses for the compute, data, and service layers of your environment:

- [Microsoft Defender for servers](defender-for-servers-introduction.md)
- [Microsoft Defender for Storage](defender-for-storage-introduction.md)
- [Microsoft Defender for SQL](defender-for-sql-introduction.md)
- [Microsoft Defender for Containers](defender-for-containers-introduction.md)
- [Microsoft Defender for App Service](defender-for-app-service-introduction.md)
- [Microsoft Defender for Key Vault](defender-for-key-vault-introduction.md)
- [Microsoft Defender for Resource Manager](defender-for-resource-manager-introduction.md)
- [Microsoft Defender for DNS](defender-for-dns-introduction.md)
- [Microsoft Defender for open-source relational databases](defender-for-databases-introduction.md)

Use the advanced protection tiles in the [workload protections dashboard](workload-protections-dashboard.md) to monitor and configure each of these protections.

> [!TIP]
> Microsoft Defender for IoT is a separate product. You'll find all the details in [Introducing Microsoft Defender for IoT](../defender-for-iot/overview.md).

## Next steps

- To get started with Defender for Cloud, you need a subscription to Microsoft Azure. If you don't have a subscription, [sign up for a free trial](https://azure.microsoft.com/free/).

- Defender for Cloud's free plan is enabled on all your current Azure subscriptions when you visit the Defender for Cloud pages in the Azure portal for the first time, or if enabled programmatically via the REST API. To take advantage of advanced security management and threat detection capabilities, you must enable the enhanced security features. These features are free for the first 30 days. [Learn more about the pricing](https://azure.microsoft.com/pricing/details/defender-for-cloud/).

- If you're ready to enable enhanced security features now, [Quickstart: Enable enhanced security features](enable-enhanced-security.md) walks you through the steps.

> [!div class="nextstepaction"]
> [Enable Microsoft Defender plans](enable-enhanced-security.md)
