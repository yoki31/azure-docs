---
title: DCsv3 and DCdsv3-series - Azure Virtual Machines
description: Specifications for the DCsv3 and DCdsv3-series VMs.
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: mmcrey
ms.custom: ignite-fall-2021
---

# DCsv3 and DCdsv3-series

**Applies to:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Flexible scale sets :heavy_check_mark: Uniform scale sets

> [!IMPORTANT] 
> DCsv3 and DCdsv3 are in public preview as of November 1st, 2021.

The DCsv3 and DCdsv3-series virtual machines help protect the confidentiality and integrity of your code and data whilst it’s processed in the public cloud. By leveraging Intel® Software Guard Extensions and Intel® Total Memory Encryption - Multi Key, customers can ensure their data is always encrypted and protected in use. 

These machines are powered by the latest 3rd Generation Intel® Xeon Scalable processors, and leverage Intel® Turbo Boost Max Technology 3.0 to reach 3.5 GHz. 

With this generation, CPU Cores have increased 6x (up to a maximum of 48 physical cores), Encrypted Memory (EPC) has increased 1500x to 256GB, Regular Memory has increased 12x to 384GB. All these changes substantially improve the performance gen-on-gen and unlock new entirely new scenarios. 

> [!NOTE]
> Hyperthreading is disabled for added security posture. Pricing is the same as Dv5 and Dsv5-series per physical core.

We are offering two variants dependent on whether the workload benefits from a local disk or not. Whether you choose a VM with a local disk or not, you can attach remote persistent disk storage to all VMs. Remote disk options (such as for the VM boot disk) are billed separately from the VMs in any case, as always. 

## Configuration

CPU: 3rd Generation Intel® Xeon Scalable Processor 8370C<br>
Base All-Core Frequency: 2.8 GHz<br>
[Turbo Boost Max 3.0](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html): Enabled, Max Frequency 3.5 GHz<br>
[Hyper-Threading](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html): Not Supported<br>
[Total Memory Encryption - Multi Key](https://itpeernetwork.intel.com/memory-encryption/): Enabled<br>
[Premium Storage](premium-storage-performance.md): Supported<br>
[Ultra-Disk Storage](disks-enable-ultra-ssd.md): Supported<br>
[Accelerated Networking](../virtual-network/create-vm-accelerated-networking-cli.md): Supported<br>
[Azure Kubernetes Service](../aks/intro-kubernetes.md): Supported (CLI provisioning only initially)<br>
[Live Migration](maintenance-and-updates.md): Not Supported<br>
[Memory Preserving Updates](maintenance-and-updates.md): Not Supported<br>
[VM Generation Support](generation-2.md): Generation 2<br>
[Ephemeral OS Disks](ephemeral-os-disks.md): Supported <br>
[Dedicated Host](dedicated-hosts.md): Coming Soon<br>
[Nested Virtualization](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Not Supported <br>

## DCsv3-series Technical specifications

| Size             | Physical Cores | Memory GB | Temp storage (SSD) GiB | Max data disks | Max NICs |  EPC Memory GB |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1s_v3 | 1              | 8           | N/A                    | 4              | 2     |  4                 |
| Standard_DC2s_v3 | 2              | 16          | N/A                    | 8              | 2     |  8                 |
| Standard_DC4s_v3 | 4              | 32          | N/A                    | 16             | 4     |  16                |
| Standard_DC8s_v3 | 8              | 64          | N/A                    | 32             | 8     |  32                |
| Standard_DC16s_v3  | 16           | 128         | N/A                    | 32             | 8     |  64                |
| Standard_DC24s_v3  | 24           | 192         | N/A                    | 32             | 8     |  128               |
| Standard_DC32s_v3  | 32           | 256         | N/A                    | 32             | 8     |  192               |
| Standard_DC48s_v3  | 48           | 384         | N/A                    | 32             | 8     |  256               |

## DCdsv3-series Technical specifications

| Size             | Physical Cores | Memory GB | Temp storage (SSD) GiB | Max data disks | Max NICs |  EPC Memory GB |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1ds_v3 | 1              | 8           | 75                    | 4              | 2     |  4                 |
| Standard_DC2ds_v3 | 2              | 16          | 150                    | 8              | 2     |  8                 |
| Standard_DC4ds_v3 | 4              | 32          | 300                    | 16             | 4     |  16                |
| Standard_DC8ds_v3 | 8              | 64          | 600                    | 32             | 8     |  32                |
| Standard_DC16ds_v3  | 16           | 128         | 1200                    | 32             | 8     |  64                |
| Standard_DC24ds_v3  | 24           | 192         | 1800                    | 32             | 8     |  128               |
| Standard_DC32ds_v3  | 32           | 256         | 2400                    | 32             | 8     |  192               |
| Standard_DC48ds_v3  | 48           | 384         | 2400                    | 32             | 8     |  256               |

## Get started

- Create DCsv3 and DCdsv3 VMs using the [Azure portal](./linux/quick-create-portal.md)
- DCsv3 and DCdsv3 VMs are [Generation 2 VMs](./generation-2.md#creating-a-generation-2-vm) and only support `Gen2` images.
- Currently available in the regions listed in [Azure Products by Region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## More sizes and information

- [General purpose](sizes-general.md)
- [Memory optimized](sizes-memory.md)
- [Storage optimized](sizes-storage.md)
- [GPU optimized](sizes-gpu.md)
- [High performance compute](sizes-hpc.md)
- [Previous generations](sizes-previous-gen.md)
- [Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)

Pricing Calculator : [Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)

Learn more about how [Azure compute units (ACU)](acu.md) can help you compare compute performance across Azure SKUs.
