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
|[Linux machines should meet requirements for the Azure compute security baseline](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) |Requires that prerequisites are deployed to the policy assignment scope. For details, visit [https://aka.ms/gcpol](../../../../../articles/governance/policy/concepts/guest-configuration.md). Machines are non-compliant if the machine is not configured correctly for one of the recommendations in the Azure compute security baseline. |AuditIfNotExists, Disabled |[1.3.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AzureLinuxBaseline_AINE.json) |
|[Windows machines should meet requirements of the Azure compute security baseline](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) |Requires that prerequisites are deployed to the policy assignment scope. For details, visit [https://aka.ms/gcpol](../../../../../articles/governance/policy/concepts/guest-configuration.md). Machines are non-compliant if the machine is not configured correctly for one of the recommendations in the Azure compute security baseline. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AzureWindowsBaseline_AINE.json) |