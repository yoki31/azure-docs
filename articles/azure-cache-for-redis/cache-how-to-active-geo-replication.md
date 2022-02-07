---
title: Configure active geo-replication for Enterprise Azure Cache for Redis instances
description: Learn how to replicate your Azure Cache for Redis Enterprise instances across Azure regions
author: flang-msft
ms.service: cache
ms.topic: conceptual
ms.date: 02/02/2022
ms.author: franlanglois
---
# Configure active geo-replication for Enterprise Azure Cache for Redis instances

In this article, you'll learn how to configure an active geo-replicated Azure Cache using the Azure portal.

Active geo-replication groups up to five Enterprise Azure Cache for Redis instances into a single cache that spans across Azure regions. All instances act as the local primaries. An application decides which instance or instances to use for read and write requests.

> [!NOTE]
> Data transfer between Azure regions is charged at standard [bandwidth rates](https://azure.microsoft.com/pricing/details/bandwidth/).

## Create or join an active geo-replication group

> [!IMPORTANT]
> Active geo-replication must be enabled at the time an Azure Cache for Redis is created.
>
>

1. In the **Advanced** tab of **New Redis Cache** creation UI, select **Enterprise** for **Clustering Policy**.

    For more information on choosing **Clustering policy**, see [Clustering Policy](quickstart-create-redis-enterprise.md#clustering-policy).

    :::image type="content" source="media/cache-how-to-active-geo-replication/cache-clustering-policy.png" alt-text="Configure active geo-replication":::

1. Select **Configure** to set up **Active geo-replication**.

1. Create a new replication group, for a first cache instance, or select an existing one from the list.

    :::image type="content" source="media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png" alt-text="Link caches":::

1. Select **Configure** to finish.

    :::image type="content" source="media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png" alt-text="Active geo-replication configured":::

1. Wait for the first cache to be created successfully. Repeat the above steps for each additional cache instance in the geo-replication group.

## Remove from an active geo-replication group

To remove a cache instance from an active geo-replication group, you just delete the instance. The remaining instances will reconfigure themselves automatically.

## Next steps

Learn more about Azure Cache for Redis features.

* [Azure Cache for Redis service tiers](cache-overview.md#service-tiers)
* [High availability for Azure Cache for Redis](cache-high-availability.md)
