---
title: Compare Azure Database for PostgreSQL - Single Server and Flexible Server
description: Detailed comparison of features and capabilities between Azure Database for PostgreSQL Single Server and Flexible Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/08/2021
---

# Comparison chart - Azure Database for PostgreSQL Single Server and Flexible Server



The following table provides a high-level features and capabilities comparisons between Single Server and Flexible Server.

| **Feature / Capability** | **Single Server** | **Flexible Server** |
| ---- | ---- | ---- |
| **General**  | | |
| General availability | GA since 2018 | GA since 2021|
| PostgreSQL | Community | Community |
| Supported versions | 10, 11 | 11, 12, 13 |
| Underlying O/S | Windows | Linux  |
| AZ selection for application colocation | No | Yes |
| Built-in connection pooler | No | Yes (PgBouncer)|
| Uptime SLA | [SLA](https://azure.microsoft.com/support/legal/sla/postgresql/v1_1/)| [SLA](https://azure.microsoft.com/support/legal/sla/postgresql/v1_2/) |
| **Connectivity** | | |
| Username in connection string | `<user_name>@server_name`. For example, `pgadmusr@mypgServer` | Just username. For example, `pgadmusr` | 
| lc_collate  | English_United States.1252 | en_US.utf8 |
| lc_ctype    | English_United States.1252 |en_US.utf8 |
|lc_messages | English_United States.1252 |en_US.utf8|
| lc_monetary | English_United States.1252 |  en_US.utf-8 |
| lc_numeric  | English_United States.1252 |  en_US.utf-8 |
| lc_time     | English_United States.1252 | en_US.utf8 |
| Connection port | 5432 | 5432 (DB), 6432 (PgBouncer) |
| Max. connections | 1982 | 5000 |
| Connections limit configurable? | No | Yes (`max_connections` parameter) |
| **Compute & Storage** | | |
| Compute tiers | Basic, General Purpose, Memory Optimized | Burstable, General Purpose, Memory Optimized |
| Burstable SKUs | No | Yes |
| Ability to scale across compute tiers | Cannot scale Basic tier | Yes. Can scale across tiers |
| Stop/Start | No | Yes (for all compute SKUs). Only compute is stopped/started |
| Max. Storage size | 1 TB (Basic), 4 TB or 16 TB (GP,MO). Note: Not all regions support 16 TB. | 16 TB |
| Min storage size | 5 GB (Basic), 100 GB (GP, MO) | 32 GB |
| Storage auto-grow | Yes (1 GB increments) | No |
| Max IOPS | Basic - Variable. GP/MO: up to 20K  | Up to 20K |
| **Networking/Security** | | |
| Supported networking | Virtual network, private link, public access | Private access (VNET injection in a delegated subnet), public access) |
| Public access control | Firewall | Firewall |
| Private link support | Yes |No|
| Private DNS Zone support | No | Yes |
| Ability to move between private and public access | No | No |
| TLS support | TLS 1.2 | TLS 1.2, 1.3 enforced|
| Can turn off SSL | Yes | No |
| SCRAM authentication | No | Yes |
| **High Availability** | | |
| Zone-redundant HA | No | Yes (a synchronous standby is established on another zone within a region) |
| HA Configuration | Built-in with storage pinned to a zone. Compute can float across regions. | Physically separate compute & storage provisioned across two zones |
| Cost | 1x | 2x (compute + storage) |
| Availability with non-HA configuration | Automatic restart, compute relocation | Automatic restart, compute relocation)
| Protect from zone failure | Compute - Yes. Storage - No | Compute & storage - Yes |
| Protect from region failure | No | No |
| Mode of HA replication | N/A | Postgres physical streaming replication in SYNC mode)
| Standby can be used for read purposes | N/A | No |
| Application performance impact | No (not replicating) | Yes (Due to sync replication. Depends on the workload) |
| Automatic failover | Yes (spins another server)| Yes |
| Application connection string post failover | No change | No change |
| **Logical Replication** | | |
| Support for logical decoding | Yes | Yes |
| Support for native logical replication | No | Yes |
| Support for PgLogical extension | No | Yes |
| Support logical replication with HA | N/A | Limited |
| **Disaster Recovery** | | |
| Cross region DR | Using read replicas, geo-redundant backup | N/A |
| DR using replica | Using async physical replication | N/A |
| Automatic failover | No | N/A |
| Can use the same r/w endpoint | No | N/A |
| **Backup and Recovery** | | |
| Automated backups | Yes | Yes |
| Backup retention | 7-35 days | 7-35 days |
| PITR capability to any time within the retention period | Yes | Yes
| Ability to restore on a different zone | N/A | Yes |
| Ability to restore to a different VNET | No | Yes |
| Ability to restore to a different region | Yes (Geo-redundant) | Yes (in Preview in [selected regions](overview.md#azure-regions)) |
| Ability to restore a deleted server | Limited via API | Limited via support ticket |
| **Read Replica** | | |
| Support for read replicas | Yes | No |
| Number of read replicas | 5 | N/A |
| Mode of replication | Async | N/A |
| Cross-region support | Yes | N/A |
| **Maintenance Window** | | |
| System scheduled window | Yes | Yes |
| Customer scheduled window| No | Yes (can choose any 1hr on any day) |
| Notice period | 3 days | 5 days |
| Maintenance period | Anytime within 15 hrs window | 1hr window | 
| **Metrics** | | |
| Errors | Failed connections | Failed connections|
| Latency | Max lag across replicas, Replica lag | N/A |
| Saturation | Backup storage used, CPU %, IO %, Memory %, Server log storage limit, server log storage %, server log storage used, Storage limit, Storage %, Storage used | Backup storage used, CPU credits consumed, CPU credits remaining, CPU %, Disk queue depth, IOPS, Memory %, Read IOPS, Read throughput bytes/s, storage free, storage %, storage used, Transaction log storage used, Write IOPS, Write throughput bytes/s |
| Traffic | Active connections, Network In, Network out | Active connections, Max. used transaction ID, Network In, Network Out, succeeded connections |
| **Extensions** | | (offers latest versions)|
| TimescaleDB, orafce, plv8 | Yes | No |
| PgCron, lo, pglogical | No | Yes |
| pgAudit | Preview | Yes |
| **Security** | | |
| Azure Active Directory Support (AAD) | Yes | No |
| Customer managed encryption key (BYOK) | Yes | No |
| SCRAM Authentication (SHA-256) | No | Yes |
| Secure Sockets Layer support (SSL) | Yes | Yes |
| **Other features** | | |
| Alerts | Yes | Yes |
| Microsoft Defender for Cloud | Yes | No |
| Resource health | Yes | No |
| Service health | Yes | Yes |
| Performance insights (iPerf) | Yes | Yes |
| Major version upgrades support | No | No |
| Minor version upgrades | Yes. Automatic during maintenance window | Yes. Automatic during maintenance window |


## Next steps

- Understand [what’s available for compute and storage options - Flexible server](concepts-compute-storage.md)
- Learn about [supported PostgreSQL Database Versions in Flexible Server](concepts-supported-versions.md)
- Learn about [current limitations in Flexible Server](concepts-limits.md)
