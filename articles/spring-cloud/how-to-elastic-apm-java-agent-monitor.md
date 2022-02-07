---
title:  How to monitor Spring Boot apps with Elastic APM Java Agent
description: How to use Elastic APM Java Agent to monitor Spring Boot applications running in Azure Spring Cloud
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/07/2021
ms.custom: devx-track-java
---

# How to monitor Spring Boot apps with Elastic APM Java Agent

This article explains how to use Elastic APM Agent to monitor Spring Boot applications running in Azure Spring Cloud.

With the Elastic Observability Solution, you can achieve unified observability to:

* Monitor apps using the Elastic APM Java Agent and using persistent storage with Azure Spring Cloud.
* Use diagnostic settings to ship Azure Spring Cloud logs to Elastic. For more information, see [Analyze logs with Elastic (ELK) using diagnostics settings](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/how-to-elastic-diagnostic-settings.md).

The following video introduces unified observability for Spring Boot applications using Elastic.

<br>

> [!VIDEO https://www.youtube.com/embed/KjmQX1SxZdA]

## Prerequisites

* [Azure CLI](/cli/azure/install-azure-cli)
* [Deploy Elastic on Azure](https://www.elastic.co/blog/getting-started-with-the-azure-integration-enhancement)
* [Elastic APM Endpoint and Secret Token from the Elastic Deployment](https://www.elastic.co/guide/en/cloud/current/ec-manage-apm-and-fleet.html)

## Deploy the Spring Petclinic application

This article uses the Spring Petclinic sample to walk through the required steps. Use the following steps to deploy the sample application:

1. Follow the steps in [Deploy Spring Boot apps using Azure Spring Cloud and MySQL](https://github.com/Azure-Samples/spring-petclinic-microservices#readme) until you reach the [Deploy Spring Boot applications and set environment variables](https://github.com/Azure-Samples/spring-petclinic-microservices#deploy-spring-boot-applications-and-set-environment-variables) section.

1. Use the Azure Spring Cloud extension for Azure CLI with the following command to create an application to run in Azure Spring Cloud:

   ```azurecli
   az spring-cloud app create \
      --resource-group <your-resource-group-name> \
      --service <your-Azure-Spring-Cloud-instance-name> \
      --name <your-app-name> \
      --is-public true
   ```

## Enable custom persistent storage for Azure Spring Cloud

Use the following steps to enable custom persistent storage:

1. Follow the steps in [How to enable your own persistent storage in Azure Spring Cloud](how-to-custom-persistent-storage.md).

1. Use the following Azure CLI command to add persistent storage for your Azure Spring Cloud apps.

   ```azurecli
   az spring-cloud app append-persistent-storage \
      --resource-group <your-resource-group-name> \
      --service <your-Azure-Spring-Cloud-instance-name> \
      --name <your-app-name> \
      --persistent-storage-type AzureFileVolume \
      --share-name <your-Azure-file-share-name> \
      --mount-path <unique-mount-path> \
      --storage-name <your-mounted-storage-name>
   ```

## Activate Elastic APM Java Agent

Before proceeding, you'll need your Elastic APM server connectivity information handy, which assumes you've deployed Elastic on Azure. For more information, see [How to deploy and manage Elastic on Microsoft Azure](https://www.elastic.co/blog/getting-started-with-the-azure-integration-enhancement). To get this information, use the following steps:

1. In the Azure portal, go to the **Overview** page of your Elastic deployment, then select **Manage Elastic Cloud Deployment**.

   :::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-get-link-from-microsoft-azure.png" alt-text="Azure portal screenshot of 'Elasticsearch (Elastic Cloud)' page." lightbox="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-get-link-from-microsoft-azure.png":::

1. Under your deployment on Elastic Cloud Console, select the **APM & Fleet** section to get Elastic APM Server endpoint and secret token.

   :::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-endpoint-secret.png" alt-text="Elastic screenshot 'APM & Fleet' page." lightbox="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-endpoint-secret.png":::

1. Download Elastic APM Java Agent from [Maven Central](https://search.maven.org/search?q=g:co.elastic.apm%20AND%20a:elastic-apm-agent).

   :::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/maven-central-repository-search.png" alt-text="Maven Central screenshot with jar download highlighted." lightbox="media/how-to-elastic-apm-java-agent-monitor/maven-central-repository-search.png":::

1. Upload Elastic APM Agent to the custom persistent storage you enabled earlier. Go to Azure Fileshare and select **Upload** to add the agent JAR file.

   :::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/upload-files-microsoft-azure.png" alt-text="Azure portal screenshot showing 'Upload files' pane of 'File share' page." lightbox="media/how-to-elastic-apm-java-agent-monitor/upload-files-microsoft-azure.png":::

1. After you have the Elastic APM endpoint and secret token, use the following command to activate Elastic APM Java agent when deploying applications. The placeholder *`<agent-location>`* refers to the mounted storage location of the Elastic APM Java Agent.

   ```azurecli
   az spring-cloud app deploy \
       --name <your-app-name> \
       --artifact-path <unique-path-to-your-app-jar-on-custom-storage> \
       --jvm-options='-javaagent:<agent-location>' \
       --env ELASTIC_APM_SERVICE_NAME=<your-app-name> \
             ELASTIC_APM_APPLICATION_PACKAGES='<your-app-package-name>' \
             ELASTIC_APM_SERVER_URL='<your-Elastic-APM-server-URL>' \
             ELASTIC_APM_SECRET_TOKEN='<your-Elastic-APM-secret-token>'
   ```

## Automate provisioning

You can also run a provisioning automation pipeline using Terraform or an Azure Resource Manager template (ARM template). This pipeline can provide a complete hands-off experience to instrument and monitor any new applications that you create and deploy.

### Automate provisioning using Terraform

To configure the environment variables in a Terraform template, add the following code to the template, replacing the *\<...>* placeholders with your own values. For more information, see [Manages an Active Azure Spring Cloud Deployment](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment).

```terraform
resource "azurerm_spring_cloud_java_deployment" "example" {
  ...
  jvm_options = "-javaagent:<unique-path-to-your-app-jar-on-custom-storage>"
  ...
    environment_variables = {
      "ELASTIC_APM_SERVICE_NAME"="<your-app-name>",
      "ELASTIC_APM_APPLICATION_PACKAGES"="<your-app-package>",
      "ELASTIC_APM_SERVER_URL"="<your-Elastic-APM-server-URL>",
      "ELASTIC_APM_SECRET_TOKEN"="<your-Elastic-APM-secret-token>"
  }
}
```

### Automate provisioning using an ARM template

To configure the environment variables in an ARM template, add the following code to the template, replacing the *\<...>* placeholders with your own values. For more information, see [Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json).

```arm
"deploymentSettings": {
  "environmentVariables": {
    "ELASTIC_APM_SERVICE_NAME"="<your-app-name>",
    "ELASTIC_APM_APPLICATION_PACKAGES"="<your-app-package>",
    "ELASTIC_APM_SERVER_URL"="<your-Elastic-APM-server-URL>",
    "ELASTIC_APM_SECRET_TOKEN"="<your-Elastic-APM-secret-token>"
  },
  "jvmOptions": "-javaagent:<unique-path-to-your-app-jar-on-custom-storage>",
  ...
}
```

## Upgrade Elastic APM Java Agent

To plan your upgrade, see [Upgrade versions](https://www.elastic.co/guide/en/cloud/current/ec-upgrade-deployment.html) for Elastic Cloud on Azure, and [Breaking Changes](https://www.elastic.co/guide/en/apm/server/current/breaking-changes.html) for APM. After you've upgraded APM Server, upload the Elastic APM Java agent JAR file in the custom persistent storage and restart apps with updated JVM options pointing to the upgraded Elastic APM Java agent JAR.

## Monitor applications and metrics with Elastic APM

Use the following steps to monitor applications and metrics:

1. In the Azure portal, go to the **Overview** page of your Elastic deployment, then select the Kibana link.

   :::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-get-kibana-link.png" alt-text="Azure portal screenshot showing Elasticsearch page with 'Deployment URL / Kibana' highlighted." lightbox="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-get-kibana-link.png":::

1. After Kibana is open, search for *APM* in the search bar, then select **APM**.

   :::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-kibana-search-apm.png" alt-text="Elastic / Kibana screenshot showing APM search results." lightbox="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-kibana-search-apm.png":::

Kibana APM is the curated application to support Application Monitoring workflows. Here you can view high-level details such as request/response times, throughput, transactions in a service with most impact on the duration.

:::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-customer-service.png" alt-text="Elastic / Kibana screenshot showing APM Services Overview page." lightbox="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-customer-service.png":::

You can drill down in a specific transaction to understand the transaction-specific details such as the distributed tracing.

:::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-customer-service-latency-distribution.png" alt-text="Elastic / Kibana screenshot showing APM Services Transactions page." lightbox="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-customer-service-latency-distribution.png":::

Elastic APM Java agent also captures the JVM metrics from the Azure Spring Cloud apps that are available with Kibana App for users for troubleshooting.

:::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-customer-service-jvm-metrics.png" alt-text="Elastic / Kibana screenshot showing APM Services JVMs page." lightbox="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-customer-service-jvm-metrics.png":::

Using the inbuilt AI engine in the Elastic solution, you can also enable Anomaly Detection on the Azure Spring Cloud Services and choose an appropriate action - such as Teams notification, creation of a JIRA issue, a webhook-based API call, and others.

:::image type="content" source="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-alert-anomaly.png" alt-text="Elastic / Kibana screenshot showing APM Services page with 'Create rule' pane showing and Actions highlighted." lightbox="media/how-to-elastic-apm-java-agent-monitor/elastic-apm-alert-anomaly.png":::

## Next steps

* [Quickstart: Deploy your first Spring Boot app in Azure Spring Cloud](./quickstart.md)
* [Deploy Elastic on Azure](https://www.elastic.co/blog/getting-started-with-the-azure-integration-enhancement)
