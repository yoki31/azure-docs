---
title: Detect threats quickly with near-real-time (NRT) analytics rules in Microsoft Sentinel | Microsoft Docs
description: This article explains how the new near-real-time (NRT) analytics rules can help you detect threats quickly in Microsoft Sentinel.
author: yelevin
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
---
# Detect threats quickly with near-real-time (NRT) analytics rules in Microsoft Sentinel

> [!IMPORTANT]
>
> - Near-real-time (NRT) rules are currently in **PREVIEW**. See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## What are near-real-time (NRT) analytics rules?

When you're faced with security threats, time and speed are of the essence. You need to be aware of threats as they materialize so you can analyze and respond quickly to contain them. Microsoft Sentinel's near-real-time (NRT) analytics rules offer you faster threat detection - closer to that of an on-premises SIEM - and the ability to shorten response times in specific scenarios.

Microsoft Sentinel’s [near-real-time analytics rules](detect-threats-built-in.md#nrt) provide up-to-the-minute threat detection out-of-the-box. This type of rule was designed to be highly responsive by running its query at intervals just one minute apart.

## How do they work?

NRT rules are hard-coded to run once every minute and capture events ingested in the preceding minute, so as to be able to supply you with information as up-to-the-minute as possible.

Unlike regular scheduled rules that run on a built-in five-minute delay to account for ingestion time lag, NRT rules run on just a two-minute delay, solving the ingestion delay problem by querying on events' ingestion time instead of their generation time at the source (the TimeGenerated field). This results in improvements of both frequency and accuracy in your detections. (To understand this issue more completely, see [Query scheduling and alert threshold](detect-threats-custom.md#query-scheduling-and-alert-threshold) and [Handle ingestion delay in scheduled analytics rules](ingestion-delay.md).)

NRT rules have many of the same features and capabilities as scheduled analytics rules. The full set of alert enrichment capabilities is available – you can map entities and surface custom details, and you can configure dynamic content for alert details. You can choose how alerts are grouped into incidents, you can temporarily suppress the running of a query after it generates a result, and you can define automation rules and playbooks to run in response to alerts and incidents generated from the rule.

For the time being, these templates have limited application as outlined below, but the technology is rapidly evolving and growing.

## Considerations
The following limitations currently govern the use of NRT rules:

1. No more than 20 rules can be defined per customer at this time.

1. As this type of rule is new, its syntax is currently limited but will gradually evolve. Therefore, at this time the following restrictions are in effect:

    1. The query defined in an NRT rule can reference **only one table**. Queries can, however, refer to multiple watchlists and to threat intelligence feeds.

    1. You cannot use unions or joins.

    1. Because this rule type is in near real time, we have reduced the built-in delay to a minimum (two minutes).

    1. Since NRT rules use the ingestion time rather than the event generation time (represented by the TimeGenerated field), you can safely ignore the data source delay and the ingestion time latency (see above).

    1. Queries can run only within a single workspace. There is no cross-workspace capability.

    1. Event grouping is not configurable. NRT rules produce a single alert that groups all the applicable events.

## Next steps

In this document, you learned how near-real-time (NRT) analytics rules work in Microsoft Sentinel.

- Learn how to [create NRT rules](create-nrt-rules.md).
- Learn about [other types of analytics rules](detect-threats-built-in.md).
