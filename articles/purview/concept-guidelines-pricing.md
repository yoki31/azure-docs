---
title: Azure Purview pricing guidelines
description: This article provides a guideline towards understanding the various components in Azure Purview pricing.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: conceptual
ms.date: 10/03/2021
ms.custom: ignite-fall-2021
---

# Azure Purview pricing   

Azure Purview enables a unified governance experience by providing a single pane of glass for managing data governance by enabling automated scanning and classifying data at scale.


## Why do you need to understand the components of the Azure Purview pricing? 

- While the pricing for Azure Purview is on a subscription-based **Pay-As-You-Go** model, there are various dimensions that you can consider while budgeting for Azure Purview
- This guideline is intended to help you plan the budgeting for Azure Purview by providing a view on the various control factors that impact the budget


## Factors impacting Azure Pricing  
There are **direct** and **indirect** costs that need to be considered while planning the Azure Purview budgeting and cost management.

### Direct costs

Direct costs impacting Azure Purview pricing are based on the following three dimensions:
- **Elastic data map**
- **Automated scanning & classification**
- **Advanced resource sets**

#### Elastic data map

- The **Data map** is the foundation of the Azure Purview architecture and so needs to be up to date with asset information in the data estate at any given point

- The data map is charged in terms of **Capacity Unit** (CU). The data map is provisioned at one CU if the catalog is storing up to 10 GB of metadata storage and serves up to 25 data map operations/sec

- While provisioning an account initially, the data map is always provisioned at one CU

- However, the data map scales automatically between the minimal and maximal limits of that elasticity window, to cater to changes in the data map with respect to two key factors - **operation throughput** and **metadata storage**

##### Operation throughput

- An event driven factor based on the Create, Read, Update, Delete operations performed on the data map
- Some examples of the data map operations would be:
        - Creating an asset in Data Map
        - Adding a relationship to an asset such as owner, steward, parent, lineage
        - Editing an asset to add business metadata such as description, glossary term
        - Keyword-search returning results to search result page
        - Importing or exporting information using API
- If there are multiple queries executed on the Data Map, the number of I/O operations also increases resulting in the scaling up of the data map
- The number of concurrent users also forms a factor governing the data map capacity unit
- Other factors to consider are type of search query, API interaction, workflows, approvals, and so on
- Data burst level
    - When there is a need for more operations/second throughput, the Data map can autoscale within the elasticity window to cater to the changed load
    - This constitutes the **burst characteristic** that needs to be estimated and planned for
    - The burst characteristic comprises the **burst level** and the **burst duration** for which the burst exists
        - The **burst level** is a multiplicative index of the expected consistent elasticity under steady state
        - The **burst duration** is the percentage of the month that such bursts (in elasticity) are expected because of growing metadata or higher number of operations on the data map


##### Metadata storage

- If the number of assets reduces in the data estate, and are then removed in the data map through subsequent incremental scans, the storage component automatically reduces and so the data map scales down


#### Automated scanning & classification

- A **full scan** processes all assets within a selected scope of a data source whereas an **incremental scan** detects and processes assets, which have been created, modified, or deleted since the previous successful scan 

- All scans (full or Incremental scans) will pick up **updated, modified, or deleted** assets

- It is important to consider and avoid the scenarios when multiple people or groups belonging to different departments  set up scans for the same data source resulting in additional pricing for duplicate scanning

- Schedule **frequent incremental scans** post the initial full scan aligned with the changes in the data estate. This will ensure the data map is kept up to date always and the incremental scans consume lesser v-core hours as compared to a full scan

- The **“View Details”** link for a data source will enable users to run a full scan. However, consider running incremental scans after a full scan for optimized scanning excepting when there is a change to the scan rule set (classifications/file types)

- **Register the data source at a parent collection** and **Scope scans at child collection** with different access controls to ensure there are no duplicate scanning costs being entailed

- Curtail the users who are allowed to register data sources for scanning through **fine grained access control** and **Data Source Administrator** role using [Collection authorization](./catalog-permissions.md). This will ensure only valid data sources are allowed to be registered and scanning v-core hours is controlled resulting in lower costs for scanning

- Consider that the **type of data source** and the **number of assets** being scanned impact the scan duration

- **Create custom scan rule sets** to include only the subset of **file types** available in your data estate and **classifications** that are relevant to your business requirements to ensure optimal use of the scanners

