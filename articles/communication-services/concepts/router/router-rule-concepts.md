---
title: Job Router rule engines
titleSuffix: An Azure Communication Services concept document
description: Learn about the Azure Communication Services Job Router rules engine concepts.
author: jasonshave
manager: phans
services: azure-communication-services

ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp
---

# Job Router rule engines

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Job Router can use one or more rule engines to process data and make decisions about your Jobs and Workers. This document covers what the rule engines do and why you may want to apply them in your implementation.

## Rules engine overview

Controlling the behavior of your implementation can often include complex decision making. Job Router provides a flexible way to invoke behavior programmatically using various rule engines. Job Router's rule engines generally take a set of **labels** defined on objects such as a Job, a Queue, or a Worker as an input, apply the rule and produce an output.

Depending on where you apply rules in Job Router, the result can vary. For example, a Classification Policy can choose a Queue ID based on the labels defined on the input of a Job. In another example, a Distribution Policy can find the best Worker using a custom scoring rule.

## Rule engine types

The following rule engine types exist in Job Router to provide flexibility in how your Jobs are processed.

**Static rule -** Used to specify a static value such as selecting a specific Queue ID.

**Expression rule -** Uses the [PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/) language to define your rule as an inline expression.

**Azure Function rule -** Allows the Job Router to pass the input labels as a payload to an Azure Function and respond back with an output value.

### Example: Use a static rule to set the priority of a job

In this example a `StaticRule`, which is a subtype of `RouterRule` can be used to set the priority of all Jobs, which use this classification policy.

::: zone pivot="programming-language-csharp"

```csharp
await client.SetClassificationPolicyAsync(
    id: "my-policy-id",
    prioritizationRule: new StaticRule(5)
);
```

::: zone-end

::: zone pivot="programming-language-javascript"

```typescript
await client.upsertClassificationPolicy({
    id: "my-policy-id",
    prioritizationRule: {
        kind: "static-rule",
        value: 5
    }
});
```

::: zone-end

### Example: Use an expression rule to set the priority of a job

In this example a `ExpressionRule`, which is a subtype of `RouterRule` can be used to set the priority of all Jobs, which use this classification policy.

::: zone pivot="programming-language-csharp"

```csharp
await client.SetClassificationPolicyAsync(
    id: "my-policy-id",
    prioritizationRule: new ExpressionRule("If(job.Urgent = true, 10, 5)")
);
```

::: zone-end

::: zone pivot="programming-language-javascript"

```typescript
await client.upsertClassificationPolicy({
    id: "my-policy-id",
    prioritizationRule: {
        kind: "expression-rule",
        expression: "If(job.Urgent = true, 10, 5)"
    }
});
```

::: zone-end