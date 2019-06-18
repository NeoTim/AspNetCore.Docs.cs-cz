---
title: Zprostředkovatelé úložiště klíčů v ASP.NET Core
author: rick-anderson
description: Další informace o poskytovatele úložiště klíčů v ASP.NET Core a jak konfigurovat umístění úložiště klíčů.
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 19d51399e24d085f7c34f70098ca02cbba7a888f
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167046"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="aebb4-103">Zprostředkovatelé úložiště klíčů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aebb4-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="aebb4-104">Systém ochrany dat [využívá mechanismus zjišťování ve výchozím nastavení](xref:security/data-protection/configuration/default-settings) k určení, kde by měl trvalé kryptografických klíčů.</span><span class="sxs-lookup"><span data-stu-id="aebb4-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="aebb4-105">Vývojář můžete přepsat výchozí mechanismus pro zjišťování a ručně zadejte jeho umístění.</span><span class="sxs-lookup"><span data-stu-id="aebb4-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="aebb4-106">Pokud chcete zadat umístění služby explicitní trvalost klíče, systém ochrany dat deregisters výchozí šifrování klíčů v rest mechanismus, takže klíče jsou už v klidovém stavu zašifrovaná.</span><span class="sxs-lookup"><span data-stu-id="aebb4-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="aebb4-107">Doporučujeme vám dále [zadejte mechanismus explicitní šifrování klíče](xref:security/data-protection/implementation/key-encryption-at-rest) pro nasazení v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="aebb4-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="aebb4-108">Systém souborů</span><span class="sxs-lookup"><span data-stu-id="aebb4-108">File system</span></span>

<span data-ttu-id="aebb4-109">Chcete-li konfigurovat klíče úložiště založené na systému souborů, zavolejte [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) konfigurace rutiny, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="aebb4-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="aebb4-110">Zadejte [DirectoryInfo](/dotnet/api/system.io.directoryinfo) bude odkazovat na úložiště ukládat klíče:</span><span class="sxs-lookup"><span data-stu-id="aebb4-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="aebb4-111">`DirectoryInfo` Může odkazovat na adresáře na místním počítači, nebo můžete přejít do složky ve sdílené síťové složce.</span><span class="sxs-lookup"><span data-stu-id="aebb4-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="aebb4-112">Pokud přejdete do adresáře v místním počítači (a tento scénář je, že jenom aplikace v místním počítači vyžaduje přístup k použití tohoto úložiště), zvažte použití [rozhraní Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) k šifrování klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="aebb4-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="aebb4-113">V opačném případě zvažte použití [certifikát X.509](xref:security/data-protection/implementation/key-encryption-at-rest) šifrování klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="aebb4-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="aebb4-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="aebb4-114">Azure Storage</span></span>

