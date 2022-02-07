---
title: Troubleshoot Azure Cache for Redis server issues
description: Learn how to resolve common server issues, such as memory pressure, high CPU, long running commands, or bandwidth limitations, when using Azure Cache for Redis.
author: flang-msft
ms.author: franlanglois
ms.service: cache
ms.topic: conceptual
ms.date: 02/02/2022

---

# Troubleshoot Azure Cache for Redis server issues

This section discusses troubleshooting issues caused by conditions on an Azure Cache for Redis server or any of the virtual machines hosting it.

- [High server load](#high-server-load)
  - [Scale up or scale out](#scale-up-or-scale-out)
  - [Rapid changes in number of client connections](#rapid-changes-in-number-of-client-connections)
  - [Long running or expensive commands](#long-running-or-expensive-commands)
  - [Scaling](#scaling)
  - [Server maintenance](#server-maintenance)
- [High memory usage](#high-memory-usage)
- [Long-running commands](#long-running-commands)
- [Server-side bandwidth limitation](#server-side-bandwidth-limitation)

> [!NOTE]
> Several of the troubleshooting steps in this guide include instructions to run Redis commands and monitor various performance metrics. For more information and instructions, see the articles in the [Additional information](#additional-information) section.
>

## High server load

High server load means the Redis server is busy and unable to keep up with requests, leading to timeouts. Check the *Server Load* metric on your cache by selecting **Monitoring** from the Resource menu on the left. You see the **Server Load** graph in the working pane under **Insights**. Or, add a metric set to *Server Load* under **Metrics**.

Following are some options to consider for high server load.

### Scale up or scale out

Scale out to add more shards, so that load is distributed across multiple Redis processes. Also, consider scaling up to a larger cache size with more CPU cores. For more information, see [Azure Cache for Redis planning FAQs](cache-planning-faq.yml).

### Rapid changes in number of client connections

For more information, see [Avoid client connection spikes](cache-best-practices-connection.md#avoid-client-connection-spikes).

### Long running or expensive commands

This section was moved. For more information, see [Long running commands](cache-troubleshoot-timeouts.md#long-running-commands).

### Scaling

Scaling operations are CPU and memory intensive as it could involve moving data around nodes and changing cluster topology. For more information, see [Scaling](cache-best-practices-scale.md).

### Server maintenance

If your Azure Cache for Redis underwent a failover, all client connections from the node that went down are transferred to the node that is still running. The server load could spike because of the increased connections. You can try rebooting your client applications so that all the client connections get recreated and redistributed among the two nodes.

## High memory usage

Memory pressure on the server can lead to various performance problems that delay processing of requests. When memory pressure hits, the system pages data to disk, which causes the system to slow down significantly.

Several possible can cause this memory pressure:

- The cache is filled with data near its maximum capacity.
- Redis server is seeing high memory fragmentation. Fragmentation is most often caused by storing large objects. Redis is optimized for small objects. If the `used_memory_rss` value is higher than the `used_memory` metric, it means part of Redis memory has been swapped off by the operating system, and you can expect some significant latencies. Because Redis server does not have control over how its allocations are mapped to memory pages, high `used_memory_rss` is often the result of a spike in memory usage.

Redis exposes two stats through the [INFO](https://redis.io/commands/info) command that can help you identify this issue: "used_memory" and "used_memory_rss". You can [view these metrics](cache-how-to-monitor.md#view-metrics-with-azure-monitor-metrics-explorer) using the portal.

Validate that the `maxmemory-reserved` and `maxfragmentationmemory-reserved` values are set appropriately.

There are several possible changes you can make to help keep memory usage healthy:

- [Configure a memory policy](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) and set expiration times on your keys. This policy may not be sufficient if you have fragmentation.
- [Configure a maxmemory-reserved value](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) that is large enough to compensate for memory fragmentation.
- [Create alerts](cache-how-to-monitor.md#alerts) on metrics like used memory to be notified early about potential impacts.
- [Scale](cache-how-to-scale.md) to a larger cache size with more memory capacity. For more information, see [Azure Cache for Redis planning FAQs](./cache-planning-faq.yml).

For recommendations on memory management, see [Best practices for memory management](cache-best-practices-memory-management.md).

## Long-running commands

This section was moved. For more information, see [Long running commands](cache-troubleshoot-timeouts.md#long-running-commands).

## Server-side bandwidth limitation

This section was moved. For more information, see [Network bandwidth limitation](cache-troubleshoot-timeouts.md#network-bandwidth-limitation).

## Additional information

- [Troubleshoot Azure Cache for Redis client-side issues](cache-troubleshoot-client.md)
- [Choosing the right tier](cache-overview.md#choosing-the-right-tier)
- [How can I benchmark and test the performance of my cache?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
- [How to monitor Azure Cache for Redis](cache-how-to-monitor.md)
- [How can I run Redis commands?](cache-development-faq.yml#how-can-i-run-redis-commands-)
