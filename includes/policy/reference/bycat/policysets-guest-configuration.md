---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 01/18/2022
ms.author: gwallace
ms.custom: generated
---

|Name |Description |Policies |Version |
|---|---|---|---|
|[\[Preview\]:Windows machines should meet requirements for the Azure compute security baseline](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |This initiative audits Windows machines with settings that do not meet the Azure compute security baseline. For details, please visit [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) |29 |2.0.1-preview |
|[Audit machines with insecure password security settings](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |This initiative deploys the policy requirements and audits machines with insecure password security settings. For more information on Guest Configuration policies, please visit [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) |9 |1.0.0 |
|[Deploy prerequisites to enable Guest Configuration policies on virtual machines](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |This initiative adds a system-assigned managed identity and deploys the platform-appropriate Guest Configuration extension to virtual machines that are eligible to be monitored by Guest Configuration policies. This is a prerequisite for all Guest Configuration policies and must be assigned to the policy assignment scope before using any Guest Configuration policy. For more information on Guest Configuration, visit [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md). |4 |1.0.0 |