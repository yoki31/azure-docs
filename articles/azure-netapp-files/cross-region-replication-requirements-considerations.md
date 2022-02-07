---
title: Requirements and considerations for Azure NetApp Files cross-region replication | Microsoft Docs
description: Describes the requirements and considerations for using the volume cross-region replication functionality of Azure NetApp Files.  
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/21/2022
ms.author: anfdocs
---

# Requirements and considerations for using cross-region replication 

This article describes requirements and considerations about [using the volume cross-region replication](cross-region-replication-create-peering.md) functionality of Azure NetApp Files.

## Requirements and considerations 

* Azure NetApp Files replication is only available in certain fixed region pairs. See [Supported region pairs](cross-region-replication-introduction.md#supported-region-pairs). 
* SMB volumes are supported along with NFS volumes. Replication of SMB volumes requires an Active Directory connection in the source and destination NetApp accounts. The destination AD connection must have access to the DNS servers or ADDS Domain Controllers that are reachable from the delegated subnet in the destination region. For more information, see [Requirements for Active Directory connections](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* The destination account must be in a different region from the source volume region. You can also select an existing NetApp account in a different region.  
* The replication destination volume is read-only until you [fail over to the destination region](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume) to enable the destination volume for read and write. 
* Azure NetApp Files replication does not currently support multiple subscriptions; all replications must be performed under a single subscription.
* See [resource limits](azure-netapp-files-resource-limits.md) for the maximum number of cross-region replication destination volumes. You can open a support ticket to [request a limit increase](azure-netapp-files-resource-limits.md#request-limit-increase) in the default quota of replication destination volumes (per subscription in a region).
* There can be a delay up to five minutes for the interface to reflect a newly added snapshot on the source volume.  
* Cascading and fan in/out topologies are not supported.
* Configuring volume replication for source volumes created from snapshot is not supported at this time.
* After you set up cross-region replication, the replication process creates *snapmirror snapshots* to provide references between the source volume and the destination volume. Snapmirror snapshots are cycled automatically when a new one is created for every incremental transfer. You cannot delete snapmirror snapshots until replication relationship and volume is deleted. 
* You cannot mount a dual-protocol volume until you [authorize replication from the source volume](cross-region-replication-create-peering.md#authorize-replication-from-the-source-volume) and the initial [transfer](cross-region-replication-display-health-status.md#display-replication-status) happens.
* You can delete manual snapshots on the source volume of a replication relationship when the replication relationship is active or broken, and also after the replication relationship is deleted. You cannot delete manual snapshots for the destination volume until the replication relationship is broken.
* You cannot revert a source or destination volume of cross-region replication to a snapshot. The snapshot revert functionality is greyed out for volumes in a replication relationship. 

## Next steps
* [Create volume replication](cross-region-replication-create-peering.md)
* [Display health status of replication relationship](cross-region-replication-display-health-status.md)
* [Manage disaster recovery](cross-region-replication-manage-disaster-recovery.md)
* [Volume replication metrics](azure-netapp-files-metrics.md#replication)
* [Delete volume replications or volumes](cross-region-replication-delete.md)
* [Troubleshoot cross-region replication](troubleshoot-cross-region-replication.md)