<span data-ttu-id="aebb4-115">[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) balíček umožňuje ukládat klíče ochrany dat ve službě Azure Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="aebb4-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="aebb4-116">Klíče mohou být sdíleny napříč několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="aebb4-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="aebb4-117">Aplikace můžete sdílet soubory cookie pro ověřování nebo CSRF ochrany napříč několika servery.</span><span class="sxs-lookup"><span data-stu-id="aebb4-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="aebb4-118">Pokud chcete nakonfigurovat zprostředkovatele služby Azure Blob Storage, volání jednoho z [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) přetížení.</span><span class="sxs-lookup"><span data-stu-id="aebb4-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="aebb4-119">Pokud webová aplikace běží jako služba Azure, tokeny ověřování se dají automaticky vytvořit pomocí [Microsoft.Azure.Services.appauthentication přistupovat](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="aebb4-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

<span data-ttu-id="aebb4-120">Zobrazit [další podrobnosti o konfiguraci ověřování služba služba.](/azure/key-vault/service-to-service-authentication)</span><span class="sxs-lookup"><span data-stu-id="aebb4-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="aebb4-121">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="aebb4-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="aebb4-122">[Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) balíček umožňuje ukládat klíče ochrany dat v mezipaměti Redis.</span><span class="sxs-lookup"><span data-stu-id="aebb4-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="aebb4-123">Klíče mohou být sdíleny napříč několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="aebb4-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="aebb4-124">Aplikace můžete sdílet soubory cookie pro ověřování nebo CSRF ochrany napříč několika servery.</span><span class="sxs-lookup"><span data-stu-id="aebb4-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="aebb4-125">[Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) balíček umožňuje ukládat klíče ochrany dat v mezipaměti Redis.</span><span class="sxs-lookup"><span data-stu-id="aebb4-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="aebb4-126">Klíče mohou být sdíleny napříč několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="aebb4-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="aebb4-127">Aplikace můžete sdílet soubory cookie pro ověřování nebo CSRF ochrany napříč několika servery.</span><span class="sxs-lookup"><span data-stu-id="aebb4-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="aebb4-128">Ke konfiguraci na Redis zavoláním jednoho z [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) přetížení:</span><span class="sxs-lookup"><span data-stu-id="aebb4-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="aebb4-129">Ke konfiguraci na Redis zavoláním jednoho z [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) přetížení:</span><span class="sxs-lookup"><span data-stu-id="aebb4-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="aebb4-130">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="aebb4-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="aebb4-131">StackExchange.Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="aebb4-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="aebb4-132">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="aebb4-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="aebb4-133">Ukázky ASPNET/DataProtection</span><span class="sxs-lookup"><span data-stu-id="aebb4-133">aspnet/DataProtection samples</span></span>](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="aebb4-134">Registru</span><span class="sxs-lookup"><span data-stu-id="aebb4-134">Registry</span></span>

<span data-ttu-id="aebb4-135">**Platí jenom pro nasazení Windows.**</span><span class="sxs-lookup"><span data-stu-id="aebb4-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="aebb4-136">Aplikace v některých případech nemusí mít oprávnění k zápisu do systému souborů.</span><span class="sxs-lookup"><span data-stu-id="aebb4-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="aebb4-137">Představte si třeba situaci, ve kterém je aplikace spuštěna jako účet virtuální služby (například *w3wp.exe*pro identitu fondu aplikací).</span><span class="sxs-lookup"><span data-stu-id="aebb4-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="aebb4-138">V těchto případech můžete zřizovat správce klíč registru, který je přístupný pro identitu účtu služby.</span><span class="sxs-lookup"><span data-stu-id="aebb4-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="aebb4-139">Volání [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) rozšiřující metoda, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="aebb4-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="aebb4-140">Zadejte [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) odkazující na umístění pro uložení kryptografických klíčů:</span><span class="sxs-lookup"><span data-stu-id="aebb4-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="aebb4-141">Doporučujeme používat [rozhraní Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) šifrování klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="aebb4-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="aebb4-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="aebb4-142">Entity Framework Core</span></span>

<span data-ttu-id="aebb4-143">[Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) balíček poskytuje mechanismus pro ukládání klíčů ochrany dat k databázi pomocí Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="aebb4-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="aebb4-144">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` Balíček NuGet musí být přidán do souboru projektu není součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="aebb4-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="aebb4-145">S tímto balíčkem klíče mohou být sdíleny napříč několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="aebb4-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="aebb4-146">Chcete-li nakonfigurovat poskytovatele EF Core, zavolejte [ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) metody:</span><span class="sxs-lookup"><span data-stu-id="aebb4-146">To configure the EF Core provider, call the [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

<span data-ttu-id="aebb4-147">Obecný parametr `TContext`, musí dědit z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a implementovat [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="aebb4-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="aebb4-148">Vytvořte `DataProtectionKeys` tabulky.</span><span class="sxs-lookup"><span data-stu-id="aebb4-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aebb4-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aebb4-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aebb4-150">Spusťte následující příkazy v **Konzola správce balíčků** okno (PMC):</span><span class="sxs-lookup"><span data-stu-id="aebb4-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="aebb4-151">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="aebb4-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="aebb4-152">V příkazovém řádku spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="aebb4-152">Execute the following commands in a command shell:</span></span>

```console
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="aebb4-153">`MyKeysContext` je `DbContext` definované v předchozím příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="aebb4-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="aebb4-154">Pokud používáte `DbContext` s jiným názvem, nahraďte vaše `DbContext` název `MyKeysContext`.</span><span class="sxs-lookup"><span data-stu-id="aebb4-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="aebb4-155">`DataProtectionKeys` Třídy na entitu přijme strukturu je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="aebb4-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="aebb4-156">Vlastnost či pole</span><span class="sxs-lookup"><span data-stu-id="aebb4-156">Property/Field</span></span> | <span data-ttu-id="aebb4-157">Typ CLR</span><span class="sxs-lookup"><span data-stu-id="aebb4-157">CLR Type</span></span> | <span data-ttu-id="aebb4-158">SQL Type</span><span class="sxs-lookup"><span data-stu-id="aebb4-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="aebb4-159">`int`, PK, není null</span><span class="sxs-lookup"><span data-stu-id="aebb4-159">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="aebb4-160">`nvarchar(MAX)`, hodnotu null</span><span class="sxs-lookup"><span data-stu-id="aebb4-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="aebb4-161">`nvarchar(MAX)`, hodnotu null</span><span class="sxs-lookup"><span data-stu-id="aebb4-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="aebb4-162">Vlastní klíče úložiště</span><span class="sxs-lookup"><span data-stu-id="aebb4-162">Custom key repository</span></span>

<span data-ttu-id="aebb4-163">Pokud nejsou integrované mechanismy vhodné, Vývojář můžete zadat vlastní mechanismus trvalost klíče poskytnutím vlastní [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span><span class="sxs-lookup"><span data-stu-id="aebb4-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