- While creating a new scan for a data source, follow the **order of preparation** recommended before actually running the scan. This includes gathering the requirements for **business specific classifications** and **file types** (for storage accounts) to enable appropriate scan rule sets to be defined to avoid multiple scans and control unnecessary costs for multiple scans through missed requirements

- Align your scan schedules with Self-Hosted Integration Runtime (SHIR) VMs (Virtual Machines) size to avoid extra costs linked to virtual machines


#### Advanced resource sets

- Azure Purview uses **resource sets** to address the challenge of mapping large numbers of data assets to a single logical resource by providing the ability to scan all the files in the data lake and find patterns (GUID, localization patterns, etc.) to group them as a single asset in the data map

- **Advanced Resource Set** is an optional feature, which allows for customers to get enriched resource set information computed such as Total Size, Partition Count, etc., and enables the customization of resource set grouping via pattern rules. If Advanced Resource Set feature is not enabled, your data catalog will still contain resource set assets, but without the aggregated properties. There will be no "Resource Set" meter billed to the customer in this case.

- Use the basic resource set feature, before switching on the Advanced Resource Sets in Azure Purview to verify if requirements are met

- Consider turning on Advanced Resource Sets if:
    - your data lakes schema is constantly changing, and you are looking for additional value beyond the basic Resource Set feature to enable Azure Purview to compute parameters such as #partitions, size of the data estate, etc., as a service
    - there is a need to customize how resource set assets get grouped 

- It is important to note that billing for Advanced Resource Sets is based on the compute used by the offline tier to aggregate resource set information and is dependent on the size/number of resource sets in your catalog


### Indirect costs   

Indirect costs impacting Azure Purview pricing to be considered are:

- [Managed resources](https://azure.microsoft.com/pricing/details/azure-purview/)
    - When an Azure Purview account is provisioned, a storage account and event hub queue are created within the subscription in order to cater to secured scanning, which may be charged separately


- [Azure private endpoint](./catalog-private-link.md)
    - Azure private end points are used for Azure Purview accounts where it is required for users on a virtual network (VNet) to securely access the catalog over a private link
    - The prerequisites for setting up private endpoints could result in extra costs

- [Self-hosted integration runtime related costs](./manage-integration-runtimes.md) 
    - Self-hosted integration runtime requires infrastructure, which results in extra costs
    - It is required to deploy and register Self-hosted integration runtime (SHIR) inside the same virtual network where Azure Purview ingestion private endpoints are deployed
    - [Additional memory requirements for scanning](./register-scan-sapecc-source.md#create-and-run-scan)
        - Certain data sources such as SAP require additional memory on the SHIR machine for scanning


- [Virtual Machine Sizing](../virtual-machines/sizes.md)
    - Plan virtual machine sizing in order to distribute the scanning workload across VMs to optimize the v-cores utilized while running scans

- [Microsoft 365 license](./create-sensitivity-label.md) 
    - Microsoft Information Protection (MIP) sensitivity labels can be automatically applied to your Azure assets in Azure Purview.
    - MIP sensitivity labels are created and managed in the Microsoft 365 Security and Compliance Center.
    - To create sensitivity labels for use in Azure Purview, you must have an active Microsoft 365 license, which offers the benefit of automatic labeling. For the full list of licenses, see the Sensitivity labels in Azure Purview FAQ. 

- [Azure Alerts](../azure-monitor/alerts/alerts-overview.md)
    - Azure Alerts can notify customers of issues found with infrastructure or applications using the monitoring data in Azure Monitor
    - The additional pricing for Azure Alerts is available [here](https://azure.microsoft.com/pricing/details/monitor/)

- [Cost Management Budgets & Alerts](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md)
    - Automatically generated cost alerts are used in Azure to monitor Azure usage and spending based on when Azure resources are consumed
    - Azure allows you to create and manage Azure budgets. Refer [tutorial](../cost-management-billing/costs/tutorial-acm-create-budgets.md)

- Multi-cloud egress charges
    - Consider the egress charges (minimal charges added as a part of the multi-cloud subscription) associated with scanning multi-cloud (for example AWS, Google) data sources running native services excepting the S3 and RDS sources


## Next steps
- [Azure Purview pricing page](https://azure.microsoft.com/pricing/details/azure-purview/)
