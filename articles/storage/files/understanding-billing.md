---
title: Understand Azure Files billing | Microsoft Docs
description: Learn how to interpret the provisioned and pay-as-you-go billing models for Azure file shares.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2021
ms.author: rogarana
ms.subservice: files
---

# Understand Azure Files billing
Azure Files provides two distinct billing models: provisioned and pay-as-you-go. The provisioned model is only available for premium file shares, which are file shares deployed in the **FileStorage** storage account kind. The pay-as-you-go model is only available for standard file shares, which are file shares deployed in the **general purpose version 2 (GPv2)** storage account kind. This article explains how both models work in order to help you understand your monthly Azure Files bill.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/m5_-GsKv4-o" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        This video is an interview discussing covering the basics of the Azure Files billing model including how to optimize Azure file shares to achieve the lowest costs possible and how to compare Azure Files to other file storage offerings on-premises and in the cloud.
   :::column-end:::
:::row-end:::

For Azure Files pricing information, see [Azure Files pricing page](https://azure.microsoft.com/pricing/details/storage/files/).

## Applies to
| File share type | SMB | NFS |
|-|:-:|:-:|
| Standard file shares (GPv2), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Standard file shares (GPv2), GRS/GZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Premium file shares (FileStorage), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![Yes](../media/icons/yes-icon.png) |

## Storage units	
Azure Files uses base-2 units of measurement to represent storage capacity: KiB, MiB, GiB, and TiB. Your operating system may or may not use the same unit of measurement or counting system.

### Windows
Both the Windows operating system and Azure Files measure storage capacity using the base-2 counting system, but there is a difference when labeling units. Azure Files labels its storage capacity with base-2 units of measurement while Windows labels its storage capacity in base-10 units of measurement. When reporting storage capacity, Windows doesn't convert its storage capacity from base-2 to base-10.

| Acronym | Definition                         | Unit     | Windows displays as |
|---------|------------------------------------|----------|---------------------|
| KiB     | 1,024 bytes                        | kibibyte | KB (kilobyte)       |
| MiB     | 1,024 KiB (1,048,576 bytes)        | mebibyte | MB (megabyte)       |
| GiB     | 1024 MiB (1,073,741,824 bytes)     | gibibyte | GB (gigabyte)       |
| TiB     | 1024 GiB (1,099,511,627,776 bytes) | tebibyte | TB (terabyte)       |

### macOS
See [How iOS and macOS report storage capacity](https://support.apple.com/HT201402) on Apple's website to determine which counting system is used.

### Linux
A different counting system could be used by each operating system or individual piece of software. See their documentation to determine how they report storage capacity.

## Reserve capacity
Azure Files supports storage capacity reservations, which enable you to achieve a discount on storage by pre-committing to storage utilization. You should consider purchasing reserved instances for any production workload, or dev/test workloads with consistent footprints. When you purchase reserved capacity, your reservation must specify the following dimensions:

- **Capacity size**: Capacity reservations can be for either 10 TiB or 100 TiB, with more significant discounts for purchasing a higher capacity reservation. You can purchase multiple reservations, including reservations of different capacity sizes to meet your workload requirements. For example, if your production deployment has 120 TiB of file shares, you could purchase one 100 TiB reservation and two 10 TiB reservations to meet the total capacity requirements.
- **Term**: Reservations can be purchased for either a one year or three year term, with more significant discounts for purchasing a longer reservation term. 
- **Tier**: The tier of Azure Files for the capacity reservation. Reservations for Azure Files currently are available for the premium, hot, and cool tiers.
- **Location**: The Azure region for the capacity reservation. Capacity reservations are available in a subset of Azure regions.
- **Redundancy**: The storage redundancy for the capacity reservation. Reservations are supported for all redundancies Azure Files supports, including LRS, ZRS, GRS, and GZRS.

Once you purchase a capacity reservation, it will automatically be consumed by your existing storage utilization. If you use more storage than you have reserved, you will pay list price for the balance not covered by the capacity reservation. Transaction, bandwidth, data transfer, and metadata storage charges are not included in the reservation.

For more information on how to purchase storage reservations, see [Optimize costs for Azure Files with reserved capacity](files-reserve-capacity.md).

## Provisioned model
Azure Files uses a provisioned model for premium file shares. In a provisioned business model, you proactively specify to the Azure Files service what your storage requirements are, rather than being billed based on what you use. This is similar to buying hardware on-premises, in that when you provision an Azure file share with a certain amount of storage, you pay for that storage regardless of whether you use it or not, just like you don't start paying the costs of physical media on-premises when you start to use space. Unlike purchasing physical media on-premises, provisioned file shares can be dynamically scaled up or down depending on your storage and IO performance characteristics.

The provisioned size of the file share can be increased at any time but can be decreased only after 24 hours since the last increase. After waiting for 24 hours without a quota increase, you can decrease the share quota as many times as you like, until you increase it again. IOPS/throughput scale changes will be effective within a few minutes after the provisioned size change.

It is possible to decrease the size of your provisioned share below your used GiB. If you do this, you will not lose data but, you will still be billed for the size used and receive the performance (baseline IOPS, throughput, and burst IOPS) of the provisioned share, not the size used.

### Provisioning method
When you provision a premium file share, you specify how many GiBs your workload requires. Each GiB that you provision entitles you to additional IOPS and throughput on a fixed ratio. In addition to the baseline IOPS for which you are guaranteed, each premium file share supports bursting on a best effort basis. The formulas for IOPS and throughput are as follows:

| Item | Value |
|-|-|
| Minimum size of a file share | 100 GiB |
| Provisioning unit | 1 GiB |
| Baseline IOPS formula | `MIN(3000 + 1 * ProvisionedGiB, 100000)` |
| Burst limit | `MIN(MAX(10000, 3 * ProvisionedGiB), 100000)` |
| Burst credits | `(BurstLimit - BaselineIOPS) * 3600` |
| Throughput rate (ingress + egress) | `100 + CEILING(0.04 * ProvisionedGiB) + CEILING(0.06 * ProvisionedGiB)` |

The following table illustrates a few examples of these formulae for the provisioned share sizes:

| Capacity (GiB) | Baseline IOPS | Burst IOPS | Burst credits | Throughput (ingress + egress) (MiB/sec) |
|-|-|-|-|-|
| 100 | 3,100 | Up to 10,000 | 24,840,000 | 110 |
| 500 | 3,500 | Up to 10,000 | 23,400,000 | 150 |
| 1,024 | 4,024 | Up to 10,000 | 21,513,600 | 203 |
| 5,120 | 8,120 | Up to 15,360 | 26,064,000 | 613 |
| 10,240 | 13,240 | Up to 30,720 | 62,928,000 | 1,125 |
| 33,792 | 36,792 | Up to 100,000 | 227,548,800 | 3,480 |
| 51,200 | 54,200 | Up to 100,000 | 164,880,000 | 5,220 |
| 102,400 | 100,000 | Up to 100,000 | 0 | 10,340 |

Effective file share performance is subject to machine network limits, available network bandwidth, IO sizes, parallelism, among many other factors. For example, based on internal testing with 8 KiB read/write IO sizes, a single Windows virtual machine without SMB Multichannel enabled, *Standard F16s_v2*, connected to premium file share over SMB could achieve 20K read IOPS and 15K write IOPS. With 512 MiB read/write IO sizes, the same VM could achieve 1.1 GiB/s egress and 370 MiB/s ingress throughput. The same client can achieve up to \~3x performance if SMB Multichannel is enabled on the premium shares. To achieve maximum performance scale, [enable SMB Multichannel](files-smb-protocol.md#smb-multichannel) and spread the load across multiple VMs. Refer to [SMB Multichannel performance](storage-files-smb-multichannel-performance.md) and [troubleshooting guide](storage-troubleshooting-files-performance.md) for some common performance issues and workarounds.

### Bursting
If your workload needs the extra performance to meet peak demand, your share can use burst credits to go above share baseline IOPS limit to offer share performance it needs to meet the demand. Premium file shares can burst their IOPS up to 4,000 or up to a factor of three, whichever is a higher value. Bursting is automated and operates based on a credit system. Bursting works on a best effort basis and the burst limit is not a guarantee.

Credits accumulate in a burst bucket whenever traffic for your file share is below baseline IOPS. For example, a 100 GiB share has 500 baseline IOPS. If actual traffic on the share was 100 IOPS for a specific 1-second interval, then the 400 unused IOPS are credited to a burst bucket. Similarly, an idle 1 TiB share, accrues burst credit at 1,424 IOPS. These credits will then be used later when operations would exceed the baseline IOPS.

Whenever a share exceeds the baseline IOPS and has credits in a burst bucket, it will burst up to the max allowed peak burst rate. Shares can continue to burst as long as credits are remaining but, this is based on the number of burst credits accrued. Each IO beyond baseline IOPS consumes one credit and once all credits are consumed the share would return to the baseline IOPS.

Share credits have three states:

- Accruing, when the file share is using less than the baseline IOPS.
- Declining, when the file share is using more than the baseline IOPS and in the bursting mode.
- Constant, when the files share is using exactly the baseline IOPS, there are either no credits accrued or used.

New file shares start with the full number of credits in its burst bucket. Burst credits will not be accrued if the share IOPS fall below baseline IOPS due to throttling by the server.

## Pay-as-you-go model
Azure Files uses a pay-as-you-go business model for standard file shares. In a pay-as-you-go business model, the amount you pay is determined by how much you actually use, rather than based on a provisioned amount. At a high level, you pay a cost for the amount of logical data stored, and then an additional set of transactions based on your usage of that data. A pay-as-you-go model can be cost-efficient, because you don't need to overprovision to account for future growth or performance requirements or deprovision if your workload is data footprint varies over time. On the other hand, a pay-as-you-go model can also be difficult to plan as part of a budgeting process, because the pay-as-you-go billing model is driven by end-user consumption.

### Differences in standard tiers
When you create a standard file share, you pick between the transaction optimized, hot, and cool tiers. All three tiers are stored on the exact same standard storage hardware. The main difference for these three tiers is their data at-rest storage prices, which are lower in cooler tiers, and the transaction prices, which are higher in the cooler tiers. This means:

- Transaction optimized, as the name implies, optimizes the price for high transaction workloads. Transaction optimized has the highest data at-rest storage price, but the lowest transaction prices.
- Hot is for active workloads that do not involve a large number of transactions, and has a slightly lower data at-rest storage price, but slightly higher transaction prices as compared to transaction optimized. Think of it as the middle ground between the transaction optimized and cool tiers.
- Cool optimizes the price for workloads that do not have much activity, offering the lowest data at-rest storage price, but the highest transaction prices.

If you put an infrequently accessed workload in the transaction optimized tier, you will pay almost nothing for the few times in a month that you make transactions against your share, but you will pay a high amount for the data storage costs. If you were to move this same share to the cool tier, you would still pay almost nothing for the transaction costs, simply because you are very infrequently making transactions for this workload, but the cool tier has a much cheaper data storage price. Selecting the appropriate tier for your use case allows you to considerably reduce your costs.

Similarly, if you put a highly accessed workload in the cool tier, you will pay a lot more in transaction costs, but less for data storage costs. This can lead to a situation where the increased costs from the transaction prices increase outweigh the savings from the decreased data storage price, leading you to pay more money on cool than you would have on transaction optimized. It is possible for some usage levels that while the hot tier will be the most cost efficient tier, the cool tier will be more expensive than transaction optimized.

Your workload and activity level will determine the most cost efficient tier for your standard file share. In practice, the best way to pick the most cost efficient tier involves looking at the actual resource consumption of the share (data stored, write transactions, etc.).

### Logical size versus physical size
The data at-rest capacity charge for Azure Files is billed based on the logical size, often colloquially called "size" or "content length", of the file. The logical size of the file is distinct from the physical size of the file on disk, often called "size on disk" or "used size". The physical size of the file may be large or smaller than the logical size of the file.

### What are transactions?
Transactions are operations or requests against Azure Files to upload, download, or otherwise manipulate the contents of the file share. Every action taken on a file share translates to one or more transactions, and on standard shares that use the pay-as-you-go billing model, that translates to transaction costs.

There are five basic transaction categories: write, list, read, other, and delete. All operations done via the REST API or SMB are bucketed into one of these 4 categories as follows:

| Transaction bucket | Management operations | Data operations |
|-|-|-|
| Write transactions | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li><li>`SetShareACL`</li></ul> | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> |
| List transactions | <ul><li>`ListShares`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul> |
| Read transactions | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> |
| Other transactions | | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> |
| Delete transactions | <ul><li>`DeleteShare`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |  

> [!Note]  
> NFS 4.1 is only available for premium file shares, which use the provisioned billing model, transactions do not affect billing for premium file shares.

## Value-add services

### Azure File Sync
If you are thinking about using Azure File Sync, consider the following when evaluating cost:

#### Server fee
For each server that you have connected to a sync group, there is an additional $5 fee. This is independent of the number of server endpoints. For example, if you had one server that contained three different server endpoints, you would only have one $5 charge. One sync server is free per Storage Sync Service. 

#### Data cost
The cost of data at rest depends on the billing tier you choose. This is the cost of storing data in the Azure file share in the cloud including snapshot storage.  

#### Cloud enumeration scans cost
Azure File Sync enumerates the Azure File Share in the cloud once per day to discover changes that were made directly to the share so that they can sync down to the server endpoints. This scan generates transactions which are billed to the storage account at a rate of two LIST transactions per directory per day. You can put this number into the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate the scan cost.  

> [!Tip]  
> If you don't know how many folders you have, check out the TreeSize tool from JAM Software GmbH.

#### Churn and tiering costs
As files change on server endpoints, the changes are uploaded to the cloud share, which generates transactions. When cloud tiering is enabled, additional transactions are generated for managing tiered files, including I/O happening on tiered files, in addition to egress costs. The quantity and type of transactions is difficult to predict due to churn rates and cache efficiency, but you can use your previous transaction patterns to predict future costs if you only have one file share in your storage account. See [Choosing a billing tier](#choosing-a-billing-tier) for details on how to view previous transactions.  

#### Choosing a billing tier
For Azure File Sync customers, we recommend choosing standard file shares over premium file shares. This is because with Azure File Sync, customers get that low latency on-premises that they always had, so the higher performance provided by premium file shares isn’t necessary. When first migrating to Azure Files via Azure File Sync, we recommend the Transaction Optimized tier due to the large number of transactions incurred during migration. Once migration is complete, you can plug in your previous transactions into the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to figure out which tier is best suited for your workload. 

To see previous transactions:
1. Go to your storage account and select **Metrics** in the left navigation bar.
2. Select **Scope** as your storage account name, **Metric Namespace** as "File", **Metric** as "Transactions", and **Aggregation** as "Sum".
3. Select **Apply Splitting**.
4. Select **Values** as "API Name". Select your desired **Limit** and **Sort**.
5. Select your desired time period.

> [!Note]  
> Make sure you view transactions over a period of time to get a better idea of average number of transactions. Ensure that the chosen time period does not overlap with initial provisioning. Multiply the average number of transactions during this time period to get the estimated transactions for an entire month.

## File storage comparison checklist
To correctly evaluate the cost of Azure Files compared to other file storage options, consider the following questions:

- **How do you pay for storage, IOPS, and bandwidth?**  
    With Azure Files, the billing model you use depends on whether you are deploying [premium](#provisioned-model) or [standard](#pay-as-you-go-model) file shares. Most cloud solutions have models that align with the principles of either provisioned storage (price determinism, simplicity) or pay-as-you-go storage (pay only for you actually use). Of particular interest for provisioned models is minimum provisioned share size, the provisioning unit, and the ability to increase and decrease the provisioning. 

- **How do you achieve storage resiliency and redundancy?**  
    With Azure Files, storage resiliency and redundancy are baked into the product offering. All tiers and redundancy levels ensure that data is highly available and at least three copies of your data are accessible. When considering other file storage options, consider whether storage resiliency and redundancy is built-in or something you must assemble yourself. 

- **What do you need to manage?**  
    With Azure Files, the basic unit of management is a storage account. Other solutions may require additional management, such as operating system updates or virtual resource management (VMs, disks, network IP addresses, etc.).

- **What are the backup costs?**  
    With Azure Files, Azure Backup integration is easily enabled and is backup storage is billed as part of the cost share (backups are stored as differential snapshots). Other solutions may require backup software licensing and additional backup storage costs.

## See also
- [Azure Files pricing page](https://azure.microsoft.com/pricing/details/storage/files/).
- [Planning for an Azure Files deployment](storage-files-planning.md) and [Planning for an Azure File Sync deployment](../file-sync/file-sync-planning.md).
- [Create a file share](storage-how-to-create-file-share.md) and [Deploy Azure File Sync](../file-sync/file-sync-deployment-guide.md).
