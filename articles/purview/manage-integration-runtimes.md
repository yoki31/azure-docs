---
title: Create and manage Integration Runtimes
description: This article explains the steps to create and manage Integration Runtimes in Azure Purview.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 01/27/2022
---

# Create and manage a self-hosted integration runtime

The integration runtime (IR) is the compute infrastructure that Azure Purview uses to power data scan across different network environments.

A self-hosted integration runtime (SHIR) can be used to scan data source in an on-premises network or a virtual network. The installation of a self-hosted integration runtime needs an on-premises machine or a virtual machine inside a private network.

This article describes how to create and manage a self-hosted integration runtime.

> [!NOTE]
> The Azure Purview Integration Runtime cannot be shared with an Azure Synapse Analytics or Azure Data Factory Integration Runtime on the same machine. It needs to be installed on a separated machine.

## Prerequisites

- The supported versions of Windows are:
  - Windows 8.1
  - Windows 10
  - Windows 11
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019
  - Windows Server 2022

Installation of the self-hosted integration runtime on a domain controller isn't supported.

- Self-hosted integration runtime requires a 64-bit Operating System with .NET Framework 4.7.2 or above. See [.NET Framework System Requirements](/dotnet/framework/get-started/system-requirements) for details.
- The recommended minimum configuration for the self-hosted integration runtime machine is a 2-GHz processor with 4 cores, 8 GB of RAM, and 80 GB of available hard drive space. For the details of system requirements, see [Download](https://www.microsoft.com/download/details.aspx?id=39717).
- If the host machine hibernates, the self-hosted integration runtime doesn't respond to data requests. Configure an appropriate power plan on the computer before you install the self-hosted integration runtime. If the machine is configured to hibernate, the self-hosted integration runtime installer prompts with a message.
- You must be an administrator on the machine to successfully install and configure the self-hosted integration runtime.
- Scan runs happen with a specific frequency per the schedule you've set up. Processor and RAM usage on the machine follows the same pattern with peak and idle times. Resource usage also depends heavily on the amount of data that is scanned. When multiple scan jobs are in progress, you see resource usage goes up during peak times.
- Scanning some data sources requires additional setup on the self-hosted integration runtime machine. For example, JDK, Visual C++ Redistributable, or specific driver. Refer to [each source article](azure-purview-connector-overview.md) for prerequisite details.

> [!IMPORTANT]
> If you use the Self-Hosted Integration runtime to scan Parquet files, you need to install the **64-bit JRE 8 (Java Runtime Environment) or OpenJDK** on your IR machine. Check our [Java Runtime Environment section at the bottom of the page](#java-runtime-environment-installation) for an installation guide.

### Considerations for using a self-hosted IR

- You can use a single self-hosted integration runtime for scanning multiple data sources.
- You can install only one instance of self-hosted integration runtime on any single machine. If you have two Azure Purview accounts that need to scan on-premises data sources, install the self-hosted IR on two machines, one for each Azure Purview account.
- The self-hosted integration runtime doesn't need to be on the same machine as the data source, unless specially called out as a prerequisite in the respective source article. Having the self-hosted integration runtime close to the data source reduces the time for the self-hosted integration runtime to connect to the data source.

## Setting up a self-hosted integration runtime

To create and set up a self-hosted integration runtime, use the following procedures.

### Create a self-hosted integration runtime

1. On the home page of the [Azure Purview Studio](https://web.purview.azure.com/resource/), select **Data Map** from the left navigation pane.

2. Under **Sources and scanning** on the left pane, select **Integration runtimes**, and then select **+ New**.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Select on IR.":::

3. On the **Integration runtime setup** page, select **Self-Hosted** to create a self-Hosted IR, and then select **Continue**.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Create new SHIR.":::

4. Enter a name for your IR, and select Create.

5. On the **Integration Runtime settings** page, follow the steps under the **Manual setup** section. You will have to download the integration runtime from the download site onto a VM or machine where you intend to run it.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="get key":::

   - Copy and paste the authentication key.

   - Download the self-hosted integration runtime from [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) on a local Windows machine. Run the installer. Self-hosted integration runtime versions such as 5.4.7803.1 and 5.6.7795.1 are supported. 

   - On the **Register Integration Runtime (Self-hosted)** page, paste one of the two keys you saved earlier, and select **Register**.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="input key.":::

   - On the **New Integration Runtime (Self-hosted) Node** page, select **Finish**.

6. After the Self-hosted integration runtime is registered successfully, you see the following window:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="successfully registered.":::

## Manage a self-hosted integration runtime

You can edit a self-hosted integration runtime by navigating to **Integration runtimes** in the **Management center**, selecting the IR and then selecting edit. You can now update the description, copy the key, or regenerate new keys.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="edit IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="edit IR details.":::

You can delete a self-hosted integration runtime by navigating to **Integration runtimes** in the Management center, selecting the IR and then selecting **Delete**. Once an IR is deleted, any ongoing scans relying on it will fail.

## Service account for Self-hosted integration runtime

The default logon service account of self-hosted integration runtime is **NT SERVICE\DIAHostService**. You can see it in **Services -> Integration Runtime Service -> Properties -> Log on**.

:::image type="content" source="../data-factory/media/create-self-hosted-integration-runtime/shir-service-account.png" alt-text="Service account for self-hosted integration runtime":::

Make sure the account has the permission of Log on as a service. Otherwise self-hosted integration runtime can't start successfully. You can check the permission in **Local Security Policy -> Security Settings -> Local Policies -> User Rights Assignment -> Log on as a service**

:::image type="content" source="../data-factory/media/create-self-hosted-integration-runtime/shir-service-account-permission.png" alt-text="Screenshot of Local Security Policy - User Rights Assignment":::

:::image type="content" source="../data-factory/media/create-self-hosted-integration-runtime/shir-service-account-permission-2.png" alt-text="Screenshot of Log on as a service user rights assignment":::

## Notification area icons and notifications

If you move your cursor over the icon or message in the notification area, you can see details about the state of the self-hosted integration runtime.

:::image type="content" source="../data-factory/media/create-self-hosted-integration-runtime/system-tray-notifications.png" alt-text="Notifications in the notification area":::

## Networking requirements

Your self-hosted integration runtime machine needs to connect to several resources to work correctly:

* The Azure Purview services used to manage the self-hosted integration runtime.
* The data sources you want to scan using the self-hosted integration runtime.
* The managed Storage account and Event Hubs resource created by Azure Purview. Azure Purview uses these resources to ingest the results of the scan, among many other things, so the self-hosted integration runtime need to be able to connect with these resources.
* The Azure Key Vault used to store credentials.

There are two firewalls to consider:

- The *corporate firewall* that runs on the central router of the organization
- The *Windows firewall* that is configured as a daemon on the local machine where the self-hosted integration runtime is installed

Here are the domains and outbound ports that you need to allow at both **corporate and Windows/machine firewalls**.

> [!TIP]
> For domains listed with '\<managed_storage_account>' and '\<managed_Event_Hub_resource>', add the name of the managed resources associated with your Azure Purview account. You can find them from Azure portal -> your Azure Purview account -> Managed resources tab.

| Domain names                  | Outbound ports | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | Required for interactive authoring, for example, test connection on Azure Purview Studio. Currently wildcard is required as there is no dedicated resource. |
| `*.frontend.clouddatahub.net` | 443 | Required to connect to the Azure Purview service. Currently wildcard is required as there is no dedicated resource. |
| `<managed_storage_account>.blob.core.windows.net` | 443 | Required to connect to the Azure Purview managed Azure Blob storage account. |
| `<managed_storage_account>.queue.core.windows.net` | 443 | Required to connect to the Azure Purview managed Azure Queue storage account. |
| `<managed_Event_Hub_resource>.servicebus.windows.net` | 443            | Azure Purview uses this to connect with the associated service bus. It's covered by allowing the above domain. If you use private endpoint, you need to test access to this single domain.|
| `download.microsoft.com` | 443           | Required to download the self-hosted integration runtime updates. If you have disabled auto-update, you can skip configuring this domain. |
| `login.windows.net`<br>`login.microsoftonline.com` | 443 | Required to sign in to the Azure Active Directory. |

Depending on the sources you want to scan, you also need to allow additional domains and outbound ports for other Azure or external sources. A few examples are provided here:

| Domain names                  | Outbound ports | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<your_key_vault_name>.vault.azure.net` | 443 | Required if any credentials are stored in Azure Key Vault. |
| `<your_storage_account>.dfs.core.windows.net` | 443 | When scan Azure Data Lake Store Gen 2. |
| `<your_storage_account>.blob.core.windows.net` | 443            | When scan Azure Blob storage. |
| `<your_sql_server>.database.windows.net` | 1433           | When scan Azure SQL Database. |
| `<your_ADLS_account>.azuredatalakestore.net` | 443            | When scan Azure Data Lake Store Gen 1. |
| Various domains | Dependent | Domains and ports for any other sources the SHIR will scan. |

For some cloud data stores such as Azure SQL Database and Azure Storage, you need to allow IP address of self-hosted integration runtime machine on their firewall configuration.

> [!IMPORTANT]
> In most environments, you will also need to make sure that your DNS is correctly configured. To confirm, you can use **nslookup** from your SHIR machine to check connectivity to each of the domains. Each nslookup should return the IP of the resource. If you are using [Private Endpoints](catalog-private-link.md), the private IP should be returned and not the Public IP. If no IP is returned, or if when using Private Endpoints the public IP is returned, you need to address your DNS/VNet association, or your Private Endpoint/VNet peering.

## Proxy server considerations

If your corporate network environment uses a proxy server to access the internet, configure the self-hosted integration runtime to use appropriate proxy settings. You can set the proxy during the initial registration phase or after it is being registered.

:::image type="content" source="media/manage-integration-runtimes/self-hosted-proxy.png" alt-text="Specify the proxy":::

When configured, the self-hosted integration runtime uses the proxy server to connect to the services which use HTTP or HTTPS protocol. This is why you select **Change link** during initial setup.

:::image type="content" source="media/manage-integration-runtimes/set-http-proxy.png" alt-text="Set the proxy":::

There are two supported configuration options by Azure Purview:

- **Do not use proxy**: The self-hosted integration runtime doesn't explicitly use any proxy to connect to cloud services.
- **Use system proxy**: The self-hosted integration runtime uses the proxy setting that is configured in the executable's configuration files. If no proxy is specified in these files, the self-hosted integration runtime connects to the services directly without going through a proxy.

> [!IMPORTANT]
>
> Currently, **custom proxy** is not supported in Azure Purview. In addition, system proxy is supported when scanning Azure data sources and SQL Server; scanning other sources doesn't support proxy.

The integration runtime host service restarts automatically after you save the updated proxy settings.

After you register the self-hosted integration runtime, if you want to view or update proxy settings, use Microsoft Integration Runtime Configuration Manager.

1. Open **Microsoft Integration Runtime Configuration Manager**.
1. Select the **Settings** tab.
3. Under **HTTP Proxy**, select the **Change** link to open the **Set HTTP Proxy** dialog box.
4. Select **Next**. You then see a warning that asks for your permission to save the proxy setting and restart the integration runtime host service.

> [!NOTE]
> If you set up a proxy server with NTLM authentication, the integration runtime host service runs under the domain account. If you later change the password for the domain account, remember to update the configuration settings for the service and restart the service. Because of this requirement, we suggest that you access the proxy server by using a dedicated domain account that doesn't require you to update the password frequently.

If using system proxy, make sure your proxy server allow outbound traffic to the [network rules](#networking-requirements).

### Configure proxy server settings

If you select the **Use system proxy** option for the HTTP proxy, the self-hosted integration runtime uses the proxy settings in the following four files under the path C:\Program Files\Microsoft Integration Runtime\5.0\ to perform different operations:

- .\Shared\diahost.exe.config
- .\Shared\diawp.exe.config
- .\Gateway\DataScan\Microsoft.DataMap.Agent.exe.config
- .\Gateway\DataScan\DataTransfer\Microsoft.DataMap.Agent.Connectors.Azure.DataFactory.ServiceHost.exe.config

When no proxy is specified in these files, the self-hosted integration runtime connects to the services directly without going through a proxy. 

The following procedure provides instructions for updating the **diahost.exe.config** file.

1. In File Explorer, make a safe copy of C:\Program Files\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config as a backup of the original file.

1. Open Notepad running as administrator.

1. In Notepad, open the text file C:\Program Files\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config.

1. Find the default **system.net** tag as shown in the following code:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    You can then add proxy server details as shown in the following example:

    ```xml
    <system.net>
      <defaultProxy>
        <proxy bypassonlocal="true" proxyaddress="<your proxy server e.g. http://proxy.domain.org:8888/>" />
      </defaultProxy>
    </system.net>
    ```
    The proxy tag allows additional properties to specify required settings like `scriptLocation`. See [\<proxy\> Element (Network Settings)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) for syntax.
    
    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
    
1. Save the configuration file in its original location. 


Repeat the same procedure to update **diawp.exe.config** and **Microsoft.DataMap.Agent.exe.config** files. 

Then go to path C:\Program Files\Microsoft Integration Runtime\5.0\Gateway\DataScan\DataTransfer, create a file named "**Microsoft.DataMap.Agent.Connectors.Azure.DataFactory.ServiceHost.exe.config**", and configure the proxy setting as follows. You can also extend the settings as described above.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.net>
    <defaultProxy>
      <proxy bypassonlocal="true" proxyaddress="<your proxy server e.g. http://proxy.domain.org:8888/>" />
    </defaultProxy>
  </system.net>
</configuration>
```

Restart the self-hosted integration runtime host service, which picks up the changes. To restart the service, use the services applet from Control Panel. Or from Integration Runtime Configuration Manager, select the **Stop Service** button, and then select **Start Service**. If the service doesn't start, you likely added incorrect XML tag syntax in the application configuration file that you edited.

> [!IMPORTANT]
> Don't forget to update all four files mentioned above.

You also need to make sure that Microsoft Azure is in your company's allowlist. You can download the list of valid Azure IP addresses. IP ranges for each cloud, broken down by region and by the tagged services in that cloud are now available on MS Download: 
   - Public: https://www.microsoft.com/download/details.aspx?id=56519

### Possible symptoms for issues related to the firewall and proxy server

If you see error messages like the following ones, the likely reason is improper configuration of the firewall or proxy server. Such configuration prevents the self-hosted integration runtime from connecting to Azure Purview services. To ensure that your firewall and proxy server are properly configured, refer to the previous section.

- When you try to register the self-hosted integration runtime, you receive the following error message: "Failed to register this Integration Runtime node! Confirm that the Authentication key is valid and the integration service host service is running on this machine."
- When you open Integration Runtime Configuration Manager, you see a status of **Disconnected** or **Connecting**. When you view Windows event logs, under **Event Viewer** > **Application and Services Logs** > **Microsoft Integration Runtime**, you see error messages like this one:

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted)
  ```

## Java Runtime Environment Installation

If you scan Parquet files using the self-hosted integration runtime with Azure Purview, you will need to install either the Java Runtime Environment or OpenJDK on your self-hosted IR machine.

When scanning Parquet files using the self-hosted IR, the service locates the Java runtime by firstly checking the registry *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* for JRE, if not found, secondly checking system variable *`JAVA_HOME`* for OpenJDK.

- **To use JRE**: The 64-bit IR requires 64-bit JRE. You can find it from [here](https://go.microsoft.com/fwlink/?LinkId=808605).
- **To use OpenJDK**: It's supported since IR version 3.13. Package the jvm.dll with all other required assemblies of OpenJDK into self-hosted IR machine, and set system environment variable JAVA_HOME accordingly.

## Next steps

- [Azure Purview network architecture and best practices](concept-best-practices-network.md)

- [Use private endpoints with Azure Purview](catalog-private-link.md)

