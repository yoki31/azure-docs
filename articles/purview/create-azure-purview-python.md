---
title: 'Quickstart: Create an Azure Purview account using Python'
description: Create an Azure Purview account using Python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.devlang: python
ms.topic: quickstart
ms.date: 09/27/2021
ms.custom: mode-api
---

# Quickstart: Create an Azure Purview account using Python

In this quickstart, you’ll create an Azure Purview account programatically using Python. [Python reference for Azure Purview](/python/api/azure-mgmt-purview/) is available, but this article will take you through all the steps needed to create an account with Python.

Azure Purview is a data governance service that helps you manage and govern your data landscape. By connecting to data across your on-premises, multi-cloud, and software-as-a-service (SaaS) sources, Azure Purview creates an up-to-date map of your information. It identifies and classifies sensitive data, and provides end-to-end linage. Data consumers are able to discover data across your organization, and data administrators are able to audit, secure, and ensure right use of your data.

For more information about Azure Purview, [see our overview page](overview.md). For more information about deploying Azure Purview across your organization, [see our deployment best practices](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## Install the Python package

1. Open a terminal or command prompt with administrator privileges.
2. First, install the Python package for Azure management resources:

    ```python
    pip install azure-mgmt-resource
    ```

3. To install the Python package for Azure Purview, run the following command:

    ```python
    pip install azure-mgmt-purview
    ```

    The [Python SDK for Azure Purview](https://github.com/Azure/azure-sdk-for-python) supports Python 2.7, 3.3, 3.4, 3.5, 3.6 and 3.7.

4. To install the Python package for Azure Identity authentication, run the following command:

    ```python
    pip install azure-identity
    ```

    > [!NOTE]
    > The "azure-identity" package might have conflicts with "azure-cli" on some common dependencies. If you meet any authentication issue, remove "azure-cli" and its dependencies, or use a clean machine without installing "azure-cli" package.

## Create a purview client

1. Create a file named **purview.py**. Add the following statements to add references to namespaces.

    ```python
    from azure.identity import ClientSecretCredential 
	from azure.mgmt.resource import ResourceManagementClient
	from azure.mgmt.purview import PurviewManagementClient
	from azure.mgmt.purview.models import *
	from datetime import datetime, timedelta
	import time
    ```

2. Add the following code to the **Main** method that creates an instance of PurviewManagementClient class. You'll use this object to create a purview account, delete purview accounts, check name availability, and other resource provider operations.

    ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
	
	# This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Azure Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## Create a purview account

1. Add the following code to the **Main** method that creates a **purview account**. If your resource group already exists, comment out the first `create_or_update` statement.

   ```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
	    pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
	    print("location:", pa.location, " Azure Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
	    print("Error")
	    print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Azure Purview account")
            break
        time.sleep(30)      
      ```

2. Now, add the following statement to invoke the **main** method when the program is run:

   ```python
   # Start the main method
   main()
   ```

## Full script

Here’s the full Python code:

```python
	
	from azure.identity import ClientSecretCredential 
	from azure.mgmt.resource import ResourceManagementClient
	from azure.mgmt.purview import PurviewManagementClient
	from azure.mgmt.purview.models import *
	from datetime import datetime, timedelta
	import time
	
	 # Azure subscription ID
    subscription_id = '<subscription ID>'
	
	# This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
	
	# create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
	    pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
	    print("location:", pa.location, " Azure Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
	    print("Error in submitting job to create account")
	    print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Azure Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## Run the code

Build and start the application. The console prints the progress of Azure Purview account creation. Wait until it’s completed.
Here’s the sample output:

```console
location: southcentralus  Azure Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## Verify the output

Go to the **Azure Purview accounts** page in the Azure portal and verify the account created using the above code.

## Delete Azure Purview account

To delete purview account, add the following code to the program, then run:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## Next steps

The code in this tutorial creates a purview account and  deletes a purview account. You can now download the python SDK and learn about other resource provider actions you can perform for an Azure Purview account.

Follow these next articles to learn how to navigate the Azure Purview Studio, create a collection, and grant access to Azure Purview.

* [How to use the Azure Purview Studio](use-azure-purview-studio.md)
* [Create a collection](quickstart-create-collection.md)
* [Add users to your Azure Purview account](catalog-permissions.md)
