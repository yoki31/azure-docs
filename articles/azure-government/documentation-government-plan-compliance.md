---
title: Azure Government compliance
description: Provides an overview of the available compliance assurances for Azure Government
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.custom: references_regions
ms.author: stevevi
author: stevevi
recommendations: false
ms.date: 01/28/2022
---

# Azure Government compliance

Microsoft Azure Government meets demanding US government compliance requirements that mandate formal assessments and authorizations, including:

- [Federal Risk and Authorization Management Program](https://www.fedramp.gov/) (FedRAMP)
- Department of Defense (DoD) Cloud Computing [Security Requirements Guide](https://public.cyber.mil/dccs/dccs-documents/) (SRG) Impact Level (IL) 2, 4, and 5

Azure Government maintains the following authorizations that pertain to Azure Government regions US Gov Arizona, US Gov Texas, and US Gov Virginia:

- [FedRAMP High](/azure/compliance/offerings/offering-fedramp) Provisional Authorization to Operate (P-ATO) issued by the FedRAMP Joint Authorization Board (JAB)
- [DoD IL2](/azure/compliance/offerings/offering-dod-il2) Provisional Authorization (PA) issued by the Defense Information Systems Agency (DISA)
- [DoD IL4](/azure/compliance/offerings/offering-dod-il4) PA issued by DISA
- [DoD IL5](/azure/compliance/offerings/offering-dod-il5) PA issued by DISA

For links to additional Azure Government compliance assurances, see [Azure compliance](../compliance/index.yml). For example, Azure Government can help you meet your compliance obligations with many US government requirements, including:

- [Criminal Justice Information Services (CJIS)](/azure/compliance/offerings/offering-cjis)
- [Internal Revenue Service (IRS) Publication 1075](/azure/compliance/offerings/offering-irs-1075)
- [Defense Federal Acquisition Regulation Supplement (DFARS)](/azure/compliance/offerings/offering-dfars)
- [International Traffic in Arms Regulations (ITAR)](/azure/compliance/offerings/offering-itar)
- [Export Administration Regulations (EAR)](/azure/compliance/offerings/offering-ear)
- [Federal Information Processing Standard (FIPS) 140](/azure/compliance/offerings/offering-fips-140-2)
- [National Institute of Standards and Technology (NIST) 800-171](/azure/compliance/offerings/offering-nist-800-171)
- [National Defense Authorization Act (NDAA) Section 889 and Section 1634](/azure/compliance/offerings/offering-ndaa-section-889)
- [North American Electric Reliability Corporation (NERC) Critical Infrastructure Protection (CIP) standards](/azure/compliance/offerings/offering-nerc)
- [Health Insurance Portability and Accountability Act of 1996 (HIPAA)](/azure/compliance/offerings/offering-hipaa-us)
- [Electronic Prescriptions for Controlled Substances (EPCS)](/azure/compliance/offerings/offering-epcs-us)
- And many more US government, global, and industry standards

For current Azure Government regions and available services, see [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=non-regional,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia).

> [!NOTE]
>
> - Some Azure services deployed in Azure Government regions (US Gov Arizona, US Gov Texas, and US Gov Virginia) require extra configuration to meet DoD IL5 compute and storage isolation requirements, as explained in **[Isolation guidelines for Impact Level 5 workloads](./documentation-government-impact-level-5.md).**
> - For DoD IL5 PA compliance scope in Azure Government DoD regions (US DoD Central and US DoD East), see **[Azure Government DoD regions IL5 audit scope](./documentation-government-overview-dod.md#azure-government-dod-regions-il5-audit-scope).**

## Services in audit scope

For a detailed list of Azure, Dynamics 365, Microsoft 365, and Power Platform services in FedRAMP and DoD compliance audit scope, see:

- [Azure public services by audit scope](./compliance/azure-services-in-fedramp-auditscope.md#azure-public-services-by-audit-scope)
- [Azure Government services by audit scope](./compliance/azure-services-in-fedramp-auditscope.md#azure-government-services-by-audit-scope)

## Audit documentation

You can access Azure and Azure Government audit reports and related documentation via the [Service Trust Portal](https://servicetrust.microsoft.com) (STP) in the following sections:

- STP [Audit Reports](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3), which has a subsection for FedRAMP Reports.
- STP [Data Protection Resources](https://servicetrust.microsoft.com/ViewPage/TrustDocumentsV3), which is further divided into Compliance Guides, FAQ and White Papers, and Pen Test and Security Assessments subsections.

You must sign in to access audit reports on the STP. For more information, see [Get started with the Microsoft Service Trust Portal](https://aka.ms/stphelp).

Alternatively, you can access certain audit reports and certificates in the Azure or Azure Government portal by navigating to *Home > Security Center > Regulatory compliance > Audit reports* or using direct links based on your subscription (sign in required):

- Azure portal [audit reports blade](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/AuditReportsBlade)
- Azure Government portal [audit reports blade](https://portal.azure.us/#blade/Microsoft_Azure_Security/AuditReportsBlade)

You must have an existing subscription or free trial account in [Azure](https://azure.microsoft.com/free/) or [Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/) to download audit documents.

## Azure Policy regulatory compliance built-in initiatives

For additional customer assistance, Microsoft provides **Azure Policy regulatory compliance built-in initiatives**, which map to **compliance domains** and **controls** in key US government standards, including:

- [FedRAMP High](../governance/policy/samples/gov-fedramp-high.md)
- [DoD IL4](../governance/policy/samples/gov-dod-impact-level-4.md)
- [DoD IL5](../governance/policy/samples/gov-dod-impact-level-5.md)

For additional regulatory compliance built-in initiatives that pertain to Azure Government, see [Azure Policy samples](../governance/policy/samples/index.md#regulatory-compliance).

Regulatory compliance in Azure Policy provides built-in initiative definitions to view a list of the controls and compliance domains based on responsibility - customer, Microsoft, or shared. For Microsoft-responsible controls, we provide additional audit result details based on third-party attestations and our control implementation details to achieve that compliance. Each control is associated with one or more Azure Policy definitions. These policies may help you [assess compliance](../governance/policy/how-to/get-compliance-data.md) with the control; however, compliance in Azure Policy is only a partial view of your overall compliance status. Azure Policy helps to enforce organizational standards and assess compliance at scale. Through its compliance dashboard, it provides an aggregated view to evaluate the overall state of the environment, with the ability to drill down to more granular status.

## Next steps

- [Azure compliance](../compliance/index.yml)
- [Azure and other Microsoft services compliance offerings](/azure/compliance/offerings/)
- [Azure Policy overview](../governance/policy/overview.md)
- [Azure Policy regulatory compliance built-in initiatives](../governance/policy/samples/index.md#regulatory-compliance)
- [Azure Government overview](./documentation-government-welcome.md)
- [Azure Government security](./documentation-government-plan-security.md)
- [Compare Azure Government and global Azure](./compare-azure-government-global-azure.md)
- [Azure Government services by audit scope](./compliance/azure-services-in-fedramp-auditscope.md#azure-government-services-by-audit-scope)
- [Azure Government isolation guidelines for Impact Level 5 workloads](./documentation-government-impact-level-5.md)
- [Azure Government DoD overview](./documentation-government-overview-dod.md)
