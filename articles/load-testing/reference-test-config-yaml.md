---
title: Load test configuration YAML
titleSuffix: Azure Load Testing
description: 'Learn how to configure a load test by using a YAML file. The YAML configuration is used for setting up automated load testing in a CI/CD pipeline.'
services: load-testing
ms.service: load-testing
ms.topic: reference
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
adobe-target: true
---

# Configure a load test in YAML

Learn how to configure your load test in Azure Load Testing Preview by using [YAML](https://yaml.org/). You use the test configuration YAML file to create and run load tests from your continuous integration and continuous delivery (CI/CD) workflow.

> [!IMPORTANT]
> Azure Load Testing is currently in preview. For legal terms that apply to Azure features that are in beta, in preview, or otherwise not yet released into general availability, see the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Load test definition

A test configuration uses the following keys:

| Key | Type | Description | 
| ----- | ----- | ----- | 
| `version` | string | Version of the YAML configuration file that the service uses. Currently, the only valid value is `v0.1`. |
| `testName` | string | *Required*. Name of the test to run. The results of various test runs will be collected under this test name in the Azure portal. |
| `testPlan` | string | *Required*. Relative path to the Apache JMeter test script to run. |
| `engineInstances` | integer | *Required*. Number of parallel instances of the test engine to execute the provided test plan. You can update this property to increase the amount of load that the service can generate. |
| `configurationFiles` | array | List of relevant configuration files or other files that you reference in the Apache JMeter script. For example, a CSV data set file, images, or any other data file. These files will be uploaded to the Azure Load Testing resource alongside the test script. If the files are in a subfolder on your local machine, use file paths that are relative to the location of the test script. <BR><BR>Azure Load Testing currently doesn't support the use of file paths in the JMX file. When you reference an external file in the test script, make sure to only specify the file name. |
| `description` | string | Short description of the test run. |
| `failureCriteria` | object | Criteria that indicate failure of the test. Each criterion is in the form of:<BR>`[Aggregate_function] ([client_metric]) > [value]`<BR><BR>- `[Aggregate function] ([client_metric])` is either `avg(response_time_ms)` or `percentage(error).`<BR>- `value` is an integer number. |
| `secrets` | object | List of secrets that the Apache JMeter script references. |
| `secrets.name` | string | Name of the secret. This name should match the secret name that you use in the Apache JMeter script. |
| `secrets.value` | string | URI for the Azure Key Vault secret. |
| `env` | object | List of environment variables that the Apache JMeter script references. |
| `env.name` | string | Name of the environment variable. This name should match the secret name that you use in the Apache JMeter script. |
| `env.value` | string | Value of the environment variable. |

The following example contains the configuration for a load test:

```yaml
version: v0.1
testName: SampleTest
testPlan: SampleTest.jmx
description: Load test website home page
engineInstances: 1
configurationFiles:
  - 'SampleData.csv'
failureCriteria:
  - avg(response_time_ms) > 300
  - percentage(error) > 50
env:
  - name: my-variable
    value: my-value
secrets:
  - name: my-secret
    value: https://akv-contoso.vault.azure.net/secrets/MySecret
```

## Next steps

Learn how to build [automated regression testing in your CI/CD workflow](tutorial-cicd-azure-pipelines.md).
