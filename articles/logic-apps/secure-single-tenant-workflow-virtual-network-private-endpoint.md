---
title: Secure traffic between single-tenant workflows and virtual networks
description: Secure traffic between Standard logic app workflows and virtual networks in Azure using private endpoints.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 01/15/2022

# As a developer, I want to connect to my single-tenant logic app workflows with virtual networks using private endpoints and VNet integration.
---

# Secure traffic between single-tenant Standard logic apps and Azure virtual networks using private endpoints and VNet integration

To securely and privately communicate between your workflow in a Standard logic app and an Azure virtual network, you can set up *private endpoints* for inbound traffic and use VNet integration for outbound traffic.

A private endpoint is a network interface that privately and securely connects to a service powered by Azure Private Link. This service can be an Azure service such as Azure Logic Apps, Azure Storage, Azure Cosmos DB, SQL, or your own Private Link Service. The private endpoint uses a private IP address from your virtual network, which effectively brings the service into your virtual network.

This article shows how to set up access through private endpoints for inbound traffic and VNet integration for outbound traffic.

For more information, review the following documentation:

- [What is Azure Private Endpoint?](../private-link/private-endpoint-overview.md) and [Private endpoints - Integrate your app with an Azure virtual network](../app-service/overview-vnet-integration.md#private-endpoints)
- [What is Azure Private Link?](../private-link/private-link-overview.md)
- [What is Vnet integration?](../app-service/networking-features.md#regional-vnet-integration)

## Prerequisites

You need to have a new or existing Azure virtual network that includes a subnet without any delegations. This subnet is used to deploy and allocate private IP addresses from the virtual network.

For more information, review the following documentation:

- [Quickstart: Create a virtual network using the Azure portal](../virtual-network/quick-create-portal.md)
- [What is subnet delegation?](../virtual-network/subnet-delegation-overview.md)
- [Add or remove a subnet delegation](../virtual-network/manage-subnet-delegation.md)

<a name="set-up-inbound"></a>

## Set up inbound traffic through private endpoints

To secure inbound traffic to your workflow, complete these high-level steps:

1. Start your workflow with a built-in trigger that can receive and handle inbound requests, such as the Request trigger or the HTTP + Webhook trigger. This trigger sets up your workflow with a callable endpoint.

1. Add a private endpoint for your logic app resource to your virtual network.

1. Make test calls to check access to the endpoint. To call your logic app workflow after you set up this endpoint, you must be connected to the virtual network.

### Prerequisites for inbound traffic through private endpoints

In addition to the [virtual network setup in the top-level prerequisites](#prerequisites), you need to have a new or existing single-tenant based logic app workflow that starts with a built-in trigger that can receive requests.

For example, the Request trigger creates an endpoint on your workflow that can receive and handle inbound requests from other callers, including workflows. This endpoint provides a URL that you can use to call and trigger the workflow. For this example, the steps continue with the Request trigger.

For more information, review the following documentation:

- [Create single-tenant logic app workflows in Azure Logic Apps](create-single-tenant-workflows-azure-portal.md)
- [Receive and respond to inbound HTTP requests using Azure Logic Apps](../connectors/connectors-native-reqres.md)

### Create the workflow

1. If you haven't already, create a single-tenant based logic app, and a blank workflow.

1. After the designer opens, add the Request trigger as the first step in your workflow.

   > [!NOTE]
   > You can call Request triggers and webhook triggers only from inside your virtual network. 
   > Managed API webhook triggers and actions won't work because they require a public endpoint to receive calls.

1. Based on your scenario requirements, add other actions that you want to run in your workflow.

1. When you're done, save your workflow.

For more information, review [Create single-tenant logic app workflows in Azure Logic Apps](create-single-tenant-workflows-azure-portal.md).

#### Copy the endpoint URL

1. On the workflow menu, select **Overview**.

1. On the **Overview** page, copy and save the **Workflow URL** for later use.

   To trigger the workflow, you call or send a request to this URL.

1. Make sure that the URL works by calling or sending a request to the URL. You can use any tool you want to send the request, for example, Postman.

### Set up private endpoint connection

1. On your logic app menu, under **Settings**, select **Networking**.

1. On the **Networking** page, on the **Inbound traffic** card, select **Private endpoints**.

1. On the **Private Endpoint connections**, select **Add**.

1. On the **Add Private Endpoint** pane that opens, provide the requested information about the endpoint.

   For more information, review [Private Endpoint properties](../private-link/private-endpoint-overview.md#private-endpoint-properties).

1. After Azure successfully provisions the private endpoint, try again to call the workflow URL.

   This time, you get an expected `403 Forbidden` error, which is means that the private endpoint is set up and works correctly.

1. To make sure the connection is working correctly, create a virtual machine in the same virtual network that has the private endpoint, and try calling the logic app workflow.

### Considerations for inbound traffic through private endpoints

- If accessed from outside your virtual network, monitoring view can't access the inputs and outputs from triggers and actions.

- Deployment from Visual Studio Code or Azure CLI works only from inside the virtual network. You can use the Deployment Center to link your logic app to a GitHub repo. You can then use Azure infrastructure to build and deploy your code.

  For GitHub integration to work, remove the `WEBSITE_RUN_FROM_PACKAGE` setting from your logic app or set the value to `0`.

- Enabling Private Link doesn't affect outbound traffic, which still flows through the App Service infrastructure.

<a name="set-up-outbound"></a>

## Set up outbound traffic using VNet integration

To secure outbound traffic from your logic app, you can integrate your logic app with a virtual network. First, create and test an example workflow. You can then set up VNet integration.

> [!IMPORTANT]
> You can't change the subnet size after assignment, so use a subnet that's large enough to accommodate 
> the scale that your app might reach. To avoid any issues with subnet capacity, use a `/26` subnet with 64 addresses. 
> If you create the subnet for virtual network integration with the Azure portal, you must use `/27` as the minimum subnet size.


### Create and test the workflow

1. If you haven't already, in the [Azure portal](https://portal.azure.com), create a single-tenant based logic app, and a blank workflow.

1. After the designer opens, add the Request trigger as the first step in your workflow.

1. Add an HTTP action to call an internal service that's unavailable through the Internet and runs with a private IP address such as `10.0.1.3`.

1. When you're done, save your workflow.

1. From the designer, manually run the workflow.

   The HTTP action fails, which is by design and expected because the workflow runs in the cloud and can't access your internal service.

### Set up VNet integration

1. In the Azure portal, on the logic app resource menu, under **Settings**, select **Networking**.

1. On the **Networking** pane,  on the **Outbound traffic** card, select **VNet integration**.

1. On the **VNet Integration** pane, select **Add Vnet**.

1. On the **Add VNet Integration** pane, select the subscription and the virtual network that connects to your internal service.

1. If you use your own domain name server (DNS) with your virtual network, set your logic app resource's `WEBSITE_DNS_SERVER` app setting to the IP address for your DNS. If you have a secondary DNS, add another app setting named `WEBSITE_DNS_ALT_SERVER`, and set the value also to the IP for your DNS.

1. After Azure successfully provisions the VNet integration, try to run the workflow again.

   The HTTP action now runs successfully.

> [!IMPORTANT]
> For the Azure Logic Apps runtime to work, you need to have an uninterrupted connection to the backend storage. 
> For Azure-hosted managed connectors to work, you need to have an uninterrupted connection to the managed API service.
> With VNet integration, you need to make sure no firewall or network security policy is blocking these connections. 

### Considerations for outbound traffic through VNet integration

Setting up virtual network integration affects only outbound traffic. To secure inbound traffic, which continues to use the App Service shared endpoint, review [Set up inbound traffic through private endpoints](#set-up-inbound).

For more information, review the following documentation:

- [Integrate your app with an Azure virtual network](../app-service/overview-vnet-integration.md)
- [Network security groups](../virtual-network/network-security-groups-overview.md)
- [Virtual network traffic routing](../virtual-network/virtual-networks-udr-overview.md)

## Next steps

- [Logic Apps Anywhere: Networking possibilities with Logic Apps (single-tenant)](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)