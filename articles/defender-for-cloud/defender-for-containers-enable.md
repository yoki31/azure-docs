---
title: How to enable Microsoft Defender for Containers in Microsoft Defender for Cloud
description: Enable the container protections of Microsoft Defender for Containers
ms.topic: overview
zone_pivot_groups: k8s-host
ms.date: 01/25/2022
---
# Enable Microsoft Defender for Containers

Microsoft Defender for Containers is the cloud-native solution for securing your containers.

Defender for Containers protects your clusters whether they're running in:

- **Azure Kubernetes Service (AKS)** - Microsoft's managed service for developing, deploying, and managing containerized applications.

- **Amazon Elastic Kubernetes Service (EKS) in a connected Amazon Web Services (AWS) account** - Amazon's managed service for running Kubernetes on AWS without needing to install, operate, and maintain your own Kubernetes control plane or nodes.

- **An unmanaged Kubernetes distribution** (using Azure Arc-enabled Kubernetes) - Cloud Native Computing Foundation (CNCF) certified Kubernetes clusters hosted on-premises or on IaaS.

Learn about this plan in [Overview of Microsoft Defender for Containers](defender-for-containers-introduction.md).

::: zone pivot="defender-for-container-arc,defender-for-container-eks"
> [!NOTE]
> Defender for Containers' support for Arc-enabled Kubernetes clusters (and therefore AWS EKS too) is a preview feature.
> 
> [!INCLUDE [Legalese](../../includes/defender-for-cloud-preview-legal-text.md)]
::: zone-end

::: zone pivot="defender-for-container-aks"
[!INCLUDE [Prerequisites](./includes/defender-for-container-prerequisites-aks.md)]
::: zone-end

::: zone pivot="defender-for-container-arc,defender-for-container-eks"
[!INCLUDE [Prerequisites](./includes/defender-for-container-prerequisites-arc-eks.md)]
::: zone-end

::: zone pivot="defender-for-container-aks"
[!INCLUDE [Enable plan for AKS](./includes/defender-for-containers-enable-plan-aks.md)]
::: zone-end

::: zone pivot="defender-for-container-arc"
[!INCLUDE [Enable plan for Arc](./includes/defender-for-containers-enable-plan-arc.md)]
::: zone-end

::: zone pivot="defender-for-container-eks"
[!INCLUDE [Enable plan for EKS](./includes/defender-for-containers-enable-plan-eks.md)]
::: zone-end


## Simulate security alerts from Microsoft Defender for Containers

A full list of supported alerts is available in the [reference table of all Defender for Cloud security alerts](alerts-reference.md#alerts-k8scluster).

1. To simulate a security alert, run the following command from the cluster:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    The expected response is "No resource found".

    Within 30 minutes, Defender for Cloud will detect this activity and trigger a security alert.

1. In the Azure portal, open Microsoft Defender for Cloud's security alerts page and look for the alert on the relevant resource:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Sample alert from Microsoft Defender for Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::
 
::: zone pivot="defender-for-container-arc,defender-for-container-eks"
[!INCLUDE [Remove the profile](./includes/defender-for-containers-remove-extension.md)]
::: zone-end

::: zone pivot="defender-for-container-aks"
[!INCLUDE [Remove the extension](./includes/defender-for-containers-remove-profile.md)]
::: zone-end
