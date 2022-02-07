---
title: What are Azure Arc-enabled data services
description: Introduces Azure Arc-enabled data services
ms.custom: references_regions
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: overview
# Customer intent: As a data professional, I want to understand why my solutions would benefit from running with Azure Arc-enabled data services so that I can leverage the capability of the feature.
---

# What are Azure Arc-enabled data services?

Azure Arc makes it possible to run Azure data services on-premises, at the edge, and in public clouds using Kubernetes and the infrastructure of your choice.

Currently, the following Azure Arc-enabled data services are available: 

- SQL Managed Instance
- PostgreSQL Hyperscale (preview)

For an introduction to how Azure Arc-enabled data services supports your hybrid work environment, see this introductory video:

> [!VIDEO https://docs.microsoft.com/Shows//Inside-Azure-for-IT/Choose-the-right-data-solution-for-your-hybrid-environment/player?format=ny]

## Always current

Azure Arc-enabled data services such as Azure Arc-enabled SQL managed instance and Azure Arc-enabled PostgreSQL Hyperscale receive updates on a frequent basis including servicing patches and new features similar to the experience in Azure. Updates from the Microsoft Container Registry are provided to you and deployment cadences are set by you in accordance with your policies. This way, on-premises databases can stay up to date while ensuring you maintain control. Because Azure Arc-enabled data services are a subscription service, you will no longer face end-of-support situations for your databases.

## Elastic scale

Cloud-like elasticity on-premises enables you to scale you databases up or down dynamically in much the same way as they do in Azure, based on the available capacity of your infrastructure. This capability can satisfy burst scenarios that have volatile needs, including scenarios that require ingesting and querying data in real time, at any scale, with sub-second response time. In addition, you can also scale out database instances using the unique hyper scale deployment option of Azure Database for PostgreSQL Hyperscale. This capability gives data workloads an additional boost on capacity optimization, using unique scale-*out* reads and writes.

## Self-service provisioning

Azure Arc also provides other cloud benefits such as fast deployment and automation at scale. Thanks to Kubernetes-based orchestration, you can deploy a database in seconds using either GUI or CLI tools.

## Unified management

Using familiar tools such as the Azure portal, Azure Data Studio, and the Azure CLI (`az`) with the `arcdata` extension, you can now gain a unified view of all your data assets deployed with Azure Arc. You are able to not only view and manage a variety of relational databases across your environment and Azure, but also get logs and telemetry from Kubernetes APIs to analyze the underlying infrastructure capacity and health. Besides having localized log analytics and performance monitoring, you can now leverage Azure Monitor for comprehensive operational insights across your entire estate.

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## Disconnected scenario support

Many of the services such as self-service provisioning, automated backups/restore, and monitoring can run locally in your infrastructure with or without a direct connection to Azure. Connecting directly to Azure opens up additional options for integration with other Azure services such as Azure Monitor and the ability to use the Azure portal and Azure Resource Manager APIs from anywhere in the world to manage your Azure Arc-enabled data services.

## Supported regions

The following table describes the scenarios that are currently supported for Azure Arc-enabled data services.

|Azure Regions  |Direct connected mode  |Indirect connected mode  |
|---------|---------|---------|
|East US|Available|Available
|East US 2|Available|Available
|West US|Available|Available
|West US 2|Available|Available
|West US 3|Available|Available
|North Central US | Available | Available
|Central US|Available|Available
|South Central US|Available|Available
|UK South|Available|Available
|France Central|Available|Available
|West Europe |Available |Available
|North Europe|Available|Available
|Japan East|Available|Available
|Korea Central|Available|Available
|East Asia|Available|Available
|Southeast Asia|Available|Available
|Australia East|Available|Available

## Next steps

> **Just want to try things out?**  
> Get started quickly with [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) on Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) or in an Azure VM.
>
>In addition, deploy [Jumpstart ArcBox](https://azurearcjumpstart.io/azure_jumpstart_arcbox/), an easy to deploy sandbox for all things Azure Arc. ArcBox is designed to be completely self-contained within a single Azure subscription and resource group, which will make it easy for you to get hands-on with all available Azure Arc-enabled technology with nothing more than an available Azure subscription.

[Install the client tools](install-client-tools.md)

[Plan your Azure Arc data services deployment](plan-azure-arc-data-services.md) (requires installing the client tools first)

[Create an Azure SQL managed instance on Azure Arc](create-sql-managed-instance.md) (requires creation of an Azure Arc data controller first)

[Create an Azure Database for PostgreSQL Hyperscale server group on Azure Arc](create-postgresql-hyperscale-server-group.md) (requires creation of an Azure Arc data controller first)
