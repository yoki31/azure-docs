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
|[\[Preview\]: Azure Arc enabled Kubernetes clusters should have Azure Defender's extension installed](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Defender's extension for Azure Arc provides threat protection for your Arc enabled Kubernetes clusters. The extension collects data from nodes in the cluster and sends it to the Azure Defender for Kubernetes backend in the cloud for further analysis. Learn more in [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](/azure/security-center/defender-for-kubernetes-azure-arc). |AuditIfNotExists, Disabled |[4.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |