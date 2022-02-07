---
title: "Tutorial: Publish Azure Static Web Apps with Azure DevOps"
description: Learn to use Azure DevOps to publish Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic:  tutorial
ms.date: 08/17/2021
ms.author: apedward

---

# Tutorial: Publish Azure Static Web Apps with Azure DevOps

This article demonstrates how to deploy to [Azure Static Web Apps](./overview.md) using [Azure DevOps](https://dev.azure.com/).

In this tutorial, you learn to:

- Set up an Azure Static Web Apps site
- Create an Azure Pipeline to build and publish a static web app

## Prerequisites

- **Active Azure account:** If you don't have one, you can [create an account for free](https://azure.microsoft.com/free/).
- **Azure DevOps project:** If you don't have one, you can [create a project for free](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
  - Azure DevOps includes **Azure Pipelines**. If you need help getting started with Azure Pipelines, see [Create your first pipeline](/azure/devops/pipelines/create-first-pipeline?preserve-view=true&view=azure-devops).
  - The Static Web App Pipeline Task currently only works on **Linux** machines. When running the pipeline mentioned below, please ensure it is running on a Linux VM.

## Create a static web app in an Azure DevOps

  > [!NOTE]
  > If you have an existing app in your repository, you may skip to the next section.

1. Navigate to your repository in Azure Repos.

1. Select **Import** to begin importing a sample application.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps Repo":::

1. In **Clone URL**, enter `https://github.com/staticwebdev/vanilla-api.git`.

1. Select **Import**.

## Create a static web app

1. Navigate to the [Azure portal](https://portal.azure.com).

1. Select **Create a Resource**.

1. Search for **Static Web Apps**.

1. Select **Static Web Apps**.

1. Select **Create**.

1. Create a new static web app with the following values.

    :::image type="content" source="media/publish-devops/azure-portal-static-web-apps-devops.png" alt-text="Deployment details - other":::

    | Setting | Value |
    |---|---|
    | Subscription | Your Azure subscription name. |
    | Resource Group | Select an existing group name, or create a new one. |
    | Name | Enter **myDevOpsApp**. |
    | Hosting plan type | Select **Free**. |
    | Region | Select a region closest to you. |
    | Source | Select **Other**. |

1. Select **Review + create**

1. Select **Create**.

1. Once the deployment is successful, select **Go to resource**.

1. Select **Manage deployment token**.

1. Copy the **deployment token** and paste the deployment token value into a text editor for use in another screen.

    > [!NOTE]
    > This value is set aside for now because you'll copy and paste more values in coming steps.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Deployment token":::

## Create the Pipeline Task in Azure DevOps

1. Navigate to the repository in Azure Repos that was created earlier.

2. Select **Set up build**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Build pipeline":::

3. In the *Configure your pipeline* screen, select **Starter pipeline**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Configure pipeline":::

4. Copy the following YAML and replace the generated configuration in your pipeline with this code.

    ```yaml
    trigger:
      - main

    pool:
      vmImage: ubuntu-latest

    steps:
      - checkout: self
        submodules: true
      - task: AzureStaticWebApp@0
        inputs:
          app_location: '/src'
          api_location: 'api'
          output_location: '/src'
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > If you are not using the sample app, the values for `app_location`, `api_location`, and `output_location` need  to change to match the values in your application.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    The `azure_static_web_apps_api_token` value is self managed and is manually configured.

5. Select **Variables**.

6. Select **New variable**.

7. Name the variable **deployment_token** (matching the name in the workflow).

8. Copy the deployment token that you previously pasted into a text editor.

9. Paste in the deployment token in the _Value_ box.

    :::image type="content" source="media/publish-devops/yaml-token.png" alt-text="Variable token" lightbox="media/publish-devops/yaml-token.png":::

10. Select **Keep this value secret**.

11. Select **OK**.

12. Select **Save** to return to your pipeline YAML.

13. Select **Save and run** to open the _Save and run_ dialog.

    :::image type="content" source="media/publish-devops/yaml-save.png" alt-text="Pipeline" lightbox="media/publish-devops/yaml-save.png":::

14. Select **Save and run** to run the pipeline.

15. Once the deployment is successful, navigate to the Azure Static Web Apps **Overview** which includes links to the deployment configuration. Note how the _Source_ link now points to the branch and location of the Azure DevOps repository.

16. Select the **URL** to see your newly deployed website.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Deployment location":::

## Clean up resources

Clean up the resources you deployed by deleting the resource group.

1. From the Azure portal, select **Resource group** from the left menu.
2. Enter the resource group name in the **Filter by name** field.
3. Select the resource group name you used in this tutorial.
4. Select **Delete resource group** from the top menu.

## Next steps

> [!div class="nextstepaction"]
> [Configure Azure Static Web Apps](./configuration.md)
