---
title: 'Tutorial: Access data with managed identity'
description: Learn how to make database connectivity more secure by using a managed identity, and also how to apply it to other Azure services.

ms.devlang: csharp
ms.topic: tutorial
ms.date: 01/27/2022
ms.custom: "devx-track-csharp, mvc, cli-validate, devx-track-azurecli"
---
# Tutorial: Connect to SQL Database from App Service without secrets using a managed identity

[App Service](overview.md) provides a highly scalable, self-patching web hosting service in Azure. It also provides a [managed identity](overview-managed-identity.md) for your app, which is a turn-key solution for securing access to [Azure SQL Database](/azure/sql-database/) and other Azure services. Managed identities in App Service make your app more secure by eliminating secrets from your app, such as credentials in the connection strings. In this tutorial, you'll add managed identity to the sample web app you built in one of the following tutorials: 

- [Tutorial: Build an ASP.NET app in Azure with Azure SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutorial: Build an ASP.NET Core and Azure SQL Database app in Azure App Service](tutorial-dotnetcore-sqldb-app.md)

When you're finished, your sample app will connect to SQL Database securely without the need of username and passwords.

![Architecture diagram for tutorial scenario.](./media/tutorial-connect-msi-sql-database/architecture.png)

> [!NOTE]
> The steps covered in this tutorial support the following versions:
> 
> - .NET Framework 4.8 and above
> - .NET 6.0 and above
>

What you will learn:

> [!div class="checklist"]
> * Enable managed identities
> * Grant SQL Database access to the managed identity
> * Configure Entity Framework to use Azure AD authentication with SQL Database
> * Connect to SQL Database from Visual Studio using Azure AD authentication

