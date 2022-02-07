---
title: Upgrade directly connected Azure Arc data controller using the CLI
description: Article describes how to upgrade a directly connected Azure Arc data controller using the CLI
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 12/10/2021
ms.topic: how-to
---

# Upgrade a directly connected Azure Arc data controller using the CLI

This article describes how to upgrade a directly connected Azure Arc-enabled data controller using the Azure CLI (`az`).

During a data controller upgrade, portions of the data control plane such as Custom Resource Definitions (CRDs) and containers may be upgraded. An upgrade of the data controller will not cause downtime for the data services (SQL Managed Instance or PostgreSQL Hyperscale server).

## Prerequisites

You will need a directly connected data controller with the imageTag v1.0.0_2021-07-30 or later.

To check the version, run:

```console
kubectl get datacontrollers -n <namespace> -o custom-columns=BUILD:.spec.docker.imageTag
```

## Install tools

Before you can proceed with the tasks in this article you need to install:

- The [Azure CLI (az)](/cli/azure/install-azure-cli)
- The [`arcdata` extension for Azure CLI](install-arcdata-extension.md)

[!INCLUDE [azure-arc-angle-bracket-example](../../../includes/azure-arc-angle-bracket-example.md)]

## View available images and chose a version

Pull the list of available images for the data controller with the following command:

   ```azurecli
   az arcdata dc list-upgrades --k8s-namespace <custom location> 
   ```

The command above returns output like the following example:

```output
Found 2 valid versions.  The current datacontroller version is v1.0.0_2021-07-30.
v1.1.0_2021-11-02
v1.0.0_2021-07-30
```

## Upgrade data controller

This section shows how to upgrade a directly connected data controller.

> [!NOTE]
> Some of the data services tiers and modes are generally available and some are in preview.
> If you install GA and preview services on the same data controller, you can't upgrade in place.
> To upgrade, delete all non-GA database instances. You can find the list of generally available 
> and preview services in the [Release Notes](./release-notes.md).

### Upgrade  

You will need to connect and authenticate to a Kubernetes cluster and have an existing Kubernetes context selected prior to beginning the upgrade of the Azure Arc data controller.

```kubectl
kubectl config use-context <Kubernetes cluster name>
```

You can perform a dry run first. The dry run validates the registry exists, the version schema, and the private repository authorization token (if used). To perform a dry run, use the `--dry-run` parameter in the `az arcdata dc upgrade` command. For example:

```azurecli
az arcdata dc upgrade --resource-group <resource group> --name <data controller name> --desired-version <version> [--no-wait]
```

The output for the preceding command is:

```output
Preparing to upgrade dc arcdc in namespace arc to version <version-tag>.
Preparing to upgrade dc arcdc in namespace arc to version <version-tag>.
****Dry Run****
Arcdata Control Plane would be upgraded to: <version-tag>
```

To upgrade the data controller, run the `az arcdata dc upgrade` command. If you don't specify a target image, the data controller will be upgraded to the latest version.

```azurecli
az arcdata dc upgrade --resource-group <resource group> --name <data controller name> [--no-wait]
```

In example above, you can include `--desired-version <version>` to specify a version if you do not want the latest version. 

## Monitor the upgrade status

You can monitor the progress of the upgrade with CLI.

### CLI

```azurecli
 az arcdata dc status show --resource-group <resource group>
```

The upgrade is a two-part process. First the controller is upgraded, then the monitoring stack is upgraded. When the upgrade is complete, the output will be:

```output
Ready
```

## Troubleshoot upgrade problems

If you encounter any troubles with upgrading, see the [troubleshooting guide](troubleshoot-guide.md).
