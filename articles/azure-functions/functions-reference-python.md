---
title: Python developer reference for Azure Functions
description: Understand how to develop functions with Python
ms.topic: article
ms.date: 11/4/2020
ms.devlang: python
ms.custom: devx-track-python
---

# Azure Functions Python developer guide

This article is an introduction to developing Azure Functions using Python. The content below assumes that you've already read the [Azure Functions developers guide](functions-reference.md).

As a Python developer, you may also be interested in one of the following articles:

| Getting started | Concepts| Scenarios/Samples |
|--|--|--|
| <ul><li>[Python function using Visual Studio Code](./create-first-function-vs-code-python.md)</li><li>[Python function with terminal/command prompt](./create-first-function-cli-python.md)</li></ul> | <ul><li>[Developer guide](functions-reference.md)</li><li>[Hosting options](functions-scale.md)</li><li>[Performance&nbsp;considerations](functions-best-practices.md)</li></ul> | <ul><li>[Image classification with PyTorch](machine-learning-pytorch.md)</li><li>[Azure automation sample](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Machine learning with TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Browse Python samples](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> While you can [develop your Python based Azure Functions locally on Windows](create-first-function-vs-code-python.md#run-the-function-locally), Python is only supported on a Linux based hosting plan when running in Azure. See the list of supported [operating system/runtime](functions-scale.md#operating-systemruntime) combinations.

## Programming model

Azure Functions expects a function to be a stateless method in your Python script that processes input and produces output. By default, the runtime expects the method to be implemented as a global method called `main()` in the `__init__.py` file. You can also [specify an alternate entry point](#alternate-entry-point).

Data from triggers and bindings is bound to the function via method attributes using the `name` property defined in the *function.json* file. For example, the  _function.json_ below describes a simple function triggered by an HTTP request named `req`:

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Based on this definition, the `__init__.py` file that contains the function code might look like the following example:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

You can also explicitly declare the attribute types and return type in the function using Python type annotations. This helps you use the intellisense and autocomplete features provided by many Python code editors.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Use the Python annotations included in the [azure.functions.*](/python/api/azure-functions/azure.functions) package to bind input and outputs to your methods.

## Alternate entry point

You can change the default behavior of a function by optionally specifying the `scriptFile` and `entryPoint` properties in the *function.json* file. For example, the _function.json_ below tells the runtime to use the `customentry()` method in the _main.py_ file, as the entry point for your Azure Function.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## Folder structure

The recommended folder structure for a Python Functions project looks like the following example:

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
The main project folder (<project_root>) can contain the following files:

* *local.settings.json*: Used to store app settings and connection strings when running locally. This file doesn't get published to Azure. To learn more, see [local.settings.file](functions-develop-local.md#local-settings-file).
* *requirements.txt*: Contains the list of Python packages the system installs when publishing to Azure.
* *host.json*: Contains configuration options that affect all functions in a function app instance. This file does get published to Azure. Not all options are supported when running locally. To learn more, see [host.json](functions-host-json.md).
* *.vscode/*: (Optional) Contains store VSCode configuration. To learn more, see [VSCode setting](https://code.visualstudio.com/docs/getstarted/settings).
* *.venv/*: (Optional) Contains a Python virtual environment used by local development.
* *Dockerfile*: (Optional) Used when publishing your project in a [custom container](functions-create-function-linux-custom-image.md).
* *tests/*: (Optional) Contains the test cases of your function app.
* *.funcignore*: (Optional) Declares files that shouldn't get published to Azure. Usually, this file contains `.vscode/` to ignore your editor setting, `.venv/` to ignore local Python virtual environment, `tests/` to ignore test cases, and `local.settings.json` to prevent local app settings being published.

Each function has its own code file and binding configuration file (function.json).

When deploying your project to a function app in Azure, the entire contents of the main project (*<project_root>*) folder should be included in the package, but not the folder itself, which means `host.json` should be in the package root. We recommend that you maintain your tests in a folder along with other functions, in this example `tests/`. For more information, see [Unit Testing](#unit-testing).

## Import behavior

You can import modules in your function code using both absolute and relative references. Based on the folder structure shown above, the following imports work from within the function file *<project_root>\my\_first\_function\\_\_init\_\_.py*:

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  The *shared_code/* folder needs to contain an \_\_init\_\_.py file to mark it as a Python package when using absolute import syntax.

The following \_\_app\_\_ import and beyond top-level relative import are deprecated, since it is not supported by static type checker and not supported by Python test frameworks:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## Triggers and Inputs

Inputs are divided into two categories in Azure Functions: trigger input and other input. Although they're different in the `function.json` file, usage is identical in Python code.  Connection strings or secrets for trigger and input sources map to values in the `local.settings.json` file when running locally, and the application settings when running in Azure.

For example, the following code demonstrates the difference between the two:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

When the function is invoked, the HTTP request is passed to the function as `req`. An entry will be retrieved from the Azure Blob Storage based on the _ID_ in the route URL and made available as `obj` in the function body.  Here, the storage account specified is the connection string found in the AzureWebJobsStorage app setting, which is the same storage account used by the function app.


## Outputs

Output can be expressed both in return value and output parameters. If there's only one output, we recommend using the return value. For multiple outputs, you'll have to use output parameters.

To use the return value of a function as the value of an output binding, the `name` property of the binding should be set to `$return` in `function.json`.

To produce multiple outputs, use the `set()` method provided by the [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) interface to assign a value to the binding. For example, the following function can push a message to a queue and also return an HTTP response.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## Logging

Access to the Azure Functions runtime logger is available via a root [`logging`](https://docs.python.org/3/library/logging.html#module-logging) handler in your function app. This logger is tied to Application Insights and allows you to flag warnings and errors that occur during the function execution.

The following example logs an info message when the function is invoked via an HTTP trigger.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

More logging methods are available that let you write to the console at different trace levels:

| Method                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Writes a message with level CRITICAL on the root logger.  |
| **`error(_message_)`**   | Writes a message with level ERROR on the root logger.    |
| **`warning(_message_)`**    | Writes a message with level WARNING on the root logger.  |
| **`info(_message_)`**    | Writes a message with level INFO on the root logger.  |
| **`debug(_message_)`** | Writes a message with level DEBUG on the root logger.  |

To learn more about logging, see [Monitor Azure Functions](functions-monitoring.md).

### Log custom telemetry

By default, the Functions runtime collects logs and other telemetry data generated by your functions. This telemetry ends up as traces in Application Insights. Request and dependency telemetry for certain Azure services are also collected by default by [triggers and bindings](functions-triggers-bindings.md#supported-bindings). To collect custom request and custom dependency telemetry outside of bindings, you can use the [OpenCensus Python Extensions](https://github.com/census-ecosystem/opencensus-python-extensions-azure), which sends custom telemetry data to your Application Insights instance. You can find a list of supported extensions at the [OpenCensus repository](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib).

>[!NOTE]
>To use the OpenCensus Python extensions, you need to enable [Python worker extensions](#python-worker-extensions) in your function app by setting `PYTHON_ENABLE_WORKER_EXTENSIONS` to `1`. You also need to switch to using the Application Insights connection string by adding the [`APPLICATIONINSIGHTS_CONNECTION_STRING`](functions-app-settings.md#applicationinsights_connection_string) setting to your [application settings](functions-how-to-use-azure-function-app-settings.md#settings), if it's not already there.


```
// requirements.txt
...
opencensus-extension-azure-functions
opencensus-ext-requests
```

```python
import json
import logging

import requests
from opencensus.extension.azure.functions import OpenCensusExtension
from opencensus.trace import config_integration

config_integration.trace_integrations(['requests'])

OpenCensusExtension.configure()

def main(req, context):
    logging.info('Executing HttpTrigger with OpenCensus extension')

    # You must use context.tracer to create spans
    with context.tracer.span("parent"):
        response = requests.get(url='http://example.com')

    return json.dumps({
        'method': req.method,
        'response': response.status_code,
        'ctx_func_name': context.function_name,
        'ctx_func_dir': context.function_directory,
        'ctx_invocation_id': context.invocation_id,
        'ctx_trace_context_Traceparent': context.trace_context.Traceparent,
        'ctx_trace_context_Tracestate': context.trace_context.Tracestate,
        'ctx_retry_context_RetryCount': context.retry_context.retry_count,
        'ctx_retry_context_MaxRetryCount': context.retry_context.max_retry_count,
    })
```

## HTTP Trigger and bindings

The HTTP trigger is defined in the function.json file. The `name` of the binding must match the named parameter in the function.
In the previous examples, a binding name `req` is used. This parameter is an [HttpRequest] object, and an [HttpResponse] object is returned.

From the [HttpRequest] object, you can get request headers, query parameters, route parameters, and the message body.

The following example is from the [HTTP trigger template for Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

In this function, the value of the `name` query parameter is obtained from the `params` parameter of the [HttpRequest] object. The JSON-encoded message body is read using the `get_json` method.

Likewise, you can set the `status_code` and `headers` for the response message in the returned [HttpResponse] object.

## Web frameworks

You can leverage WSGI and ASGI-compatible frameworks such as Flask and FastAPI with your HTTP-triggered Python functions. This section shows how to modify your functions to support these frameworks.

First, the function.json file must be updated to include a `route` in the HTTP trigger, as shown in the following example:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "route": "test",
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

The host.json file must also be updated to include an HTTP `routePrefix`, as shown in the following example.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "excludedTypes": "Request"
      }
    },
    "extensions": { "http": { "routePrefix": "" }}
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[2.*, 3.0.0)"
  }
}
```

Update the Python code file `init.py`, depending on the interface used by your framework. The following example shows either an ASGI hander approach or a WSGI wrapper approach for Flask:

# [ASGI](#tab/asgi)

```python
app=Flask("Test")

@app.route("/api/HandleApproach")
def test():
  return "Hello!"

def main(req: func.HttpRequest, context) -> func.HttpResponse:
  logging.info('Python HTTP trigger function processed a request.')
  return func.AsgiMiddleware(app).handle(req, context)
```

# [WSGI](#tab/wsgi)

```python
app=Flask("Test")

@app.route("/api/WrapperApproach")
def test():
  return "Hello!"

def main(req: func.HttpRequest, context) -> func.HttpResponse:
  logging.info('Python HTTP trigger function processed a request.')
  return func.WsgiMiddleware(app).handle(req, context)
```

---


## Scaling and Performance

For scaling and performance best practices for Python function apps, see the [Python scale and performance article](python-scale-performance-reference.md).

## Context

To get the invocation context of a function during execution, include the [`context`](/python/api/azure-functions/azure.functions.context) argument in its signature.

For example:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

The [**Context**](/python/api/azure-functions/azure.functions.context) class has the following string attributes:

`function_directory`
The directory in which the function is running.

`function_name`
Name of the function.

`invocation_id`
ID of the current function invocation.

`trace_context`
Context for distributed tracing. Please refer to  [`Trace Context`](https://www.w3.org/TR/trace-context/) for more information..

`retry_context`
Context for retries to the function. Please refer to [`retry-policies`](./functions-bindings-errors.md#retry-policies-preview) for more information.

## Global variables

It is not guaranteed that the state of your app will be preserved for future executions. However, the Azure Functions runtime often reuses the same process for multiple executions of the same app. In order to cache the results of an expensive computation, declare it as a global variable.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## Environment variables

In Functions, [application settings](functions-app-settings.md), such as service connection strings, are exposed as environment variables during execution. There are two main ways to access these settings in your code. 

| Method | Description |
| --- | --- |
| **`os.environ["myAppSetting"]`** | Tries to get the application setting by key name, raising an error when unsuccessful.  |
| **`os.getenv("myAppSetting")`** | Tries to get the application setting by key name, returning null when unsuccessful.  |

Both of these ways require you to declare `import os`.

The following example uses `os.environ["myAppSetting"]` to get the [application setting](functions-how-to-use-azure-function-app-settings.md#settings), with the key named `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

For local development, application settings are [maintained in the local.settings.json file](functions-develop-local.md#local-settings-file).

## Python version

Azure Functions supports the following Python versions:

| Functions version | Python<sup>*</sup> versions |
| ----- | ----- |
| 4.x | 3.9<br/> 3.8<br/>3.7 |
| 3.x | 3.9<br/> 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>Official CPython distributions

To request a specific Python version when you create your function app in Azure, use the `--runtime-version` option of the [`az functionapp create`](/cli/azure/functionapp#az_functionapp_create) command. The Functions runtime version is set by the `--functions-version` option. The Python version is set when the function app is created and can't be changed.

When running locally, the runtime uses the available Python version.

### Changing Python version

To set a Python function app to a specific language version, you  need to specify the language as well as the version of the language in `LinuxFxVersion` field in site config. For example, to change Python app to use Python 3.8, set `linuxFxVersion` to `python|3.8`.

To learn more about Azure Functions runtime support policy, please refer to this [article](./language-support-policy.md)

To see the full list of supported Python versions functions apps, please refer to this [article](./supported-languages.md)



# [Azure CLI](#tab/azurecli-linux)

You can view and set the `linuxFxVersion` from the Azure CLI.  

Using the Azure CLI, view the current `linuxFxVersion` with the [az functionapp config show](/cli/azure/functionapp/config) command.

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

In this code, replace `<function_app>` with the name of your function app. Also replace `<my_resource_group>` with the name of the resource group for your function app. 

You see the `linuxFxVersion` in the following output, which has been truncated for clarity:

```output
{
  ...
  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

You can update the `linuxFxVersion` setting in the function app with the [az functionapp config set](/cli/azure/functionapp/config) command.

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Replace `<FUNCTION_APP>` with the name of your function app. Also replace `<RESOURCE_GROUP>` with the name of the resource group for your function app. Also, replace `<LINUX_FX_VERSION>` with the python version you want to use, prefixed by `python|`  e.g. `python|3.9`

You can run this command from the [Azure Cloud Shell](../cloud-shell/overview.md) by choosing **Try it** in the preceding code sample. You can also use the [Azure CLI locally](/cli/azure/install-azure-cli) to execute this command after executing [az login](/cli/azure/reference-index#az-login) to sign in.

The function app restarts after the change is made to the site config.

--- 


## Package management

When developing locally using the Azure Functions Core Tools or Visual Studio Code, add the names and versions of the required packages to the `requirements.txt` file and install them using `pip`.

For example, the following requirements file and pip command can be used to install the `requests` package from PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## Publishing to Azure

When you're ready to publish, make sure that all your publicly available dependencies are listed in the requirements.txt file, which is located at the root of your project directory.

Project files and folders that are excluded from publishing, including the virtual environment folder, are listed in the .funcignore file.

There are three build actions supported for publishing your Python project to Azure: remote build, local build, and builds using custom dependencies.

You can also use Azure Pipelines to build your dependencies and publish using continuous delivery (CD). To learn more, see [Continuous delivery by using Azure DevOps](functions-how-to-azure-devops.md).

### Remote build

When using remote build, dependencies restored on the server and native dependencies match the production environment. This results in a smaller deployment package to upload. Use remote build when developing Python apps on Windows. If your project has custom dependencies, you can [use remote build with extra index URL](#remote-build-with-extra-index-url).

Dependencies are obtained remotely based on the contents of the requirements.txt file. [Remote build](functions-deployment-technologies.md#remote-build) is the recommended build method. By default, the Azure Functions Core Tools requests a remote build when you use the following [`func azure functionapp publish`](functions-run-local.md#publish) command to publish your Python project to Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Remember to replace `<APP_NAME>` with the name of your function app in Azure.

The [Azure Functions Extension for Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) also requests a remote build by default.

### Local build

Dependencies are obtained locally based on the contents of the requirements.txt file. You can prevent doing a remote build by using the following [`func azure functionapp publish`](functions-run-local.md#publish) command to publish with a local build.

```command
func azure functionapp publish <APP_NAME> --build local
```

Remember to replace `<APP_NAME>` with the name of your function app in Azure.

Using the `--build local` option, project dependencies are read from the requirements.txt file and those dependent packages are downloaded and installed locally. Project files and dependencies are deployed from your local computer to Azure. This results in a larger deployment package being uploaded to Azure. If for some reason, dependencies in your requirements.txt file can't be acquired by Core Tools, you must use the custom dependencies option for publishing.

We don't recommend using local builds when developing locally on Windows.

### Custom dependencies

When your project has dependencies not found in the [Python Package Index](https://pypi.org/), there are two ways to build the project. The build method depends on how you build the project.

#### Remote build with extra index URL

When your packages are available from an accessible custom package index, use a remote build. Before publishing, make sure to [create an app setting](functions-how-to-use-azure-function-app-settings.md#settings) named `PIP_EXTRA_INDEX_URL`. The value for this setting is the URL of your custom package index. Using this setting tells the remote build to run `pip install` using the `--extra-index-url` option. To learn more, see the [Python pip install documentation](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

You can also use basic authentication credentials with your extra package index URLs. To learn more, see [Basic authentication credentials](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) in Python documentation.

#### Install local packages

If your project uses packages not publicly available to our tools, you can make them available to your app by putting them in the \_\_app\_\_/.python_packages directory. Before publishing, run the following command to install the dependencies locally:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

When using custom dependencies, you should use the `--no-build` publishing option, since you have already installed the dependencies into the project folder.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Remember to replace `<APP_NAME>` with the name of your function app in Azure.

## Unit Testing

Functions written in Python can be tested like other Python code using standard testing frameworks. For most bindings, it's possible to create a mock input object by creating an instance of an appropriate class from the `azure.functions` package. Since the [`azure.functions`](https://pypi.org/project/azure-functions/) package is not immediately available, be sure to install it via your `requirements.txt` file as described in the [package management](#package-management) section above.

Take *my_second_function* as an example, following is a mock test of an HTTP triggered function:

First we need to create *<project_root>/my_second_function/function.json* file and define this function as an http trigger.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Now, we can implement the *my_second_function* and the *shared_code.my_second_helper_function*.

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

We can start writing test cases for our http trigger.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

Inside your `.venv` Python virtual environment, install your favorite Python test framework, such as `pip install pytest`. Then run `pytest tests` to check the test result.

## Temporary files

The `tempfile.gettempdir()` method returns a temporary folder, which on Linux is `/tmp`. Your application can use this directory to store temporary files generated and used by your functions during execution.

> [!IMPORTANT]
> Files written to the temporary directory aren't guaranteed to persist across invocations. During scale out, temporary files aren't shared between instances.

The following example creates a named temporary file in the temporary directory (`/tmp`):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

We recommend that you maintain your tests in a folder separate from the project folder. This keeps you from deploying test code with your app.

## Preinstalled libraries

There are a few libraries come with the Python Functions runtime.

### Python Standard Library

The Python Standard Library contains a list of built-in Python modules that are shipped with each Python distribution. Most of these libraries help you access system functionality, like file I/O. On Windows systems, these libraries are installed with Python. On the Unix-based systems, they are provided by package collections.

To view the full details of the list of these libraries, see the links below:

* [Python 3.6 Standard Library](https://docs.python.org/3.6/library/)
* [Python 3.7 Standard Library](https://docs.python.org/3.7/library/)
* [Python 3.8 Standard Library](https://docs.python.org/3.8/library/)
* [Python 3.9 Standard Library](https://docs.python.org/3.9/library/)

### Azure Functions Python worker dependencies

The Functions Python worker requires a specific set of libraries. You can also use these libraries in your functions, but they aren't a part of the Python standard. If your functions rely on any of these libraries, they may not be available to your code when running outside of Azure Functions. You can find a detailed list of dependencies in the **install\_requires** section in the [setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) file.

> [!NOTE]
> If your function app's requirements.txt contains an `azure-functions-worker` entry, remove it. The functions worker is automatically managed by Azure Functions platform, and we regularly update it with new features and bug fixes. Manually installing an old version of worker in requirements.txt may cause unexpected issues.

> [!NOTE]
>  If your package contains certain libraries that may collide with worker's dependencies (e.g. protobuf, tensorflow, grpcio), please configure [`PYTHON_ISOLATE_WORKER_DEPENDENCIES`](functions-app-settings.md#python_isolate_worker_dependencies-preview) to `1` in app settings to prevent your application from referring worker's dependencies. This feature is in preview.

### Azure Functions Python library

Every Python worker update includes a new version of [Azure Functions Python library (azure.functions)](https://github.com/Azure/azure-functions-python-library). This approach makes it easier to continuously update your Python function apps, because each update is backwards-compatible. A list of releases of this library can be found in [azure-functions PyPi](https://pypi.org/project/azure-functions/#history).

The runtime library version is fixed by Azure, and it can't be overridden by requirements.txt. The `azure-functions` entry in requirements.txt is only for linting and customer awareness.

Use the following code to track the actual version of the Python Functions library in your runtime:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### Runtime system libraries

For a list of preinstalled system libraries in Python worker Docker images, see the links below:

|  Functions runtime  | Debian version | Python versions |
|------------|------------|------------|
| Version 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Version 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3.9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## Python worker extensions  

The Python worker process that runs in Azure Functions lets you integrate third-party libraries into your function app. These extension libraries act as middleware that can inject specific operations during the lifecycle of your function's execution. 

Extensions are imported in your function code much like a standard Python library module. Extensions are executed based on the following scopes: 

| Scope | Description |
| --- | --- |
| **Application-level** | When imported into any function trigger, the extension applies to every function execution in the app. |
| **Function-level** | Execution is limited to only the specific function trigger into which it's imported. |

Review the information for a given extension to learn more about the scope in which the extension runs. 

Extensions implement a Python worker extension interface that lets the Python worker process call into the extension code during the function execution lifecycle. To learn more, see [Creating extensions](#creating-extensions).

### Using extensions 

You can use a Python worker extension library in your Python functions by following these basic steps:

1. Add the extension package in the requirements.txt file for your project.
1. Install the library into your app.
1. Add the application setting `PYTHON_ENABLE_WORKER_EXTENSIONS`:
    + Locally: add `"PYTHON_ENABLE_WORKER_EXTENSIONS": "1"` in the `Values` section of your [local.settings.json file](functions-develop-local.md#local-settings-file)
    + Azure: add `PYTHON_ENABLE_WORKER_EXTENSIONS=1` to your [app settings](functions-how-to-use-azure-function-app-settings.md#settings).
1. Import the extension module into your function trigger. 
1. Configure the extension instance, if needed. Configuration requirements should be called-out in the extension's documentation. 

> [!IMPORTANT]
> Third-party Python worker extension libraries are not supported or warrantied by Microsoft. You must make sure that any extensions you use in your function app is trustworthy, and you bear the full risk of using a malicious or poorly written extension. 

Third-parties should provide specific documentation on how to install and consume their specific extension in your function app. For a basic example of how to consume an extension, see [Consuming your extension](develop-python-worker-extensions.md#consume-your-extension-locally). 

Here are examples of using extensions in a function app, by scope:

# [Application-level](#tab/application-level)

```python
# <project_root>/requirements.txt
application-level-extension==1.0.0
```

```python
# <project_root>/Trigger/__init__.py

from application_level_extension import AppExtension
AppExtension.configure(key=value)

def main(req, context):
  # Use context.app_ext_attributes here
```
# [Function-level](#tab/function-level)
```python
# <project_root>/requirements.txt
function-level-extension==1.0.0
```

```python
# <project_root>/Trigger/__init__.py

from function_level_extension import FuncExtension
func_ext_instance = FuncExtension(__file__)

def main(req, context):
  # Use func_ext_instance.attributes here
```
---

### Creating extensions 

Extensions are created by third-party library developers who have created functionality that can be integrated into Azure Functions.  An extension developer designs, implements, and releases Python packages that contain custom logic designed specifically to be run in the context of function execution. These extensions can be published either to the PyPI registry or to GitHub repositories.

To learn how to create, package, publish, and consume a Python worker extension package, see [Develop Python worker extensions for Azure Functions](develop-python-worker-extensions.md).

#### Application-level extensions

An extension inherited from [`AppExtensionBase`](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py) runs in an _application_ scope. 

`AppExtensionBase` exposes the following abstract class methods for you to implement:

| Method | Description |
| --- | --- |
| **`init`** | Called after the extension is imported. |
| **`configure`** | Called from function code when needed to configure the extension. |
| **`post_function_load_app_level`** | Called right after the function is loaded. The function name and function directory are passed to the extension. Keep in mind that the function directory is read-only, and any attempt to write to local file in this directory fails. |
| **`pre_invocation_app_level`** | Called right before the function is triggered. The function context and function invocation arguments are passed to the extension. You can usually pass other attributes in the context object for the function code to consume. |
| **`post_invocation_app_level`** | Called right after the function execution completes. The function context, function invocation arguments, and the invocation return object are passed to the extension. This implementation is a good place to validate whether execution of the lifecycle hooks succeeded. |

#### Function-level extensions

An extension that inherits from [FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py) runs in a specific function trigger. 

`FuncExtensionBase` exposes the following abstract class methods for implementations:

| Method | Description |
| --- | --- |
| **`__init__`** | This method is the constructor of the extension. It's called when an extension instance is initialized in a specific function. When implementing this abstract method, you may want to accept a `filename` parameter and pass it to the parent's method `super().__init__(filename)` for proper extension registration. |
| **`post_function_load`** | Called right after the function is loaded. The function name and function directory are passed to the extension. Keep in mind that the function directory is read-only, and any attempt to write to local file in this directory fails. |
| **`pre_invocation`** | Called right before the function is triggered. The function context and function invocation arguments are passed to the extension. You can usually pass other attributes in the context object for the function code to consume. |
| **`post_invocation`** | Called right after the function execution completes. The function context, function invocation arguments, and the invocation return object are passed to the extension. This implementation is a good place to validate whether execution of the lifecycle hooks succeeded. |

## Cross-origin resource sharing

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS is fully supported for Python function apps.

## Async

By default, a host instance for Python can process only one function invocation at a time. This is because Python is a single-threaded runtime. For a function app that processes a large number of I/O events or is being I/O bound, you can significantly improve performance by running functions asynchronously. For more information, see [Improve throughout performance of Python apps in Azure Functions](python-scale-performance-reference.md#async).

## <a name="shared-memory"></a>Shared memory (preview)

To improve throughput, Functions lets your out-of-process Python language worker share memory with the Functions host process. When your function app is hitting bottlenecks, you can enable shared memory by adding an application setting named [FUNCTIONS_WORKER_SHARED_MEMORY_DATA_TRANSFER_ENABLED](functions-app-settings.md#functions_worker_shared_memory_data_transfer_enabled) with a value of `1`. With shared memory enabled, you can then use the [DOCKER_SHM_SIZE](functions-app-settings.md#docker_shm_size) setting to set the shared memory to something like `268435456`, which is equivalent to 256 MB.

For example, you might enable shared memory to reduce bottlenecks when using Blob storage bindings to transfer payloads larger than 1 MB.

This functionality is available only for function apps running in Premium and Dedicated (App Service) plans. To learn more, see [Shared memory](https://github.com/Azure/azure-functions-python-worker/wiki/Shared-Memory). 

## Known issues and FAQ

Following is a list of troubleshooting guides for common issues:

* [ModuleNotFoundError and ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Cannot import 'cygrpc'](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

All known issues and feature requests are tracked using [GitHub issues](https://github.com/Azure/azure-functions-python-worker/issues) list. If you run into a problem and can't find the issue in GitHub, open a new issue and include a detailed description of the problem.

## Next steps

For more information, see the following resources:

* [Azure Functions package API documentation](/python/api/azure-functions/azure.functions)
* [Best practices for Azure Functions](functions-best-practices.md)
* [Azure Functions triggers and bindings](functions-triggers-bindings.md)
* [Blob storage bindings](functions-bindings-storage-blob.md)
* [HTTP and Webhook bindings](functions-bindings-http-webhook.md)
* [Queue storage bindings](functions-bindings-storage-queue.md)
* [Timer trigger](functions-bindings-timer.md)

[Having issues? Let us know.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse
