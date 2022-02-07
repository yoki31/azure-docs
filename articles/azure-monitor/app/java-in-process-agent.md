---
title: Azure Monitor Application Insights Java
description: Application performance monitoring for Java applications running in any environment without requiring code modification. Distributed tracing and application map.
ms.topic: conceptual
ms.date: 06/24/2021
ms.devlang: java
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
---

# Azure Monitor OpenTelemetry-based auto-instrumentation for Java applications

This article describes how to enable and configure the OpenTelemetry-based Azure Monitor Java offering. After you finish the instructions in this article, you'll be able to use Azure Monitor Application Insights to monitor your application.

## Get started

Java auto-instrumentation can be enabled without any code changes.

### Prerequisites

- Java application using Java 8+
- Azure subscription: [Create an Azure subscription for free](https://azure.microsoft.com/free/)
- Application Insights resource: [Create an Application Insights resource](create-workspace-resource.md#create-workspace-based-resource)

### Enable Azure Monitor Application Insights

This section shows you how to download the auto-instrumentation jar file.

#### Download the jar file

Download the [applicationinsights-agent-3.2.5.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.2.5/applicationinsights-agent-3.2.5.jar) file.

> [!WARNING]
> 
> If you're upgrading from 3.0 Preview:
>
>    - Review all [configuration options](./java-standalone-config.md) carefully. The JSON structure has completely changed. The file name is now all lowercase.
> 
> If you're upgrading from 3.0.x:
> 
>    - The operation names and request telemetry names are now prefixed by the HTTP method, such as `GET` and `POST`.
>    This change can affect custom dashboards or alerts if they relied on the previous values.
>    For details, see the [3.1.0 release notes](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0).
>
> If you're upgrading from 3.1.x:
>    -  Starting from 3.2.0, controller "InProc" dependencies are not captured by default. For details on how to enable this, please see the [config options](./java-standalone-config.md#autocollect-inproc-dependencies-preview).
>    - Database dependency names are now more concise with the full (sanitized) query still present in the `data` field. HTTP dependency names are now more descriptive.
>    This change can affect custom dashboards or alerts if they relied on the previous values.
>    For details, see the [3.2.0 release notes](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0).

#### Point the JVM to the jar file

Add `-javaagent:path/to/applicationinsights-agent-3.2.5.jar` to your application's JVM args.

> [!TIP]
> For help with configuring your application's JVM args, see [Tips for updating your JVM args](./java-standalone-arguments.md).

#### Set the Application Insights connection string

1. There are two ways you can point the jar file to your Application Insights resource:

   - You can set an environment variable:
    
        ```console
        APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
        ```

   - Or you can create a configuration file named `applicationinsights.json`. Place it in the same directory as `applicationinsights-agent-3.2.5.jar` with the following content:

        ```json
        {
          "connectionString": "InstrumentationKey=..."
        }
        ```

1. Find the connection string on your Application Insights resource.

    :::image type="content" source="media/java-ipa/connection-string.png" alt-text="Screenshot that shows the Application Insights connection string.":::
    
#### Confirm data is flowing

Run your application and open your **Application Insights Resource** tab in the Azure portal. It can take a few minutes for data to show up in the portal.

> [!NOTE]
> If you can't run the application or you aren't getting data as expected, see the [Troubleshooting](#troubleshooting) section.

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="Screenshot that shows the Application Insights Overview tab with server requests and server response time highlighted.":::

> [!IMPORTANT]
> If you have two or more services that emit telemetry to the same Application Insights resource, you're required to [set cloud role names](java-standalone-config.md#cloud-role-name) to represent them properly on the application map.

As part of using Application Insights instrumentation, we collect and send diagnostic data to Microsoft. This data helps us run and improve Application Insights. You have the option to disable nonessential data collection. To learn more, see [Statsbeat in Azure Application Insights](./statsbeat.md).

## Configuration options

In the `applicationinsights.json` file, you can also configure these settings:

* Cloud role name
* Cloud role instance
* Sampling
* JMX metrics
* Custom dimensions
* Telemetry processors (preview)
* Autocollected logging
* Autocollected Micrometer metrics, which include Spring Boot Actuator metrics
* Heartbeat
* HTTP proxy
* Self-diagnostics

For more information, see [Configuration options](./java-standalone-config.md).

## Instrumentation libraries

Java 3.x includes the following instrumentation libraries.

### Autocollected requests

* JMS consumers
* Kafka consumers
* Netty/WebFlux
* Servlets
* Spring scheduling

### Autocollected dependencies

Autocollected dependencies plus downstream distributed trace propagation:

* Apache HttpClient
* Apache HttpAsyncClient
* AsyncHttpClient
* Google HttpClient
* gRPC
* java.net.HttpURLConnection
* Java 11 HttpClient
* JAX-RS client
* Jetty HttpClient
* JMS
* Kafka
* Netty client
* OkHttp

Autocollected dependencies without downstream distributed trace propagation:

* Cassandra
* JDBC
* MongoDB (async and sync)
* Redis (Lettuce and Jedis)

### Autocollected logs

* java.util.logging
* Log4j, which includes MDC properties
* SLF4J/Logback, which includes MDC properties

### Autocollected metrics

* Micrometer, which includes Spring Boot Actuator metrics
* JMX Metrics

### Azure SDKs

Telemetry emitted by these Azure SDKs is autocollected by default:

* [Azure App Configuration](/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Azure Cognitive Search](/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Azure Communication Chat](/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [Azure Communication Common](/java/api/overview/azure/communication-common-readme) 1.0.0+
* [Azure Communication Identity](/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [Azure Communication Phone Numbers](/java/api/overview/azure/communication-phonenumbers-readme) 1.0.0+
* [Azure Communication SMS](/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Azure Cosmos DB](/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Azure Digital Twins - Core](/java/api/overview/azure/digitaltwins-core-readme) 1.1.0+
* [Azure Event Grid](/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Azure Event Hubs](/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Azure Event Hubs - Azure Blob Storage Checkpoint Store](/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Azure Form Recognizer](/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Azure Identity](/java/api/overview/azure/identity-readme) 1.2.4+
* [Azure Key Vault - Certificates](/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Azure Key Vault - Keys](/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [Azure Key Vault - Secrets](/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Azure Service Bus](/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Azure Storage - Blobs](/java/api/overview/azure/storage-blob-readme) 12.11.0+
* [Azure Storage - Blobs Batch](/java/api/overview/azure/storage-blob-batch-readme) 12.9.0+
* [Azure Storage - Blobs Cryptography](/java/api/overview/azure/storage-blob-cryptography-readme) 12.11.0+
* [Azure Storage - Common](/java/api/overview/azure/storage-common-readme) 12.11.0+
* [Azure Storage - Files Data Lake](/java/api/overview/azure/storage-file-datalake-readme) 12.5.0+
* [Azure Storage - Files Shares](/java/api/overview/azure/storage-file-share-readme) 12.9.0+
* [Azure Storage - Queues](/java/api/overview/azure/storage-queue-readme) 12.9.0+
* [Azure Text Analytics](/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "the above names and links scraped from https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "and version sync'd manually against the oldest version in maven central built on azure-core 1.14.0"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    continue"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version + '\n'"
[//]: # "}"
[//]: # "console.log(str)"

## Modify telemetry

This section explains how to modify telemetry.

### Add span attributes

You can use `opentelemetry-api` to add attributes to spans. These attributes can include adding a custom business dimension to your telemetry. You can also use attributes to set optional fields in the Application Insights schema, such as User ID or Client IP.

#### Add a custom dimension

Adding one or more custom dimensions populates the _customDimensions_ field in the requests, dependencies, or exceptions table.

> [!NOTE]
> This feature is only in 3.2.0 and later.

1. Add `opentelemetry-api-1.6.0.jar` to your application:

    ```xml
    <dependency>
      <groupId>io.opentelemetry</groupId>
      <artifactId>opentelemetry-api</artifactId>
      <version>1.6.0</version>
    </dependency>
    ```

1. Add custom dimensions in your code:

    ```java
    import io.opentelemetry.api.trace.Span;
    
    Span.current().setAttribute("mycustomdimension", "myvalue1");
    ```

#### Set the user ID

Populate the _user ID_ field in the requests, dependencies, or exceptions table.

> [!IMPORTANT]
> Consult applicable privacy laws before you set Authenticated User ID.

> [!NOTE]
> This feature is only in 3.2.0 and later.

1. Add `opentelemetry-api-1.6.0.jar` to your application:

    ```xml
    <dependency>
      <groupId>io.opentelemetry</groupId>
      <artifactId>opentelemetry-api</artifactId>
      <version>1.6.0</version>
    </dependency>
    ```

1. Set `user_Id` in your code:

    ```java
    import io.opentelemetry.api.trace.Span;
    
    Span.current().setAttribute("enduser.id", "myuser");
    ```

### Get the trace ID or span ID

You can use `opentelemetry-api` to get the trace ID or span ID. This action can be done to add these identifiers to existing logging telemetry to improve correlation when you debug and diagnose issues.

> [!NOTE]
> This feature is only in 3.2.0 and later.

1. Add `opentelemetry-api-1.6.0.jar` to your application:
    
    ```xml
    <dependency>
      <groupId>io.opentelemetry</groupId>
      <artifactId>opentelemetry-api</artifactId>
      <version>1.6.0</version>
    </dependency>
    ```

1. Get the request trace ID and the span ID in your code:

    ```java
    import io.opentelemetry.api.trace.Span;
    
    String traceId = Span.current().getSpanContext().getTraceId();
    String spanId = Span.current().getSpanContext().getSpanId();
    ```

## Custom telemetry

Our goal in Application Insights Java 3.x is to allow you to send your custom telemetry by using standard APIs.

We currently support Micrometer, popular logging frameworks, and the Application Insights Java 2.x SDK. Application Insights Java 3.x automatically captures the telemetry sent through these APIs and correlates it with autocollected telemetry.

### Supported custom telemetry

The following table represents currently supported custom telemetry types that you can enable to supplement the Java 3.x agent. To summarize:

- Custom metrics are supported through micrometer.
- Custom exceptions and traces are supported through logging frameworks.
- Custom requests, dependencies, and exceptions are supported through `opentelemetry-api`.
- Any type of the custom telemetry is supported through the [Application Insights Java 2.x SDK](#send-custom-telemetry-by-using-the-2x-sdk).

| Custom telemetry type            | Micrometer | Log4j, logback, JUL | 2.x SDK | opentelemetry-api |
|---------------------|------------|---------------------|---------|-------------------|
| Custom events       |            |                     |  Yes    |                   |
| Custom metrics      |  Yes       |                     |  Yes    |                   |
| Dependencies        |            |                     |  Yes    |  Yes              |
| Exceptions          |            |  Yes                |  Yes    |  Yes              |
| Page views          |            |                     |  Yes    |                   |
| Requests            |            |                     |  Yes    |  Yes              |
| Traces              |            |  Yes                |  Yes    |                   |

Currently, we're not planning to release an SDK with Application Insights 3.x.

Application Insights Java 3.x is already listening for telemetry that's sent to the Application Insights Java 2.x SDK. This functionality is an important part of the upgrade story for existing 2.x users. And it fills an important gap in our custom telemetry support until the OpenTelemetry API is generally available.

### Send custom metrics by using Micrometer

1. Add Micrometer to your application:
    
    ```xml
    <dependency>
      <groupId>io.micrometer</groupId>
      <artifactId>micrometer-core</artifactId>
      <version>1.6.1</version>
    </dependency>
    ```

2. Use the Micrometer [global registry](https://micrometer.io/docs/concepts#_global_registry) to create a meter:

    ```java
    static final Counter counter = Metrics.counter("test.counter");
    ```

3. Use the counter to record metrics:

    ```java
    counter.increment();
    ```

4. The metrics will be ingested into the
   [customMetrics](/azure/azure-monitor/reference/tables/custommetrics) table, with tags captured in the
   `customDimensions` column. You can also view the metrics in the
   [Metrics explorer](../essentials/metrics-getting-started.md) under the "Log-based metrics" metric namespace.

    > [!NOTE]
    > Application Insights Java replaces all non-alphanumeric characters (except dashes) in the Micrometer metric name
    > with underscores, so the `test.counter` metric above will show up as `test_counter`.

### Send custom traces and exceptions by using your favorite logging framework

Log4j, Logback, and java.util.logging are auto-instrumented. Logging performed via these logging frameworks is autocollected as trace and exception telemetry.

By default, logging is only collected when that logging is performed at the INFO level or above.
To change this level, see the [configuration options](./java-standalone-config.md#auto-collected-logging).

If you want to attach custom dimensions to your logs, use [Log4j 1.2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html), or [Logback MDC](http://logback.qos.ch/manual/mdc.html). Application Insights Java 3.x automatically captures those MDC properties as custom dimensions on your trace and exception telemetry.

### Send custom telemetry by using the 2.x SDK

1. Add `applicationinsights-core-2.6.4.jar` to your application. All 2.x versions are supported by Application Insights Java 3.x. If you have a choice. it's worth using the latest version:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>applicationinsights-core</artifactId>
      <version>2.6.4</version>
    </dependency>
    ```

1. Create a TelemetryClient:
    
    ```java
    static final TelemetryClient telemetryClient = new TelemetryClient();
    ```

1. Use the client to send custom telemetry:

    ##### Events
    
    ```java
    telemetryClient.trackEvent("WinGame");
    ```
    
    ##### Metrics
    
    ```java
    telemetryClient.trackMetric("queueLength", 42.0);
    ```
    
    ##### Dependencies
    
    ```java
    boolean success = false;
    long startTime = System.currentTimeMillis();
    try {
        success = dependency.call();
    } finally {
        long endTime = System.currentTimeMillis();
        RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
        telemetry.setSuccess(success);
        telemetry.setTimestamp(new Date(startTime));
        telemetry.setDuration(new Duration(endTime - startTime));
        telemetryClient.trackDependency(telemetry);
    }
    ```
    
    ##### Logs
    
    ```java
    telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
    ```
    
    ##### Exceptions
    
    ```java
    try {
        ...
    } catch (Exception e) {
        telemetryClient.trackException(e);
    }
    ```

## Troubleshooting

For help with troubleshooting, see [Troubleshooting](java-standalone-troubleshoot.md).

## Release notes

See the [release notes](https://github.com/microsoft/ApplicationInsights-Java/releases) on GitHub.

## Support

To get support:

- For help with troubleshooting, review the [troubleshooting steps](java-standalone-troubleshoot.md).
- For Azure support issues, open an [Azure support ticket](https://azure.microsoft.com/support/create-ticket/).
- For OpenTelemetry issues, contact the [OpenTelemetry community](https://opentelemetry.io/community/) directly.

## OpenTelemetry feedback

To provide feedback:

- Fill out the OpenTelemetry community's [customer feedback survey](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform).
- Tell Microsoft about yourself by joining our [OpenTelemetry Early Adopter Community](https://aka.ms/AzMonOTel/).
- Engage with other Azure Monitor users in the  [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor).
- Make a feature request at the [Azure Feedback Forum](https://feedback.azure.com/d365community/forum/8849e04d-1325-ec11-b6e6-000d3a4f09d0).

## Next steps

- To review the source code, see the [Azure Monitor Java auto-instrumentation GitHub repository](https://github.com/Microsoft/ApplicationInsights-Java).
- To learn more about OpenTelemetry and its community, see the [OpenTelemetry Java GitHub repository](https://github.com/open-telemetry/opentelemetry-java-instrumentation).
- To enable usage experiences, see [Enable web or browser user monitoring](javascript.md).
