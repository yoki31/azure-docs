---
title: Resiliency in Azure
description: Learn about resiliency in Azure.
author: awysza
ms.service: azure
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: 
---

# Resiliency in Azure

**Resiliency** is a system’s ability to recover from failures and continue to function. It’s not only about avoiding failures but also involves responding to failures in a way that minimizes downtime or data loss. Because failures can occur at various levels, it’s important to have protection for all types based on your service availability requirements. Resiliency in Azure supports and advances capabilities that respond to outages in real time to ensure continuous service and data protection assurance for mission-critical applications that require near-zero downtime and high customer confidence.

Azure includes built-in resiliency services that you can leverage and manage based on your business needs. Whether it’s a single hardware node failure, a rack level failure, a datacenter outage, or a large-scale regional outage, Azure provides solutions that improve resiliency. For example, availability sets ensure that the virtual machines deployed on Azure are distributed across multiple isolated hardware nodes in a cluster. Availability zones protect customers’ applications and data from datacenter failures across multiple physical locations within a region. **Regions** and **availability zones** are central to your application design and resiliency strategy and are discussed in greater detail later in this article.

## Resiliency requirements

The required level of resilience for any Azure solution depends on several considerations. Availability and latency SLA and other business requirements drive the architectural choices and resiliency level and should be considered first. Availability requirements range from how much downtime is acceptable – and how much it costs your business – to the amount of money and time that you can realistically invest in making an application highly available.  

Building resilient systems on Azure is a **shared responsibility**. Microsoft is responsible for the reliability of the cloud platform, including its global network and data centers. Azure customers and partners are responsible for the resilience of their cloud applications, using architectural best practices based on the requirements of each workload. While Azure continually strives for highest possible resiliency in SLA for the cloud platform, you must define your own target SLAs for each workload in your solution. An SLA makes it possible to evaluate whether the architecture meets the business requirements. As you strive for higher percentages of SLA guaranteed uptime, the cost and complexity to achieve that level of availability grows. An uptime of 99.99 percent translates to about five minutes of total downtime per month. Is it worth the additional complexity and cost to reach that percentage? The answer depends on the individual business requirements. While deciding final SLA commitments, understand Microsoft’s supported SLAs. Each Azure service has its own SLA. 

## Building resiliency

You should define your application’s availability requirements at the beginning of planning. Many applications do not need 100% high availability; being aware of this can help to optimize costs during non-critical periods. Identify the type of failures an application can experience as well as the potential effect of each failure. A recovery plan should cover all critical services by finalizing recovery strategy at the individual component and the overall application level. Design your recovery strategy to protect against zonal, regional, and application-level failure. And perform testing of the end-to-end application environment to measure application resiliency and recovery against unexpected failure.  

The following checklist covers the scope of resiliency planning. 

| **Resiliency planning** |
| --- | 
| **Define** availability and recovery targets to meet business requirements. | 
| **Design** the resiliency features of your applications based on the availability requirements. |
| **Align** applications and data platforms to meet your reliability requirements. | 
| **Configure** connection paths to promote availability. | 
| **Use** availability zones and disaster recovery planning where applicable to improve reliability and optimize costs. |
| **Ensure** your application architecture is resilient to failures. | 
| **Know** what happens if SLA requirements are not met. |
| **Identify** possible failure points in the system; application design should tolerate dependency failures by deploying circuit breaking. | 
| **Build** applications that operate in the absence of their dependencies. | 

## Regions and availability zones

Regions and Availability Zones are a big part of the resiliency equation. Regions feature multiple, physically separate Availability Zones, connected by a high-performance network featuring less than 2ms latency between physical zones to help your data stay synchronized and accessible when things go wrong. You can leverage this infrastructure strategically as you architect applications and data infrastructure that automatically replicate and deliver uninterrupted services between zones and across regions. Choose the best region for your needs based on technical and regulatory considerations—service capabilities, data residency, compliance requirements, latency—and begin advancing your resiliency strategy.

Microsoft Azure services support availability zones and are enabled to drive your cloud operations at optimum high availability while supporting your disaster recovery and business continuity strategy needs. Choose the best region for your needs based on technical and regulatory considerations—service capabilities, data residency, compliance requirements, latency—and begin advancing your resiliency strategy. See [Azure regions and availability zones](az-overview.md) for more information.

## Shared responsibility

Building resilient systems on Azure is a shared responsibility. Microsoft is responsible for the reliability of the cloud platform, which includes its global network and datacenters. Azure customers and partners are responsible for the resilience of their cloud applications, using architectural best practices based on the requirements of each workload. See [Business continuity management program in Azure](business-continuity-management-program.md) for more information. 

## Next steps

- [Regions and availability zones in Azure](az-overview.md)
- [Azure services that support availability zones](az-region.md)
- [Azure Resiliency whitepaper](https://azure.microsoft.com/resources/resilience-in-azure-whitepaper/)
- [Azure Well-Architected Framework](https://www.aka.ms/WellArchitected/Framework)
- [Azure architecture guidance](/azure/architecture/high-availability/building-solutions-for-high-availability)
