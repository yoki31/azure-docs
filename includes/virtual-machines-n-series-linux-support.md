---
 title: include file
 description: include file
 services: virtual-machines-linux
 author: cynthn
 ms.service: virtual-machines-linux
 ms.topic: include
 ms.date: 11/15/2021
 ms.author: cynthn
 ms.custom: include file
---

## Supported distributions and drivers

### NVIDIA CUDA drivers

NVIDIA CUDA drivers for NC, NCv2, NCv3, ND, and NDv2-series VMs (optional for NV-series) are supported only on the Linux distributions listed in the following table. For the latest CUDA drivers and supported operating systems, visit the [NVIDIA](https://developer.nvidia.com/cuda-zone) website. Ensure that you install or upgrade to the latest supported CUDA drivers for your distribution. 

> [!NOTE]
> The latest supported CUDA drivers for NC-series VMs is currently 470.82.01. Later driver versions are not supported on the K80 cards in NC.
> 

> [!TIP]
> As an alternative to manual CUDA driver installation on a Linux VM, you can deploy an Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) image. The DSVM editions for Ubuntu 16.04 LTS or CentOS 7.4 pre-install NVIDIA CUDA drivers, the CUDA Deep Neural Network Library, and other tools.


### NVIDIA GRID drivers

Microsoft redistributes NVIDIA GRID driver installers for NV and NVv3-series VMs used as virtual workstations or for virtual applications. Install only these GRID drivers on Azure NV VMs, only on the operating systems listed in the following table. These drivers include licensing for GRID Virtual GPU Software in Azure. You do not need to set up a NVIDIA vGPU software license server.

The GRID drivers redistributed by Azure do not work on most non-NV series VMs like NC, NCv2, NCv3, ND, and NDv2-series VMs but works on NCasT4v3 series.

|Distribution|Driver|
| --- | -- |
|Ubuntu 16.04 LTS, 18.04 LTS, 20.04 LTS<br/><br/>Red Hat Enterprise Linux 7.7, 7.8, 7.9<br/><br/>CentOS 7.6, 7.7<br/><br/>SUSE Linux Enterprise Server 12 SP2+, 15 SP2 | NVIDIA GRID 13.1, driver branch [R470](https://go.microsoft.com/fwlink/?linkid=874272)(.exe)|

Visit [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) for the complete list of all previous Nvidia GRID driver links.

> [!WARNING] 
> Installation of third-party software on Red Hat products can affect the Red Hat support terms. See the [Red Hat Knowledgebase article](https://access.redhat.com/articles/1067).
>