> [!NOTE]
>Azure AD authentication is _different_ from [Integrated Windows authentication](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) in on-premises Active Directory (AD DS). AD DS and Azure AD use completely different authentication protocols. For more information, see [Azure AD Domain Services documentation](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

This article continues where you left off in either one of the following tutorials:

- [Tutorial: Build an ASP.NET app in Azure with SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](tutorial-dotnetcore-sqldb-app.md).

If you haven't already, follow one of the two tutorials first. Alternatively, you can adapt the steps for your own .NET app with SQL Database.

To debug your app using SQL Database as the back end, make sure that you've allowed client connection from your computer. If not, add the client IP by following the steps at [Manage server-level IP firewall rules using the Azure portal](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

Prepare your environment for the Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## 1. Grant database access to Azure AD user

First, enable Azure Active Directory authentication to SQL Database by assigning an Azure AD user as the admin of the server. This user is different from the Microsoft account you used to sign up for your Azure subscription. It must be a user that you created, imported, synced, or invited into Azure AD. For more information on allowed Azure AD users, see [Azure AD features and limitations in SQL Database](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

1. If your Azure AD tenant doesn't have a user yet, create one by following the steps at [Add or delete users using Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Find the object ID of the Azure AD user using the [`az ad user list`](/cli/azure/ad/user#az_ad_user_list) and replace *\<user-principal-name>*. The result is saved to a variable.

    ```azurecli-interactive
    azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
    ```

    > [!TIP]
    > To see the list of all user principal names in Azure AD, run `az ad user list --query [].userPrincipalName`.
    >

1. Add this Azure AD user as an Active Directory admin using [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) command in the Cloud Shell. In the following command, replace *\<server-name>* with the server name (without the `.database.windows.net` suffix).

    ```azurecli-interactive
    az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
    ```

For more information on adding an Active Directory admin, see [Provision an Azure Active Directory administrator for your server](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## 2. Set up your dev environment

# [Visual Studio Windows](#tab/windowsclient)

1. Visual Studio for Windows is integrated with Azure AD authentication. To enable development and debugging in Visual Studio, add your Azure AD user in Visual Studio by selecting **File** > **Account Settings** from the menu, and select **Sign in** or **Add**.

1. To set the Azure AD user for Azure service authentication, select **Tools** > **Options** from the menu, then select **Azure Service Authentication** > **Account Selection**. Select the Azure AD user you added and select **OK**.

# [Visual Studio for macOS](#tab/macosclient)

1. Visual Studio for Mac is *not* integrated with Azure AD authentication. However, the Azure Identity client library that you'll use later can use tokens from Azure CLI. To enable development and debugging in Visual Studio, [install Azure CLI](/cli/azure/install-azure-cli) on your local machine.

1. Sign in to Azure CLI with the following command using your Azure AD user:

    ```azurecli
    az login --allow-no-subscriptions
    ```

# [Visual Studio Code](#tab/vscode)

1. Visual Studio Code is integrated with Azure AD authentication through the Azure extension. Install the <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure Tools</a> extension in Visual Studio Code.

1. In Visual Studio Code, in the [Activity Bar](https://code.visualstudio.com/docs/getstarted/userinterface), select the **Azure** logo.

1. In the **App Service** explorer, select **Sign in to Azure...** and follow the instructions.

# [Azure CLI](#tab/cli)

1. The Azure Identity client library that you'll use later can use tokens from Azure CLI. To enable command-line based development, [install Azure CLI](/cli/azure/install-azure-cli) on your local machine.

1. Sign in to Azure with the following command using your Azure AD user:

    ```azurecli
    az login --allow-no-subscriptions
    ```

# [Azure PowerShell](#tab/ps)

1. The Azure Identity client library that you'll use later can use tokens from Azure PowerShell. To enable command-line based development, [install Azure PowerShell](/powershell/azure/install-az-ps) on your local machine.

1. Sign in to Azure CLI with the following cmdlet using your Azure AD user:

    ```powershell-interactive
    Connect-AzAccount
    ```

-----

For more information about setting up your dev environment for Azure Active Directory authentication, see [Azure Identity client library for .NET](/dotnet/api/overview/azure/Identity-readme).

You're now ready to develop and debug your app with the SQL Database as the back end, using Azure AD authentication.

## 3. Modify your project

> [!NOTE]
> **Microsoft.Azure.Services.AppAuthentication** is no longer recommended to use with new Azure SDK. 
> It is replaced with new **Azure Identity client library** available for .NET, Java, TypeScript and Python and should be used for all new development. 
> Information about how to migrate to `Azure Identity`can be found here: [AppAuthentication to Azure.Identity Migration Guidance](/dotnet/api/overview/azure/app-auth-migration).

The steps you follow for your project depends on whether you're using [Entity Framework](/ef/ef6/) (default for ASP.NET) or [Entity Framework Core](/ef/core/) (default for ASP.NET Core).

# [Entity Framework](#tab/ef)

1. In Visual Studio, open the Package Manager Console and add the NuGet package [Azure.Identity](https://www.nuget.org/packages/Azure.Identity) and update Entity Framework:

    ```powershell
    Install-Package Azure.Identity -Version 1.5.0
    Update-Package EntityFramework
    ```

1. In your DbContext object (in *Models/MyDbContext.cs*), add the following code to the default constructor.

    ```csharp
    var conn = (System.Data.SqlClient.SqlConnection)Database.Connection;
    var credential = new Azure.Identity.DefaultAzureCredential();
    var token = credential.GetToken(new Azure.Core.TokenRequestContext(new[] { "https://database.windows.net/.default" }));
    conn.AccessToken = token.Token;
    ```
    
    This code uses [Azure.Identity.DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) to get a useable token for SQL Database from Azure Active Directory and then adds it to the database connection. While you can customize `DefaultAzureCredential`, by default it's already very versatile. When running in App Service, it uses app's system-assigned managed identity. When running locally, it can get a token using the logged-in identity of Visual Studio, Visual Studio Code, Azure CLI, and Azure PowerShell.

1. In *Web.config*, find the connection string called `MyDbConnection` and replace its `connectionString` value with `"server=tcp:<server-name>.database.windows.net;database=<db-name>;"`. Replace _\<server-name>_ and _\<db-name>_ with your server name and database name. This connection string is used by the default constructor in *Models/MyDbContext.cs*.
    
    That's every thing you need to connect to SQL Database. When debugging in Visual Studio, your code uses the Azure AD user you configured in [2. Set up your dev environment](#2-set-up-your-dev-environment). You'll set up SQL Database later to allow connection from the managed identity of your App Service app.

1. Type `Ctrl+F5` to run the app again. The same CRUD app in your browser is now connecting to the Azure SQL Database directly, using Azure AD authentication. This setup lets you run database migrations from Visual Studio.

# [Entity Framework Core](#tab/efcore)

1. In Visual Studio, open the Package Manager Console and add the NuGet package [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient):

    ```powershell
    Install-Package Microsoft.Data.SqlClient -Version 4.0.1
    ```

1. In the [ASP.NET Core and SQL Database tutorial](tutorial-dotnetcore-sqldb-app.md), the `MyDbConnection` connection string in *appsettings.json* isn't used at all yet. The local environment and the Azure environment both get connection strings from their respective environment variables in order to keep connection secrets out of the source file. But now with Active Directory authentication, there are no more secrets. In *appsettings.json*, replace the value of the `MyDbConnection` connection string with:

    ```json
    "Server=tcp:<server-name>.database.windows.net;Authentication=Active Directory Default; Database=<database-name>;"
    ```

    > [!NOTE]
    > The [Active Directory Default](/sql/connect/ado-net/sql/azure-active-directory-authentication#using-active-directory-default-authentication) authentication type can be used both on your local machine and in Azure App Service. The driver attempts to acquire a token from Azure Active Directory using various means. If the app is deployed, it gets a token from the app's managed identity. If the app is running locally, it tries to get a token from Visual Studio, Visual Studio Code, and Azure CLI.
    >

    That's everything you need to connect to SQL Database. When debugging in Visual Studio, your code uses the Azure AD user you configured in [2. Set up your dev environment](#2-set-up-your-dev-environment). You'll set up SQL Database later to allow connection from the managed identity of your App Service app. The `DefaultAzureCredential` class caches the token in memory and retrieves it from Azure AD just before expiration. You don't need any custom code to refresh the token.

1. Type `Ctrl+F5` to run the app again. The same CRUD app in your browser is now connecting to the Azure SQL Database directly, using Azure AD authentication. This setup lets you run database migrations from Visual Studio.

-----

## 4. Use managed identity connectivity

Next, you configure your App Service app to connect to SQL Database with a system-assigned managed identity.

> [!NOTE]
> While the instructions in this section are for a system-assigned identity, a user-assigned identity can just as easily be used. To do this. you would need the change the `az webapp identity assign command` to assign the desired user-assigned identity. Then, when creating the SQL user, make sure to use the name of the user-assigned identity resource rather than the site name.

### Enable managed identity on app

To enable a managed identity for your Azure app, use the [az webapp identity assign](/cli/azure/webapp/identity#az_webapp_identity_assign) command in the Cloud Shell. In the following command, replace *\<app-name>*.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

> [!NOTE]
> To enable managed identity for a [deployment slot](deploy-staging-slots.md), add `--slot <slot-name>` and use the name of the slot in *\<slot-name>*.

Here's an example of the output:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### Grant permissions to managed identity

> [!NOTE]
> If you want, you can add the identity to an [Azure AD group](../active-directory/fundamentals/active-directory-manage-groups.md), then grant SQL Database access to the Azure AD group instead of the identity. For example, the following commands add the managed identity from the previous step to a new group called _myAzureSQLDBAccessGroup_:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

1. In the Cloud Shell, sign in to SQL Database by using the SQLCMD command. Replace _\<server-name>_ with your server name, _\<db-name>_ with the database name your app uses, and _\<aad-user-name>_ and _\<aad-password>_ with your Azure AD user's credentials.

    ```bash
    sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
    ```

1. In the SQL prompt for the database you want, run the following commands to grant the permissions your app needs. For example, 

    ```sql
    CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
    ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
    ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
    ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
    GO
    ```

    *\<identity-name>* is the name of the managed identity in Azure AD. If the identity is system-assigned, the name is always the same as the name of your App Service app. For a [deployment slot](deploy-staging-slots.md), the name of its system-assigned identity is *\<app-name>/slots/\<slot-name>*. To grant permissions for an Azure AD group, use the group's display name instead (for example, *myAzureSQLDBAccessGroup*).

1. Type `EXIT` to return to the Cloud Shell prompt.

    > [!NOTE]
    > The back-end services of managed identities also [maintains a token cache](overview-managed-identity.md#configure-target-resource) that updates the token for a target resource only when it expires. If you make a mistake configuring your SQL Database permissions and try to modify the permissions *after* trying to get a token with your app, you don't actually get a new token with the updated permissions until the cached token expires.

    > [!NOTE]
    > Azure Active Directory and managed identities are not supported for on-premises SQL Server. 

### Modify connection string

Remember that the same changes you made in *Web.config* or *appsettings.json* works with the managed identity, so the only thing to do is to remove the existing connection string in App Service, which Visual Studio created deploying your app the first time. Use the following command, but replace *\<app-name>* with the name of your app.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## 5. Publish your changes

All that's left now is to publish your changes to Azure.

# [ASP.NET](#tab/dotnet)

1. **If you came from [Tutorial: Build an ASP.NET app in Azure with SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)**, publish your changes in Visual Studio. In the **Solution Explorer**, right-click your **DotNetAppSqlDb** project and select **Publish**.

    ![Publish from Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. In the publish page, select **Publish**. 

    > [!IMPORTANT]
    > Ensure that your app service name doesn't match with any existing [App Registrations](../active-directory/manage-apps/add-application-portal.md). This will lead to Principal ID conflicts.

# [ASP.NET Core](#tab/dotnetcore)

**If you came from [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](tutorial-dotnetcore-sqldb-app.md)**, publish your changes using Git, with the following commands:

```bash
git commit -am "configure managed identity"
git push azure main
```

-----

When the new webpage shows your to-do list, your app is connecting to the database using the managed identity.

![Azure app after Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

You should now be able to edit the to-do list as before.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## Next steps

What you learned:

> [!div class="checklist"]
> * Enable managed identities
> * Grant SQL Database access to the managed identity
> * Configure Entity Framework to use Azure AD authentication with SQL Database
> * Connect to SQL Database from Visual Studio using Azure AD authentication

> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure App Service](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Tutorial: Connect to Azure services that don't support managed identities (using Key Vault)](tutorial-connect-msi-key-vault.md)

> [!div class="nextstepaction"]
> [Tutorial: Isolate back-end communication with Virtual Network integration](tutorial-networking-isolate-vnet.md)
