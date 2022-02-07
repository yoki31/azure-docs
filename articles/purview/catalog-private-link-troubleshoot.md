---
title: Troubleshooting private endpoint configuration for Azure Purview accounts
description: This article describes how to troubleshoot problems with your Azure Purview account related to private endpoints configurations
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/12/2022
# Customer intent: As an Azure Purview admin, I want to set up private endpoints for my Azure Purview account, for secure access.
---

# Troubleshooting private endpoint configuration for Azure Purview accounts

This guide summarizes known limitations related to using private endpoints for Azure Purview and provides a list of steps and solutions for troubleshooting some of the most common relevant issues. 

## Known limitations

- We currently do not support ingestion private endpoints that work with your AWS sources.
- Scanning Azure Multiple Sources using self-hosted integration runtime is not supported.
- Using Azure integration runtime to scan data sources behind private endpoint is not supported.
- Using Azure portal, the ingestion private endpoints can be created via the Azure Purview portal experience described in the preceding steps. They can't be created from the Private Link Center.
- Creating DNS A records for ingestion private endpoints inside existing Azure DNS Zones, while the Azure Private DNS Zones are located in a different subscription than the private endpoints is not supported via the Azure Purview portal experience. A records can be added manually in the destination DNS Zones in the other subscription. 
- Self-hosted integration runtime machine must be deployed in the same VNet or a peered VNet where Azure Purview account and ingestion private endpoints are deployed.
- We currently do not support scanning a Power BI tenant, which has a private endpoint configured with public access blocked.
- For limitation related to Private Link service, see [Azure Private Link limits](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## Recommended troubleshooting steps  

1. Once you deploy private endpoints for your Azure Purview account, review your Azure environment to make sure private endpoint resources are deployed successfully. Depending on your scenario, one or more of the following Azure private endpoints must be deployed in your Azure subscription:

    |Private endpoint  |Private endpoint assigned to | Example|
    |---------|---------|---------|
    |Account  |Azure Purview Account         |mypurview-private-account  |
    |Portal     |Azure Purview Account         |mypurview-private-portal  |
    |Ingestion     |Managed Storage Account (Blob)         |mypurview-ingestion-blob  |
    |Ingestion     |Managed Storage Account (Queue)         |mypurview-ingestion-queue  |
    |Ingestion     |Managed Event Hubs Namespace         |mypurview-ingestion-namespace  |

2. If portal private endpoint is deployed, make sure you also deploy account private endpoint.

3. If portal private endpoint is deployed, and public network access is set to deny in your Azure Purview account, make sure you launch [Azure Purview Studio](https://web.purview.azure.com/resource/) from internal network.
  <br>
    - To verify the correct name resolution, you can use a **NSlookup.exe** command line tool to query `web.purview.azure.com`. The result must return a private IP address that belongs to portal private endpoint. 
    - To verify network connectivity, you can use any network test tools to test outbound connectivity to `web.purview.azure.com` endpoint to port **443**. The connection must be successful.    

3. If Azure Private DNS Zones are used, make sure the required Azure DNS Zones are deployed and there is DNS (A) record for each private endpoint.

4. Test network connectivity and name resolution from management machine to Azure Purview endpoint and purview web url. If account and portal private endpoints are deployed, the endpoints must be resolved through private IP addresses.


    ```powershell
    Test-NetConnection -ComputerName web.purview.azure.com -Port 443
    ```

    Example of successful outbound connection through private IP address:

    ```
    ComputerName     : web.purview.azure.com
    RemoteAddress    : 10.9.1.7
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName purview-test01.purview.azure.com -Port 443
    ```

    Example of successful outbound connection through private IP address:

    ```
    ComputerName     : purview-test01.purview.azure.com
    RemoteAddress    : 10.9.1.8
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```
    
5. If you have created your Azure Purview account after 18 August 2021, make sure you download and install the latest version of self-hosted integration runtime from [Microsoft download center](https://www.microsoft.com/download/details.aspx?id=39717).
   
6. From self-hosted integration runtime VM, test network connectivity and name resolution to Azure Purview endpoint.

7. From self-hosted integration runtime, test network connectivity and name resolution to Azure Purview managed resources such as blob queue and Event Hub through port 443 and private IP addresses. (Replace the managed storage account and Event Hubs namespace with corresponding managed resource name assigned to your Azure Purview account).

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.blob.core.windows.net -Port 443
    ```
    Example of successful outbound connection to managed blob storage through private IP address:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.6
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.queue.core.windows.net -Port 443
    ```
    Example of successful outbound connection to managed queue storage through private IP address:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.5
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `Atlas-1225cae9-d651-4039-86a0-b43231a17a4b`.servicebus.windows.net -Port 443
    ```
    Example of successful outbound connection to Event Hub namespace through private IP address:

    ```
    ComputerName     : Atlas-1225cae9-d651-4039-86a0-b43231a17a4b.servicebus.windows.net
    RemoteAddress    : 10.15.1.4
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

8. From the network where data source is located, test network connectivity and name resolution to Azure Purview endpoint and managed resources endpoints.

9.  If data sources are located in on-premises network, review your DNS forwarder configuration. Test name resolution from within the same network where data sources are located to self-hosted integration runtime, Azure Purview endpoints and managed resources. It is expected to obtain a valid private IP address from DNS query for each endpoint.
    
    For more information, see [Virtual network workloads without custom DNS server](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) and [On-premises workloads using a DNS forwarder](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) scenarios in [Azure Private Endpoint DNS configuration](../private-link/private-endpoint-dns.md).

10. If management machine and self-hosted integration runtime VMs are deployed in on-premises network and you have set up DNS forwarder in your environment, verify DNS and network settings in your environment. 

11. If ingestion private endpoint is used, make sure self-hosted integration runtime is registered successfully inside Azure Purview account and shows as running both inside the self-hosted integration runtime VM and in the [Azure Purview Studio](https://web.purview.azure.com/resource/) .

## Common errors and messages

### Issue 
You may receive the following error message when running a scan:

`Internal system error. Please contact support with correlationId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx System Error, contact support.`

### Cause 
This can be an indication of issues related to connectivity or name resolution between the VM running self-hosted integration runtime and Azure Purview's managed resources storage account or Event Hub.

### Resolution 
Validate if name resolution between the VM running Self-Hosted Integration Runtime.


### Issue 
You may receive the following error message when running a new scan:

  `message: Unable to setup config overrides for this scan. Exception:'Type=Microsoft.WindowsAzure.Storage.StorageException,Message=The remote server returned an error: (404) Not Found.,Source=Microsoft.WindowsAzure.Storage,StackTrace= at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndExecuteAsync[T](IAsyncResult result)`

### Cause 
This can be an indication of running an older version of self-hosted integration runtime. You'll need to use the self-hosted integration runtime version 5.9.7885.3 or greater.

### Resolution 
Upgrade self-hosted integration runtime to 5.9.7885.3.


### Issue 
Azure Purview account with private endpoint deployment failed with Azure Policy validation error during the deployment.

### Cause
This error suggests that there may be an existing Azure Policy Assignment on your Azure subscription that is preventing the deployment of any of the required Azure resources. 

### Resolution 
Review your existing Azure Policy Assignments and make sure deployment of the following Azure resources are allowed in your Azure subscription. 
   
> [!NOTE]
> Depending on your scenario, you may need to deploy one or more of the following Azure resource types: 
>  -   Azure Purview (Microsoft.Purview/Accounts)
>  -   Private Endpoint (Microsoft.Network/privateEndpoints)
>  -   Private DNS Zones (Microsoft.Network/privateDnsZones)
>  -   Event Hub Name Space (Microsoft.EventHub/namespaces)
>  -   Storage Account (Microsoft.Storage/storageAccounts)


### Issue
Not authorized to access this Azure Purview account. This Azure Purview account is behind a private endpoint. Please access the account from a client in the same virtual network (VNet) that has been configured for the Azure Purview account's private endpoint.

### Cause 
User is trying to connect to Azure Purview from a public endpoint or using Azure Purview public endpoints where **Public network access** is set to **Deny**.

### Resolution
In this case, to open Azure Purview Studio, either use a machine that is deployed in the same virtual network as the Azure Purview portal private endpoint or use a VM that is connected to your CorpNet in which hybrid connectivity is allowed.

### Issue
You may receive the following error message when scanning a SQL server, using a self-hosted integration runtime:

  `Message=This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms`

### Cause 
Self-hosted integration runtime machine has enabled the FIPS mode.
Federal Information Processing Standards (FIPS) defines a certain set of cryptographic algorithms that are allowed to be used. When FIPS mode is enabled on the machine, some cryptographic classes that the invoked processes depends on are blocked in some scenarios.

### Resolution
Disable FIPS mode on self-hosted integration server.

## Next steps

If your problem isn't listed in this article or you can't resolve it, get support by visiting one of
the following channels:

- Get answers from experts through
  [Microsoft Q&A](/answers/topics/azure-purview.html).
- Connect with [@AzureSupport](https://twitter.com/azuresupport). This official Microsoft Azure resource on Twitter helps improve the customer experience by connecting the Azure community to the right answers, support, and experts.
- If you still need help, go to the [Azure support site](https://azure.microsoft.com/support/options/) and select **Submit a support
  request**.
