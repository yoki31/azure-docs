---
title: Token cache serialization (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Learn about serialization and custom serialization of the token cache using the Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/09/2021
ms.author: jmprieur
ms.reviewer: mmacy
ms.custom: "devx-track-csharp, aaddev, has-adal-ref"
#Customer intent: As an application developer, I want to learn about token cache serialization so I can have fine-grained control of the proxy.
---

# Token cache serialization in MSAL.NET

After Microsoft Authentication Library (MSAL) [acquires a token](msal-acquire-cache-tokens.md), it caches that token. Public client applications (desktop and mobile apps) should try to get a token from the cache before acquiring a token by another method. Acquisition methods on confidential client applications manage the cache themselves. This article discusses default and custom serialization of the token cache in MSAL.NET.

## Quick summary

The recommendation is:
- When you're writing a desktop application, use the cross-platform token cache as explained in [Desktop apps](msal-net-token-cache-serialization.md?tabs=desktop).
- Do nothing for [mobile and UWP apps](msal-net-token-cache-serialization.md?tabs=mobile). MSAL.NET provides secure storage for the cache.
- In ASP.NET Core [web apps](scenario-web-app-call-api-overview.md) and [web APIs](scenario-web-api-call-api-overview.md), use [Microsoft.Identity.Web](microsoft-identity-web.md) as a higher-level API. You'll get token caches and much more. See [ASP.NET Core web apps and web APIs](msal-net-token-cache-serialization.md?tabs=aspnetcore).
- In the other cases of [web apps](scenario-web-app-call-api-overview.md) and [web APIs](scenario-web-api-call-api-overview.md):
  -	If you request tokens for users in a production application, use a [distributed token cache](msal-net-token-cache-serialization.md?tabs=aspnet#distributed-caches) (Redis, SQL Server, Azure Cosmos DB, distributed memory). Use token cache serializers available from [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache/).
  -	Otherwise, if you want to use an in-memory cache:
    -	If you're only using `AcquireTokenForClient`, either reuse the confidential client application instance and don't add a serializer, or create a new confidential client application and enable the [shared cache option](msal-net-token-cache-serialization.md?tabs=aspnet#no-token-cache-serialization). 
    
        A shared cache is faster because it's not serialized. However, the memory will grow as tokens are cached. The number of tokens is equal to the number of tenants times the number of downstream APIs. An app token is about 2 KB in size, whereas tokens for a user are about 7 KB in size. It's great for development, or if you have few users. 
    -	If you  want to use an in-memory token cache and control its size and eviction policies, use the [Microsoft.Identity.Web in-memory cache option](msal-net-token-cache-serialization.md?tabs=aspnet#in-memory-token-cache-1).
-	If you build an SDK and want to write your own token cache serializer for confidential client applications, inherit from [Microsoft.Identity.Web.MsalAsbtractTokenCacheProvider](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.TokenCache/MsalAbstractTokenCacheProvider.cs) and override the `WriteCacheBytesAsync` and `ReadCacheBytesAsync` methods.


## [ASP.NET Core web apps and web APIs](#tab/aspnetcore)

The [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache) NuGet package provides token cache serialization within the [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web) library.

| Extension method | Description  |
| ---------------- | ------------ |
| [AddInMemoryTokenCaches](/dotnet/api/microsoft.identity.web.microsoftidentityappcallswebapiauthenticationbuilder.addinmemorytokencaches) | Creates a temporary cache in memory for token storage and retrieval. In-memory token caches are faster than the other cache types, but their tokens aren't persisted between application restarts, and you can't control the cache size. In-memory caches are good for applications that don't require tokens to persist between app restarts. Use an in-memory token cache in apps that participate in machine-to-machine auth scenarios like services, daemons, and others that use [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder) (the client credentials grant). In-memory token caches are also good for sample applications and during local app development. Microsoft.Identity.Web versions 1.19.0+ share an in-memory token cache across all application instances.
| `AddSessionTokenCaches` | The token cache is bound to the user session. This option isn't ideal if the ID token contains many claims, because the cookie will become too large.
| `AddDistributedTokenCaches` | The token cache is an adapter against the ASP.NET Core `IDistributedCache` implementation. It enables you to choose between a distributed memory cache, a Redis cache, a distributed NCache, or a SQL Server cache. For details about the `IDistributedCache` implementations, see [Distributed memory cache](/aspnet/core/performance/caching/distributed).


### In-memory token cache

Here's an example of code that uses the in-memory cache in the [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) method of the [Startup](/aspnet/core/fundamentals/startup) class in an ASP.NET Core application:

```CSharp
#using Microsoft.Identity.Web
```

```CSharp
using Microsoft.Identity.Web;

public class Startup
{
 const string scopesToRequest = "user.read";
  
  public void ConfigureServices(IServiceCollection services)
  {
   // code before
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest })
                .AddInMemoryTokenCaches();
   // code after
  }
  // code after
}
```

`AddInMemoryTokenCaches` is suitable in production if you request app-only tokens. If you use user tokens, consider using a distributed token cache. 

Token cache configuration code is similar between ASP.NET Core web apps and web APIs.

### Distributed token caches

Here are examples of possible distributed caches:

```C#
// or use a distributed Token Cache by adding
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest }
               .AddDistributedTokenCaches();

// Distributed token caches have a L1/L2 mechanism.
// L1 is in memory, and L2 is the distributed cache
// implementation that you will choose below.
// You can configure them to limit the memory of the 
// L1 cache, encrypt, and set eviction policies.
services.Configure<MsalDistributedTokenCacheAdapterOptions>(options => 
  {
    // Optional: Disable the L1 cache in apps that don't use session affinity
    //                 by setting DisableL1Cache to 'false'.
    options.DisableL1Cache = false;
    
    // Or limit the memory (by default, this is 500 MB)
    options.sizeLimit = 1024 * 1024 * 1024, // 1 GB

    // You can choose if you encrypt or not encrypt the cache
    options.Encrypt = false;

    // And you can set eviction policies for the distributed
    // cache.
    options.SlidingExpiration = TimeSpan.FromHours(1);
  }

// Then, choose your implementation of distributed cache
// -----------------------------------------------------

// good for prototyping and testing, but this is NOT persisted and it is NOT distributed - do not use in production
services.AddDistributedMemoryCache();

// Or a Redis cache
// Requires the Microsoft.Extensions.Caching.StackExchangeRedis NuGet package
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// You can even decide if you want to repair the connection
// with Redis and retry on Redis failures. 
services.Configure<MsalDistributedTokenCacheAdapterOptions>(options => 
{
  options.OnL2CacheFailure = (ex) =>
  {
    if (ex is StackExchange.Redis.RedisConnectionException)
    {
      // action: try to reconnect or something
      return true; //try to do the cache operation again
    }
    return false;
  };
});

// Or even a SQL Server token cache
// Requires the Microsoft.Extensions.Caching.SqlServer NuGet package
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});

// Or an Azure Cosmos DB cache
// Requires the Microsoft.Extensions.Caching.Cosmos NuGet package
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
    cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
    cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
    cacheOptions.CreateIfNotExists = true;
});
```

For more information, see:
- [Distributed cache advanced options](https://github.com/AzureAD/microsoft-identity-web/wiki/L1-Cache-in-Distributed-(L2)-Token-Cache)
- [Handle L2 cache eviction](https://github.com/AzureAD/microsoft-identity-web/wiki/Handle-L2-cache-eviction)
- [Set up a Redis cache in Docker](https://github.com/AzureAD/microsoft-identity-web/wiki/Set-up-a-Redis-cache-in-Docker)
- [Troubleshooting](https://github.com/AzureAD/microsoft-identity-web/wiki/Token-Cache-Troubleshooting)

The usage of distributed cache is featured in the [ASP.NET Core web app tutorial](/aspnet/core/tutorials/first-mvc-app/) in the [phase 2-2 token cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache).

## [Non-ASP.NET Core web apps and web APIs](#tab/aspnet)

Even when you use MSAL.NET, you can benefit from token cache serializers in Microsoft.Identity.Web.TokenCache. 

### Referencing the NuGet package

Add the [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache) NuGet package to your project instead of MSAL.NET.

### Configuring the token cache

The following code shows how to add an in-memory well-partitioned token cache to your app.

```CSharp
using Microsoft.Identity.Web;
using Microsoft.Identity.Client;
using Microsoft.Extensions.DependencyInjection;
```

```CSharp

public static async Task<AuthenticationResult> GetTokenAsync(string clientId, X509Certificate cert, string authority, string[] scopes)
 {
     // Create the confidential client application
     app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate, you can use .WithClientSecret(clientSecret)
       .WithCertificate(cert)
       .WithLegacyCacheCompatibility(false)
       .WithAuthority(authority)
       .Build();

     // Add a static in-memory token cache. Other options available: see below
     app.AddInMemoryTokenCache();  // Microsoft.Identity.Web.TokenCache 1.17+
   
     // Make the call to get a token for client_credentials flow (app-to-app scenario) 
     return await app.AcquireTokenForClient(scopes).ExecuteAsync();
     
     // OR Make the call to get a token for OBO (web API scenario)
     return await app.AcquireTokenOnBehalfOf(scopes, userAssertion).ExecuteAsync();
     
     // OR Make the call to get a token via auth code (web app scenario)
     return await app.AcquireTokenByAuthorizationCode(scopes, authCode);    
     
     // OR, when the user has previously logged in, get a token silently
     string homeAccountId = User.GetHomeAccountId(); // uid and utid claims
     var account = await app.GetAccountAsync(homeAccountId);
     try
     {
          return await app.AcquireTokenSilent(scopes, account).ExecuteAsync();; 
     } 
     catch (MsalUiRequiredException)
     {
      	// cannot get a token silently, so redirect the user to be challenged 
     }
  }
```

### Available caching technologies

Instead of `app.AddInMemoryTokenCache();`, you can use different caching serialization technologies. For example, you can use no-serialization, in-memory, and distributed token cache storage provided by .NET.

<a id="no-token-cache-serialization"></a>
#### Token cache without serialization

You can specify that you don't want to have any token cache serialization and instead rely on the MSAL.NET internal cache:
- Use `.WithCacheOptions(CacheOptions.EnableSharedCacheOptions)` when you build the application.
- Don't add any serializer.

```CSharp
    // Create the confidential client application
    app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate, you can use .WithClientSecret(clientSecret)
       .WithCertificate(cert)
       .WithLegacyCacheCompatibility(false)
       .WithCacheOptions(CacheOptions.EnableSharedCacheOptions)
       .WithAuthority(authority)
       .Build();
```

`WithCacheOptions(CacheOptions.EnableSharedCacheOptions)` makes the internal MSAL token cache shared between MSAL client application instances. Sharing a token cache is faster than using any token cache serialization, but the internal in-memory token cache doesn't have eviction policies. Existing tokens will be refreshed in place, but fetching tokens for different users, tenants, and resources makes the cache grow accordingly. 

If you use this approach and have a large number of users or tenants, be sure to monitor the memory footprint. If the memory footprint becomes a problem, consider enabling token cache serialization, which might reduce the internal cache size. Also be aware that currently, you can't use shared cache and cache serialization together.

#### In-memory token cache

In-memory token cache serialization is great in samples. It's also good in production applications if you only request app tokens (`AcquireTokenForClient`), provided that you don't mind if the token cache is lost when the web app is restarted. We don't recommend it in production if you request user tokens (`AcquireTokenByAuthorizationCode`, `AcquireTokenSilent`, `AcquireTokenOnBehalfOf`).

```CSharp 
     // Add an in-memory token cache
     app.AddInMemoryTokenCache();
```

You can also specify options to limit the size of the in-memory token cache:

```CSharp 
  // Add an in-memory token cache with options
  app.AddInMemoryTokenCache(services =>
  {
      // Configure the memory cache options
      services.Configure<MemoryCacheOptions>(options =>
      {
          options.SizeLimit = 500 * 1024 * 1024; // in bytes (500 MB)
      });
  }
  );
```

#### Distributed caches

If you use `app.AddDistributedTokenCache`, the token cache is an adapter against the .NET `IDistributedCache` implementation. So you can choose between a distributed memory cache, a SQL Server cache, a Redis cache, or an Azure Cosmos DB cache. For details about the `IDistributedCache` implementations, see [Distributed memory cache](/aspnet/core/performance/caching/distributed).

Here's the code for a distributed in-memory token cache:

```CSharp 
  // In-memory distributed token cache
  app.AddDistributedTokenCache(services =>
  {
    // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory
    services.AddDistributedMemoryCache();

    // Distributed token caches have an L1/L2 mechanism.
    // L1 is in memory, and L2 is the distributed cache
    // implementation that you will choose below.
    // You can configure them to limit the memory of the 
    // L1 cache, encrypt, and set eviction policies.
    services.Configure<MsalDistributedTokenCacheAdapterOptions>(options => 
      {
        // You can disable the L1 cache if you want
        options.DisableL1Cache = false;
        
        // Or limit the memory (by default, this is 500 MB)
        options.sizeLimit = 1024 * 1024 * 1024, // 1 GB

        // You can choose to encrypt the cache or not
        options.Encrypt = false;

        // And you can set eviction policies for the distributed
        // cache
        options.SlidingExpiration = TimeSpan.FromHours(1);
      });
  });
```

Here's the code for a SQL Server cache:

```CSharp 
     // SQL Server token cache
     app.AddDistributedTokenCache(services =>
     {
      services.AddDistributedSqlServerCache(options =>
      {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory

       // Requires to reference Microsoft.Extensions.Caching.SqlServer
       options.ConnectionString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=TestCache;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";
       options.SchemaName = "dbo";
       options.TableName = "TestCache";

       // You don't want the SQL token cache to be purged before the access token has expired. Usually
       // access tokens expire after 1 hour (but this can be changed by token lifetime policies), whereas
       // the default sliding expiration for the distributed SQL database is 20 mins. 
       // Use a value above 60 mins (or the lifetime of a token in case of longer-lived tokens)
       options.DefaultSlidingExpiration = TimeSpan.FromMinutes(90);
      });
     });
```

Here's the code for a Redis cache:

```CSharp 
    // Redis token cache
    app.AddDistributedTokenCache(services =>
    {
      // Requires to reference Microsoft.Extensions.Caching.StackExchangeRedis
       services.AddStackExchangeRedisCache(options =>
       {
         options.Configuration = "localhost";
         options.InstanceName = "Redis";
       });

      // You can even decide if you want to repair the connection
      // with Redis and retry on Redis failures. 
      services.Configure<MsalDistributedTokenCacheAdapterOptions>(options => 
      {
        options.OnL2CacheFailure = (ex) =>
        {
          if (ex is StackExchange.Redis.RedisConnectionException)
          {
            // action: try to reconnect or something
            return true; //try to do the cache operation again
          }
          return false;
        };
      });
    });
```

Here's the code for an Azure Cosmos DB cache:

```CSharp 
      // Azure Cosmos DB token cache
      app.AddDistributedTokenCache(services =>
      {
        // Requires to reference Microsoft.Extensions.Caching.Cosmos
        services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
        {
          cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
          cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
          cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
          cacheOptions.CreateIfNotExists = true;
        });
       });
```

For more information about distributed caches, see:

- [Distributed cache advanced options](https://github.com/AzureAD/microsoft-identity-web/wiki/L1-Cache-in-Distributed-(L2)-Token-Cache)
- [Handle L2 cache eviction](https://github.com/AzureAD/microsoft-identity-web/wiki/Handle-L2-cache-eviction)
- [Set up a Redis cache in Docker](https://github.com/AzureAD/microsoft-identity-web/wiki/Set-up-a-Redis-cache-in-Docker)
- [Troubleshooting](https://github.com/AzureAD/microsoft-identity-web/wiki/Token-Cache-Troubleshooting)

### Disabling a legacy token cache

MSAL has some internal code specifically to enable interacting with legacy Microsoft Authentication Library (ADAL) cache. When MSAL and ADAL aren't used side by side, the legacy cache isn't used and the related legacy code is unnecessary. MSAL [4.25.0](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases/tag/4.25.0) adds the ability to disable legacy ADAL cache code and improve cache usage performance. For a performance comparison before and after disabling the legacy cache, see [GitHub pull request 2309](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/pull/2309). 

Call `.WithLegacyCacheCompatibility(false)` on an application builder like the following code.

```csharp
var app = ConfidentialClientApplicationBuilder
	.Create(clientId)
	.WithClientSecret(clientSecret)
	.WithLegacyCacheCompatibility(false)
	.Build();
```

### Samples

- The following sample showcases using the token cache serializers in .NET Framework and .NET Core applications: [ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache). 
- The following sample is an ASP.NET web app that uses the same technics: [Use OpenID Connect to sign in users to Microsoft identity platform](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). For the full code, see [WebApp/Utils/MsalAppBuilder.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs).

## [Desktop apps](#tab/desktop)

In desktop applications, we recommend that you use the cross-platform token cache. MSAL.NET provides the cross-platform token cache in a separate library named [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet).

#### Referencing the NuGet package

Add the [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/) NuGet package to your project.

#### Configuring the token cache

For details, see the [wiki page](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache). Here's an example of usage of the cross-platform token cache:

```csharp
 var storageProperties =
     new StorageCreationPropertiesBuilder(Config.CacheFileName, Config.CacheDir)
     .WithLinuxKeyring(
         Config.LinuxKeyRingSchema,
         Config.LinuxKeyRingCollection,
         Config.LinuxKeyRingLabel,
         Config.LinuxKeyRingAttr1,
         Config.LinuxKeyRingAttr2)
     .WithMacKeyChain(
         Config.KeyChainServiceName,
         Config.KeyChainAccountName)
     .Build();

 IPublicClientApplication pca = PublicClientApplicationBuilder.Create(clientId)
    .WithAuthority(Config.Authority)
    .WithRedirectUri("http://localhost")  // make sure to register this redirect URI for the interactive login 
    .Build();
    

// This hooks up the cross-platform cache into MSAL
var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties );
cacheHelper.RegisterCache(pca.UserTokenCache);
         
```

#### Plain-text fallback mode

The cross-platform token cache allows you to store unencrypted tokens in plain text. This feature is intended for use in development environments for debugging purposes only. 

You can use the plain-text fallback mode by using the following code pattern.

```csharp
storageProperties =
    new StorageCreationPropertiesBuilder(
        Config.CacheFileName + ".plaintext",
        Config.CacheDir)
    .WithUnprotectedFile()
    .Build();

var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties).ConfigureAwait(false);
```


## [Mobile apps](#tab/mobile)

MSAL.NET provides an in-memory token cache by default. Serialization is provided by default for platforms where secure storage is available for a user as part of the platform: Universal Windows Platform (UWP), Xamarin.iOS, and Xamarin.Android.

## [Write your own cache](#tab/custom)

If you want to write your own token cache serializer, MSAL.NET provides custom token cache serialization in the .NET Framework and .NET Core subplatforms. Events are fired when the cache is accessed. Apps can choose whether to serialize or deserialize the cache. 

On confidential client applications that handle users (web apps that sign in users and call web APIs, and web APIs that call downstream web APIs), there can be many users. The users are processed in parallel. For security and performance reasons, our recommendation is to serialize one cache per user. Serialization events compute a cache key based on the identity of the processed user and serialize or deserialize a token cache for that user.

Remember, custom serialization isn't available on mobile platforms (UWP, Xamarin.iOS, and Xamarin.Android). MSAL already defines a secure and performant serialization mechanism for these platforms. .NET desktop and .NET Core applications, however, have varied architectures. And MSAL can't implement a general-purpose serialization mechanism. 

For example, websites might choose to store tokens in a Redis cache, or desktop apps might store tokens in an encrypted file. So serialization isn't provided out of the box. To have a persistent token cache application in .NET desktop or .NET Core, customize the serialization.

The following classes and interfaces are used in token cache serialization:

- `ITokenCache` defines events to subscribe to token cache serialization requests and methods to serialize or deserialize the cache at various formats (ADAL v3.0, MSAL 2.x, and MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback` is a callback passed to the events so that you can handle the serialization. They'll be called with arguments of type `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` only provides the `ClientId` value of the application and a reference to the user for which the token is available.

![Diagram that shows the classes in token cache serialization.](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET creates token caches for you. It provides you with the `IToken` cache when you call an application's `UserTokenCache` and `AppTokenCache` properties. You're not supposed to implement the interface yourself. 
>
> Your responsibility, when you implement a custom token cache serialization, is to react to `BeforeAccess` and `AfterAccess` events (or their `Async` varieties). The `BeforeAccess` delegate is responsible for deserializing the cache, whereas the `AfterAccess` one is responsible for serializing the cache. Parts of these events store or load blobs, which are passed through the event argument to whatever storage you want.

The strategies are different depending on whether you're writing a token cache serialization for a [public client application](msal-client-applications.md) (desktop) or a [confidential client application](msal-client-applications.md) (web app, web API, daemon app).

### Custom token cache for a web app or web API (confidential client application)

If you want to write your own token cache serializer for confidential client applications, we recommend that you inherit from [Microsoft.Identity.Web.MsalAsbtractTokenCacheProvider](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.TokenCache/MsalAbstractTokenCacheProvider.cs) and override the `WriteCacheBytesAsync` and `ReadCacheBytesAsync` methods.

Examples of token cache serializers are provided in [Microsoft.Identity.Web/TokenCacheProviders](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.TokenCache).

### Custom token cache for a desktop or mobile app (public client application)

Since MSAL.NET v2.x, you have several options for serializing the token cache of a public client. You can serialize the cache only to the MSAL.NET format. (The unified format cache is common across MSAL and the platforms.)  You can also support the [legacy](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token cache serialization of ADAL v3.

Customizing the token cache serialization to share the single sign-on state between ADAL.NET 3.x, ADAL.NET 5.x, and MSAL.NET is explained in part of the following sample: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> The MSAL.NET 1.1.4-preview token cache format is no longer supported in MSAL 2.x. If you have applications that use MSAL.NET 1.x, your users will have to sign in again. The migration from ADAL 4.x (and 3.x) is supported.

#### Simple token cache serialization (MSAL only)

The following code is an example of a naive implementation of custom serialization of a token cache for desktop applications. Here, the user token cache is a file in the same folder as the application.

After you build the application, you enable the serialization by calling the `TokenCacheHelper.EnableSerialization()` method and passing the application's `UserTokenCache` property.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

The `TokenCacheHelper` helper class is defined as:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different, for instance, for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changes in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

A product-quality, file-based token cache serializer for public client applications (for desktop applications running on Windows, Mac, and Linux) is available from the [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) open-source library. You can include it in your applications from the following NuGet package: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### Dual token cache serialization (MSAL unified cache and ADAL v3)

If you want to implement token cache serialization with the unified cache format (common to ADAL.NET 4.x, MSAL.NET 2.x, and other MSALs of the same generation or older, on the same platform), take a look at the following code:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

This time, the helper class is defined as:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL v3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL v3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL v3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the unified cache format, common to
  /// ADAL v4 and MSAL v2 and later, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET v4, MSAL.NET v3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL v3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an unencrypted blob (this is a JSON format)
  private static bool encrypt = true;
 }
}
```

---

## Monitor cache hit ratios and cache performance

MSAL exposes important metrics as part of [AuthenticationResult.AuthenticationResultMetadata](/dotnet/api/microsoft.identity.client.authenticationresultmetadata) object. You can log these metrics to assess the health of your application.

| Metric       | Meaning     | When to trigger an alarm?    |
| :-------------: | :----------: | :-----------: |
|  `DurationTotalInMs` | Total time spent in MSAL, including network calls and cache.   | Alarm on overall high latency (> 1 second). Value depends on token source. From the cache: one cache access. From Azure Active Directory (Azure AD): two cache accesses plus one HTTP call. First ever call (per-process) will take longer because of one extra HTTP call. |
|  `DurationInCacheInMs` | Time spent loading or saving the token cache, which is customized by the app developer (for example, save to Redis).| Alarm on spikes. |
|  `DurationInHttpInMs`| Time spent making HTTP calls to Azure AD.  | Alarm on spikes.|
|  `TokenSource` | Source of the token. Tokens are retrieved from the cache much faster (for example, ~100 ms versus ~700 ms). Can be used to monitor and alarm the cache hit ratio. | Use with `DurationTotalInMs`. |
|  `CacheRefreshReason` | Reason for fetching the access token from the identity provider. | Use with `TokenSource`. |

## Next steps

The following samples illustrate token cache serialization.

| Sample | Platform | Description|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Desktop (WPF) | Windows Desktop .NET (WPF) application that calls the Microsoft Graph API. ![Diagram that shows a topology with a desktop app client flowing to Azure Active Directory by acquiring a token interactively and to Microsoft Graph.](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Desktop (console) | Set of Visual Studio solutions that illustrate the migration of Azure AD v1.0 applications (using ADAL.NET) to Microsoft identity platform applications (using MSAL.NET). In particular, see [Token cache migration](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md) and [Confidential client token cache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache). |
[ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | ASP.NET (net472) | Example of token cache serialization in an ASP.NET MVC application (using MSAL.NET). In particular, see [MsalAppBuilder](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs).
