---
title: Use TLS/SSL certificates in your application in Azure Spring Cloud
titleSuffix: Azure Spring Cloud
description: Use TLS/SSL certificates in an application.
author: KarlErickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/08/2021
ms.custom: devx-track-java
---

# Use TLS/SSL certificates in your application in Azure Spring Cloud

This article shows you how to use public certificates in Azure Spring Cloud for your application. Your app may act as a client and access an external service that requires certificate authentication, or it may need to perform cryptographic tasks.  

When you let Azure Spring Cloud manage your TLS/SSL certificates, you can maintain the certificates and your application code separately to safeguard your sensitive data. Your app code can access the public certificates you add to your Azure Spring Cloud instance.

> [!NOTE]
> Azure CLI and Terraform support and samples will be coming soon to this article.

## Prerequisites

- An application deployed to Azure Spring Cloud. See [Quickstart: Deploy your first application in Azure Spring Cloud](./quickstart.md), or use an existing app.
- Either a certificate file with *.crt*, *.cer*, *.pem*, or *.der* extension, or a deployed instance of Azure Key Vault with a private certificate.

## Import a certificate

You can choose to import your certificate into your Azure Spring Cloud instance from either Key Vault or use a local certificate file.

### Import a certificate from Key Vault

You need to grant Azure Spring Cloud access to your key vault before you import your certificate using these steps:

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Select **Key vaults**, then select the Key Vault you'll import your certificate from.
1. In the left navigation pane, select **Access policies**, then select **+ Add Access Policies**.
1. Select **Certificate permissions**, then select **Get** and **List**.
1. Under **Select Principal**, select **None selected**, then select your security principal.
1. Select **Select**, then select **Add**.

:::image type="content" source="media/use-tls-certificates/grant-key-vault-permission.png" alt-text="Screenshot of where to grant access to key vault before importing a certificate" lightbox="media/use-tls-certificates/grant-key-vault-permission.png":::

Once you grant access to your key vault, you can import your certificate using these steps:

1. Go to your service instance.
1. From the left navigation pane of your instance, select **TLS/SSL settings**.
1. Select **Import Key Vault Certificate** in the **Public Key Certificates** section.
1. Select your Key Vault in **Key vault** and the certificate in **Certificate**, then **Select** and **Apply**.
1. When you have successfully imported your certificate, you'll see it in the list of Public Key Certificates.

### Import a local certificate file

You can import a certificate file stored locally using these steps:

1. Go to your service instance.
1. From the left navigation pane of your instance, select **TLS/SSL settings**.
1. Select **Upload public certificate** in the **Public Key Certificates** section.
1. When you've successfully imported your certificate, you'll see it in the list of Public Key Certificates.

## Load a certificate

To load a certificate into your application in Azure Spring Cloud, start with these steps:

1. Go to your application instance.
1. From the left navigation pane of your app, select **Certificate management**.
1. Select **Add certificate** to choose certificates accessible for the app.

:::image type="content" source="media/use-tls-certificates/load-certificate.png" alt-text="Screenshot of where to load a certificate into your application" lightbox="media/use-tls-certificates/load-certificate.png":::

### Load a certificate from code

Your loaded certificates are available in the */etc/azure-spring-cloud/certs/public* folder. Use the following Java code to load a public certificate in an application in Azure Spring Cloud.

```java
CertificateFactory factory = CertificateFactory.getInstance("X509");
FileInputStream is = new FileInputStream("/etc/azure-spring-cloud/certs/public/<certificate name>");
X509Certificate cert = (X509Certificate) factory.generateCertificate(is);

// use the loaded certificate
```

### Load a certificate into the trust store

For a java application, you can choose **Load into trust store** for the selected certificate. The certificate will be automatically added to the Java default TrustStores to authenticate a server in SSL authentication.

The following log from your app shows that the certificate is successfully loaded.

```
Load certificate from specific path. alias = <certificate alias>, thumbprint = <certificate thumbprint>, file = <certificate name>
```

## Next steps

* [Enable end-to-end Transport Layer Security](./how-to-enable-end-to-end-tls.md)
* [Access Config Server and Service Registry](./how-to-access-data-plane-azure-ad-rbac.md)
