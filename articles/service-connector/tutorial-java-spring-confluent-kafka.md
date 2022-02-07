---
title: 'Tutorial: Deploy a Spring Boot app connected to Apache Kafka on Confluent Cloud with Service Connector in Azure Spring Cloud'
description: Create a Spring Boot app connected to Apache Kafka on Confluent Cloud with Service Connector in Azure Spring Cloud.
ms.devlang: java
author: shizn
ms.author: xshi
ms.service: service-connector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
---

# Tutorial: Deploy a Spring Boot app connected to Apache Kafka on Confluent Cloud with Service Connector in Azure Spring Cloud

Learn how to access Apache Kafka on Confluent Cloud for a spring boot application running on Azure Spring Cloud. In this tutorial, you complete the following tasks:

> [!div class="checklist"]
> * Create Apache Kafka on Confluent Cloud
> * Create a Spring Cloud application
> * Build and deploy the Spring Boot app
> * Connect Apache Kafka on Confluent Cloud to Azure Spring Cloud using Service Connector

## 1. Set up your initial environment

1. Have an Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Install Java 8 or 11.
3. Install the <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0 or higher, with which you run commands in any shell to provision and configure Azure resources.

## 2. Clone or download the sample app

Clone the sample repository:

```Bash
git clone https://github.com/Azure-Samples/serviceconnector-springcloud-confluent-springboot/
```

Then navigate into that folder:

```Bash
cd serviceconnector-springcloud-confluent-springboot
```

## 3. Prepare cloud services

### 3.1 Create an instance of Apache Kafka for Confluent Cloud

Create an instance of Apache Kafka for Confluent Cloud by following [this guidance](../partner-solutions/apache-kafka-confluent-cloud/create.md).

### 3.2 Create Kafka cluster and schema registry on Confluent Cloud

1. Login to Confluent Cloud by SSO provided by Azure

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png" alt-text="The link of Confluent cloud SSO login using Azure portal" lightbox="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png":::

1. Use the default environment or create a new one

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png" alt-text="Cloud environment of Apache Kafka on Confluent Cloud" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png":::

1. Create a Kafka cluster with the following information

    * Cluster type: Standard
    * Region/zones: eastus(Virginia), Single Zone
    * Cluster name: `cluster_1` or any other name.

1. In **Cluster overview** -> **Cluster settings**, get the Kafka **bootstrap server url** and take a note.

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png" alt-text="Cluster settings of Apache Kafka on Confluent Cloud" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png":::

1. Create API-keys for the cluster in **Data integration** -> **API Keys** -> **+ Add Key** with **Global access**. Take a note of the key and secret.
1. Create a topic named `test` with partitions 6 in **Topics** -> **+ Add topic**
1. In default environment, click **Schema Registry** tab. Enable the Schema Registry and take a note of the **API endpoint**.
1. Create API-keys for schema registry. Take a note of the key and secret.

### 3.3 Create a Spring Cloud instance

Create an instance of Azure Spring Cloud by following [this guidance](../spring-cloud/quickstart.md) in Java. Make sure your Spring Cloud instance is created in [the region that has Service Connector support](concept-region-support.md).

## 4. Build and deploy the app

### 4.1 build the sample app and create a new spring app

1. Sign in to Azure and choose your subscription.

```azurecli
az login

az account set --subscription <Name or ID of your subscription>
```

1. Build the project using gradle

```Bash
./gradlew build
```

1. Create the app with public endpoint assigned. If you selected Java version 11 when generating the Spring Cloud project, include the --runtime-version=Java_11 switch.

```azurecli
az spring-cloud app create -n hellospring -s <service-instance-name> -g <your-resource-group-name> --assign-endpoint true
```

## 4.2 Create service connection using Service Connector

#### [CLI](#tab/Azure-CLI)

Run the following command to connect your Apache Kafka on Confluent cloud to your spring cloud app.

```azurecli
az spring-cloud connection create confluent-cloud -g <your-spring-cloud-resource-group> --service <your-spring-cloud-service> --app <your-spring-cloud-app> --deployment <your-spring-cloud-deployment> --bootstrap-server <kafka-bootstrap-server-url> --kafka-key <cluster-api-key> --kafka-secret <cluster-api-secret> --schema-registry <kafka-schema-registry-endpoint> --schema-key <registry-api-key> --schema-secret <registry-api-secret>
```

* **Replace** *\<your-resource-group-name>* with the resource group name that you created your Spring Cloud instance.
* **Replace** *\<kafka-bootstrap-server-url>* with your kafka bootstrap server url (the value should be like `pkc-xxxx.eastus.azure.confluent.cloud:9092`)
* **Replace** *\<cluster-api-key>* and *\<cluster-api-secret>* with your cluster API key and secret.
* **Replace** *\<kafka-schema-registry-endpoint>* with your kafka Schema Registry endpoint (the value should be like `https://psrc-xxxx.westus2.azure.confluent.cloud`)
* **Replace** *\<registry-api-key>* and *\<registry-api-secret>* with your kafka Schema Registry API key and secret.

> [!NOTE]
> If you see the error message "The subscription is not registered to use Microsoft.ServiceLinker", please run `az provider register -n Microsoft.ServiceLinker` to register the Service Connector resource provider and run the connection command again. 

#### [Portal](#tab/Azure-portal)

Click **Service Connector (Preview)** Select or enter the following settings.

| Setting      | Suggested value  | Description                               |
| ------------ |  ------- | -------------------------------------------------- |
| **Service Type** | Apache Kafka on Confluent cloud | Target service type. If you don't have a Apache Kafka on Confluent cloud, please complete the previous steps in this tutorial. |
| **Name** | Generated unique name | The connection name that identifies the connection between your Spring Cloud and target service  |
| **Kafka bootstrap server url** | Your kafka bootstrap server url | You get this value from step 3.2 |
| **Cluster API Key** | Your cluster API key |  |
| **Cluster API Secret** |  Your cluster API secret |  |
| **Create connection for schema registry**  | checked | Also create a connection to the schema registry |
| **Schema Registry endpoint** | Your kafka Schema Registry endpoint  |  |
| **Schema Registry API Key** | Your kafka Schema Registry API Key | |
| **Schema Registry API Secret** | Your kafka Schema Registry API Secret | |

Select **Review + Create** to review the connection settings. Then select **Create** to create start creating the service connection.

---

## 4.3 Deploy the Jar file for the app

Run the following command to upload the jar file (`build/libs/java-springboot-0.0.1-SNAPSHOT.jar`) to your Spring Cloud app

```azurecli
az spring-cloud app deploy -n hellospring -s <service-instance-name> -g <your-resource-group-name>  --artifact-path build/libs/java-springboot-0.0.1-SNAPSHOT.jar
```

## 5. Validate the Kafka data ingestion

Navigate to your Spring Cloud app's endpoint from Azure portal, click the application URL. You will see "10 messages were produced to topic test".

Then go to the Confluent portal and the topic's page will show production throughput.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png" alt-text="Sample metrics" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png":::

## Next steps

Follow the tutorials listed below to learn more about Service Connector.

> [!div class="nextstepaction"]
> [Learn about Service Connector concepts](./concept-service-connector-internals.md)
