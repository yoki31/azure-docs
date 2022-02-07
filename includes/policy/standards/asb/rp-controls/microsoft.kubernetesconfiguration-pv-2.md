---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 01/19/2022
ms.author: gwallace
ms.custom: generated
---

|Name<br /><sub>(Azure portal)</sub> |Description |Effect(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Preview\]: Azure Arc enabled Kubernetes clusters should have the Azure Policy extension installed](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b2122c1-8120-4ff5-801b-17625a355590) |The Azure Policy extension for Azure Arc provides at-scale enforcements and safeguards on your Arc enabled Kubernetes clusters in a centralized, consistent manner. Learn more at [https://aka.ms/akspolicydoc](../../../../../articles/governance/policy/concepts/policy-for-kubernetes.md). |AuditIfNotExists, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ArcPolicyExtension_Audit.json) |