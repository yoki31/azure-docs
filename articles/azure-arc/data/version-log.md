---
title: Azure Arc-enabled data services - release versions
description: A log of versions by release date for Azure Arc-enabled data services
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 12/16/2021
ms.topic: conceptual
# Customer intent: As a data professional, I want to understand what versions of components align with specific releases.
---

# Version log

This article identifies the component versions with each release of Azure Arc-enabled data services.

### January 27, 2022

|Component  |Value  |
|--------------------------------------------------------|---------|
|Container images tag |v1.3.0_2022-01-27
|CRD names and versions	|`datacontrollers.arcdata.microsoft.com`: v1beta1, v1, v2</br>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1, v2</br>`monitors.arcdata.microsoft.com`: v1beta1, v1, v2</br>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1, v2, v3</br>`postgresqls.arcdata.microsoft.com`: v1beta1, v1beta2</br>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1, v1</br>`dags.sql.arcdata.microsoft.com`: v1beta1, v2beta2</br>`activedirectoryconnectors.arcdata.microsoft.com`: v1beta1|
|ARM API version|2021-11-01|
|`arcdata` Azure CLI extension version|	1.2.0|
|Arc enabled Kubernetes helm chart extension version|1.1.18501004|
|Arc Data extension for Azure Data Studio|1.0|

### December 16, 2021

The following table describes the components in this release.

|Component  |Value  |
|--------------------------------------------------------|---------|
|Container images tag                                    | v1.2.0_2021-12-15 |
|CRD names and versions                                  | `datacontrollers.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1, v1beta2 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1, v1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1, v2beta2<br/>`activedirectoryconnectors.arcdata.microsoft.com`: v1beta1 |
|ARM API version                                         | 2021-11-01 |
|`arcdata` Azure CLI extension version                   | 1.1.2 |
|Arc enabled Kubernetes helm chart extension version     | 1.1.18031001 |
|Arc Data extension for Azure Data Studio                | 0.11 |

## November 2, 2021

The following table describes the components in this release.

|Component  |Value  |
|--------------------------------------------------------|---------|
|Container images tag                                    | v1.1.0_2021-11-02 |
|CRD names and versions                                  | `datacontrollers.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1, v1beta2 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1, v1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1, v2beta2 |
|ARM API version                                         | 2021-11-01 |
|`arcdata` Azure CLI extension version                   | 1.1.0, (Nov 3),</br>1.1.1 (Nov4) |
|Arc enabled Kubernetes helm chart extension version     | 1.0.17551005 - Required if upgrade from GA <br/><br/> 1.1.17561007 - GA+1/Nov release chart |
|Arc Data extension for Azure Data Studio                | 0.9.7 |

## August 3, 2021

This release updates the Azure Arc extension for Azure Data Studio to align with July 30, general availability. The following table describes the components this release updates. 

|Component  |Value  |
|--------------------------------------------------------|---------|
|Arc Data extension for Azure Data Studio                | 0.9.6 |

All other components are the same as previously released.

## July 30, 2021

This release introduces general availability for Azure Arc-enabled SQL Managed Instance general purpose and Azure Arc-enabled SQL Server. The following table describes the components in this release.

|Component  |Value  |
|--------------------------------------------------------|---------|
|Container images tag                                    | v1.0.0_2021-07-30 |
|CRD names and versions                                  |`datacontrollers.arcdata.microsoft.com`: v1beta1, v1 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1 <br/> |
|ARM API version                                         | 2021-08-01 (stable) |
|`arcdata` Azure CLI extension version                   | 1.0 |
|Arc enabled Kubernetes helm chart extension version     | 1.0.16701001, release train: stable |
|Arc Data extension for Azure Data Studio                | 0.9.5 |
