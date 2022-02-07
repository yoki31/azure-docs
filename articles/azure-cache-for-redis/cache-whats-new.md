---
title: What's New in Azure Cache for Redis
description: Recent updates for Azure Cache for Redis
author: flang-msft
ms.author: franlanglois
ms.service: cache
ms.topic: reference
ms.date: 02/02/2022

---

# What's New in Azure Cache for Redis

## February 2022

### Active geo-replication for Azure Cache For Redis Enterprise GA

Active geo-replication for Azure Cache for Redis Enterprise is now generally available (GA).

Active geo-replication is a powerful tool that enables Azure Cache for Redis clusters to be linked together for seamless active-active replication of data. Your applications can write to one Redis cluster and your data is automatically copied to the other linked clusters, and vice versa. For more information, see this [post](https://aka.ms/ActiveGeoGA) in the *Azure Developer Community Blog*.

## January 2022

### Support for managed identity in Azure Cache for Redis

Azure Cache for Redis now supports authenticating storage account connections using managed identity. Identity is established through Azure Active Directory, and both system-assigned and user-assigned identities are supported. This further allows the service to establish trusted access to storage for uses including data persistence and importing/exporting cache data.

For more information, see [Managed identity with Azure Cache for Redis (Preview)](cache-managed-identity.md).

## October 2021

### Azure Cache for Redis 6.0 GA

Azure Cache for Redis 6.0 is now generally available. The new version includes:

- Redis Streams, a new data type
- Performance enhancements
- Enhanced developer productivity
- Boosts security

You can now use an append-only data structure, Redis Streams, to ingest, manage, and make sense of data that is continuously being generated.

Additionally, Azure Cache for Redis 6.0 introduces new commands: `STRALGO`, `ZPOPMIN`, `ZPOPMAX`, and `HELP` for performance and ease of use.

Get started with Azure Cache for Redis 6.0, today, and select Redis 6.0 during cache creation. Also, you can upgrade your existing Redis 4.0 cache instances. For more information, see [Set Redis version for Azure Cache for Redis](cache-how-to-version.md).

### Diagnostics for connected clients

Azure Cache for Redis now integrates with Azure diagnostic settings to log information on all client connections to your cache. Logging and then analyzing this diagnostic setting helps you understand who is connecting to your caches and the timestamp of those connections. This data could be used to identify the scope of a security breach and for security auditing purposes. Users can route these logs to a destination of their choice, such as a storage account or Event Hub.

For more information, see [Monitor Azure Cache for Redis data using diagnostic settings](cache-monitor-diagnostic-settings.md).

### Azure Cache for Redis Enterprise update

Active geo-replication public preview now supports:

- RediSearch Module: Deploy RediSearch with active geo-replication
- Five caches in a replication group. Previously, supported two caches.
- OSS clustering policy - suitable for high-performance workloads and provides better scalability.

## October 2020

### Azure TLS Certificate Change

Microsoft is updating Azure services to use TLS server certificates from a different set of Certificate Authorities (CAs). This change is rolled out in phases from August 13, 2020 to October 26, 2020 (estimated). Azure is making this change because [the current CA certificates don't  one of the CA/Browser Forum Baseline requirements](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951). The problem was reported on July 1, 2020 and applies to multiple popular Public Key Infrastructure (PKI) providers worldwide. Most TLS certificates used by Azure services today come from the *Baltimore CyberTrust Root* PKI. The Azure Cache for Redis service will continue to be chained to the Baltimore CyberTrust Root. Its TLS server certificates, however, will be issued by new Intermediate Certificate Authorities (ICAs) starting on October 12, 2020.

> [!NOTE]
> This change is limited to services in public [Azure regions](https://azure.microsoft.com/global-infrastructure/geographies/). It excludes sovereign (e.g., China) or government clouds.
>
>

#### Does this change affect me?

We expect that most Azure Cache for Redis customers aren't affected by the change. Your application may be impacted if it explicitly specifies a list of acceptable certificates, a practice known as “certificate pinning”. If it's pinned to an intermediate or leaf certificate instead of the Baltimore CyberTrust Root, you should **take immediate actions** to change the certificate configuration.

The following table provides information about the certificates that are being rolled. Depending on which certificate your application uses, you may need to update it to prevent loss of connectivity to your Azure Cache for Redis instance.

| CA Type | Current | Post Rolling (Oct 12, 2020) | Action |
| ----- | ----- | ----- | ----- |
| Root | Thumbprint: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> Expiration: Monday, May 12, 2025, 4:59:00 PM<br><br> Subject Name:<br> CN = Baltimore CyberTrust Root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | Not changing | None |
| Intermediates | Thumbprints:<br> CN = Microsoft IT TLS CA 1<br> Thumbprint: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> Thumbprint: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> Thumbprint: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> Thumbprint: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> Expiration: ‎Friday, ‎May ‎20, ‎2024 5:52:38 AM<br><br> Subject Name:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | Thumbprints:<br> CN = Microsoft RSA TLS CA 01<br> Thumbprint: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> Thumbprint: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> Expiration: ‎Tuesday, ‎October ‎8, ‎2024 12:00:00 AM;<br><br> Subject Name:<br> O = Microsoft Corporation<br> C = US<br> | Required |

#### What actions should I take?

If your application uses the operating system certificate store or pins the Baltimore root among others, no action is needed.

If your application pins any intermediate or leaf TLS certificate, we recommend you pin the following roots:

| Certificate | Thumbprint |
| ----- | ----- |
| [Baltimore Root CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Digicert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> Both the intermediate and leaf certificates are expected to change frequently. We recommend not to take a dependency on them. Instead pin your application to a root certificate since it rolls less frequently.
>
>

To continue to pin intermediate certificates, add the following to the pinned intermediate certificates list, which includes few more to minimize future changes:

| Common name of the CA | Thumbprint |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS Issuing CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS Issuing CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS Issuing CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS Issuing CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

If your application validates certificate in code, you need to modify it to recognize the properties --- for example, Issuers, Thumbprint --- of the newly pinned certificates. This extra verification should cover all pinned certificates to be more future-proof.

## Next steps

If you have more questions, contact us through [support](https://azure.microsoft.com/support/options/).  
