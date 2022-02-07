---
title: Job classification concepts for Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Learn about the Azure Communication Services Job Router classification concepts.
author: jasonshave
manager: phans
services: azure-communication-services

ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
---

# Job classification

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

When you submit a job to Job Router, you can either specify the queue, priority, and worker selectors manually or you can specify a classification policy to drive these values.

If you choose to use a classification policy, you will receive a [JobClassified Event][job_classified_event] or a [JobClassificationFailed Event][job_classify_failed_event] with the result.  Once the job has been successfully classified, it will be automatically queued.  If the classification process fails, you'll need to intervene to fix it.

The process of classifying a Job involves optionally setting the following properties:

- Priority
- Worker Selectors
- Queue ID

## Prioritization rule

The priority of a Job can be resolved during classification using one of many rule engines.

See the [Rule concepts](router-rule-concepts.md) page for more information.

## Worker selectors

Each job carries a collection of worker selectors, that are evaluated against the worker labels.  These are conditions that need to be true of a worker to be a match.
You can use the classification policy to attach these conditions to a job.  You can do this by specifying one or more selector attachments.

For more information see the section [below](#using-label-selector-attachments).

## Queue selectors

You can also specify a collection of label selector attachments to select the Queue based on its labels.

For more information see the section [below](#using-label-selector-attachments).

## Using label selector attachments

The following label selector attachments are available:

**Static label selector -** Always attaches the given `LabelSelector` to the Job.

**Conditional label selector -** Evaluates a condition defined by a [rule](router-rule-concepts.md).  If it resolves to `true`, then the specified collection of selectors will be attached to the Job.

**Passthrough label selector -** Attaches a selector to the Job with the specified key and operator but gets the value from the Job label of the same key.

**Rule label selector -** Sources a collection of selectors from one of many rule engines. Read the [RouterRule concepts](router-rule-concepts.md) page for more information.

**Weighted allocation label selector -** Enables you to specify a percentage-based weighting and a collection of selectors to attach based on the weighting allocation. For example, you may want 30% of the Jobs to go to "Vendor 1" and 70% of Jobs to go to "Vendor 2".

## Reclassifying a job

Once a Job has been classified, it can be reclassified in the following ways:

1. You can update the Job labels, which will cause the Job Router to evaluate the new labels with the previous Classification Policy.
2. You can update the Classification Policy ID of a Job, which will cause Job Router to process the existing Job against the new policy.
3. An Exception Policy **trigger** can take the **action** of requesting a Job be reclassified.

> [!NOTE]
> The Job Router SDK includes an `UpdateJobLabels` method which simply updates the labels without causing the Job Router to execute the reclassification process.

<!-- LINKS -->
[subscribe_events]: ../../how-tos/router-sdk/subscribe-events.md
[job_classified_event]: ../../how-tos/router-sdk/subscribe-events.md#microsoftcommunicationrouterjobclassified
[job_classify_failed_event]: ../../how-tos/router-sdk/subscribe-events.md#microsoftcommunicationrouterjobclassificationfailed
