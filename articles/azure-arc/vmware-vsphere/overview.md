---
title: What is Azure Arc-enabled VMware vSphere (preview)?
description: Azure Arc-enabled VMware vSphere (preview) extends Azure governance and management capabilities to VMware vSphere infrastructure and delivers a consistent management experience across both platforms. 
ms.topic: overview
ms.date: 11/10/2021
ms.custom: references_regions
---

# What is Azure Arc-enabled VMware vSphere (preview)?

Azure Arc-enabled VMware vSphere (preview) extends Azure governance and management capabilities to VMware vSphere infrastructure. With Azure Arc-enabled VMware vSphere, you get a consistent management experience across Azure and VMware vSphere infrastructure.

Arc-enabled VMware vSphere (preview) allows you to:

- Perform various VMware virtual machine (VM) lifecycle operations directly from Azure, such as create, start/stop, resize, and delete.

- Empower developers and application teams to self-serve VM operations on-demand using [Azure role-based access control](../../role-based-access-control/overview.md) (RBAC).

- Browse your VMware vSphere resources (VMs, templates, networks, and storage) in Azure, providing you a single pane view for your infrastructure across both environments. You can also discover and onboard existing VMware VMs to Azure.

- Conduct governance and monitoring operations across Azure and VMware VMs by enabling guest management (installing the [Azure Arc-enabled servers Connected Machine agent](../servers/agent-overview.md)).

> [!IMPORTANT]
> In the interest of ensuring new features are documented no later than their release, this page may include documentation for features that may not yet be publicly available.

## How does it work?

To deliver this experience, you need to deploy the [Azure Arc resource bridge](../resource-bridge/overview.md) (preview), which is a virtual appliance, in your vSphere environment. It connects your vCenter Server to Azure. Azure Arc resource bridge (preview) enables you to represent the VMware resources in Azure and do various operations on them.

## Supported VMware vSphere versions

Azure Arc-enabled VMware vSphere (preview) works with VMware vSphere version 6.7.

> [!NOTE]
> Azure Arc-enabled VMware vSphere  (preview)  supports vCenters with a maximum of 2500 VMs. If your vCenter has more than 2500 VMs, it is not recommended to use Arc-enabled VMware vSphere with it at this point.

## Supported scenarios

The following scenarios are supported in Azure Arc-enabled VMware vSphere (preview):

- Virtualized Infrastructure Administrators/Cloud Administrators can connect a vCenter instance to Azure and browse the VMware virtual machine inventory in Azure.

- Administrators can use the Azure portal to browse VMware vSphere inventory and register virtual machines resource pools, networks, and templates into Azure. They can also enable guest management on many registered virtual machines at once.

- Administrators can provide app teams/developers fine-grained permissions on those VMware resources through Azure RBAC.

- App teams can use Azure interfaces (portal, CLI, or REST API) to manage the lifecycle of on-premises VMs they use for deploying their applications (CRUD, Start/Stop/Restart).

- App teams and administrators can install extensions such as the Log Analytics agent, Custom Script Extension, and Dependency Agent, on  the virtual machines and do operations supported by the extensions.

## Supported regions

You can use Azure Arc-enabled VMware vSphere (preview) in these supported regions:

- East US

- West Europe

## Next steps

- [Connect VMware vCenter to Azure Arc using the helper script](quick-start-connect-vcenter-to-arc-using-script.md)
