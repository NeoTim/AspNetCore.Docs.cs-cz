---
title: Zprostředkovatelé úložiště klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si o poskytovatelích úložiště klíčů v ASP.NET Core a o tom, jak nakonfigurovat umístění úložiště klíčů.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 1bbea6f16d57d5cc107c95293e2788271bfce601
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408042"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="c6f68-103">Zprostředkovatelé úložiště klíčů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6f68-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="c6f68-104">Systém ochrany dat [ve výchozím nastavení využívá mechanismus zjišťování](xref:security/data-protection/configuration/default-settings) , aby určil, kde by měly být zachovány kryptografické klíče.</span><span class="sxs-lookup"><span data-stu-id="c6f68-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="c6f68-105">Vývojář může přepsat výchozí mechanismus zjišťování a ručně zadat umístění.</span><span class="sxs-lookup"><span data-stu-id="c6f68-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="c6f68-106">Pokud zadáte explicitní umístění trvalosti klíčů, systém ochrany dat zruší registraci výchozího šifrování klíče v mechanismu REST, takže klíče už nebudou zašifrované v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="c6f68-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="c6f68-107">Pro produkční nasazení doporučujeme zadat také [explicitní šifrovací mechanismus klíčů](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="c6f68-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="c6f68-108">Systém souborů</span><span class="sxs-lookup"><span data-stu-id="c6f68-108">File system</span></span>

<span data-ttu-id="c6f68-109">Chcete-li nakonfigurovat úložiště klíčů založené na systému souborů, zavolejte konfigurační rutinu [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="c6f68-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="c6f68-110">Zadejte příkaz [DirectoryInfo](/dotnet/api/system.io.directoryinfo) ukazující na úložiště, kde se mají ukládat klíče:</span><span class="sxs-lookup"><span data-stu-id="c6f68-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="c6f68-111">`DirectoryInfo`Může odkazovat na adresář v místním počítači nebo může ukazovat na složku ve sdílené síťové složce.</span><span class="sxs-lookup"><span data-stu-id="c6f68-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="c6f68-112">Pokud odkazujete na adresář v místním počítači (a scénář je, že pouze aplikace na místním počítači vyžadují přístup k používání tohoto úložiště), zvažte použití rozhraní [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (ve Windows) k šifrování neaktivních klíčů.</span><span class="sxs-lookup"><span data-stu-id="c6f68-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="c6f68-113">V opačném případě zvažte použití [certifikátu X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) k šifrování neaktivních klíčů.</span><span class="sxs-lookup"><span data-stu-id="c6f68-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="c6f68-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="c6f68-114">Azure Storage</span></span>

<span data-ttu-id="c6f68-115">Balíček [Microsoft. AspNetCore. DataProtection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) umožňuje ukládat klíče ochrany dat v Azure Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="c6f68-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="c6f68-116">Klíče lze sdílet mezi několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6f68-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="c6f68-117">Aplikace můžou sdílet soubory cookie ověřování nebo CSRF ochranu napříč několika servery.</span><span class="sxs-lookup"><span data-stu-id="c6f68-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="c6f68-118">Pokud chcete nakonfigurovat poskytovatele služby Azure Blob Storage, zavolejte jedno z přetížení [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .</span><span class="sxs-lookup"><span data-stu-id="c6f68-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="c6f68-119">Pokud je webová aplikace spuštěná jako služba Azure, ověřovací tokeny se dají automaticky vytvořit pomocí [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="c6f68-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

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

<span data-ttu-id="c6f68-120">Přečtěte si [Další podrobnosti o konfiguraci ověřování služba-služba.](/azure/key-vault/service-to-service-authentication)</span><span class="sxs-lookup"><span data-stu-id="c6f68-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="c6f68-121">Redis</span><span class="sxs-lookup"><span data-stu-id="c6f68-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c6f68-122">Balíček [Microsoft. AspNetCore. DataProtection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) umožňuje ukládat klíče ochrany dat do mezipaměti Redis.</span><span class="sxs-lookup"><span data-stu-id="c6f68-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="c6f68-123">Klíče lze sdílet mezi několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6f68-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="c6f68-124">Aplikace můžou sdílet soubory cookie ověřování nebo CSRF ochranu napříč několika servery.</span><span class="sxs-lookup"><span data-stu-id="c6f68-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c6f68-125">Balíček [Microsoft. AspNetCore. DataProtection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) umožňuje ukládat klíče ochrany dat do mezipaměti Redis.</span><span class="sxs-lookup"><span data-stu-id="c6f68-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="c6f68-126">Klíče lze sdílet mezi několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6f68-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="c6f68-127">Aplikace můžou sdílet soubory cookie ověřování nebo CSRF ochranu napříč několika servery.</span><span class="sxs-lookup"><span data-stu-id="c6f68-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c6f68-128">Pro konfiguraci v Redis zavolejte jedno z přetížení [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :</span><span class="sxs-lookup"><span data-stu-id="c6f68-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="c6f68-129">Pro konfiguraci v Redis zavolejte jedno z přetížení [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :</span><span class="sxs-lookup"><span data-stu-id="c6f68-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="c6f68-130">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="c6f68-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="c6f68-131">StackExchange. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="c6f68-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="c6f68-132">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="c6f68-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="c6f68-133">Ukázky ASP.NET Core DataProtection</span><span class="sxs-lookup"><span data-stu-id="c6f68-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="c6f68-134">Registr</span><span class="sxs-lookup"><span data-stu-id="c6f68-134">Registry</span></span>

<span data-ttu-id="c6f68-135">**Platí jenom pro nasazení systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="c6f68-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="c6f68-136">Někdy může aplikace mít k systému souborů přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="c6f68-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="c6f68-137">Vezměte v úvahu scénář, ve kterém je aplikace spuštěná jako účet virtuální služby (například identita fondu aplikací *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="c6f68-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="c6f68-138">V těchto případech může správce zřídit klíč registru, který je přístupný pro identitu účtu služby.</span><span class="sxs-lookup"><span data-stu-id="c6f68-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="c6f68-139">Zavolejte metodu rozšíření [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="c6f68-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="c6f68-140">Zadejte [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) ukazující na místo, kde se mají ukládat kryptografické klíče:</span><span class="sxs-lookup"><span data-stu-id="c6f68-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="c6f68-141">K šifrování neaktivních klíčů doporučujeme použít rozhraní [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="c6f68-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="c6f68-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c6f68-142">Entity Framework Core</span></span>

<span data-ttu-id="c6f68-143">Balíček [Microsoft. AspNetCore. DataProtection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) poskytuje mechanismus pro ukládání klíčů ochrany dat do databáze pomocí Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c6f68-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="c6f68-144">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`Balíček NuGet se musí přidat do souboru projektu, není součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c6f68-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c6f68-145">S tímto balíčkem je možné klíče sdílet mezi několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6f68-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="c6f68-146">Chcete-li nakonfigurovat poskytovatele EF Core, zavolejte [metodu \<TContext> PersistKeysToDbContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :</span><span class="sxs-lookup"><span data-stu-id="c6f68-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c6f68-147">Obecný parametr, `TContext` , musí dědit z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a implementovat [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="c6f68-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="c6f68-148">Vytvořte `DataProtectionKeys` tabulku.</span><span class="sxs-lookup"><span data-stu-id="c6f68-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6f68-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6f68-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c6f68-150">V okně **konzoly Správce balíčků** (PMC) spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c6f68-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c6f68-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c6f68-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c6f68-152">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c6f68-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="c6f68-153">`MyKeysContext`je `DbContext` definován v předchozí ukázce kódu.</span><span class="sxs-lookup"><span data-stu-id="c6f68-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="c6f68-154">Pokud používáte `DbContext` s jiným názvem, nahraďte své `DbContext` jméno pro `MyKeysContext` .</span><span class="sxs-lookup"><span data-stu-id="c6f68-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="c6f68-155">`DataProtectionKeys`Třída nebo entita přijímá strukturu zobrazenou v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="c6f68-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="c6f68-156">Vlastnost nebo pole</span><span class="sxs-lookup"><span data-stu-id="c6f68-156">Property/Field</span></span> | <span data-ttu-id="c6f68-157">Typ CLR</span><span class="sxs-lookup"><span data-stu-id="c6f68-157">CLR Type</span></span> | <span data-ttu-id="c6f68-158">Typ SQL</span><span class="sxs-lookup"><span data-stu-id="c6f68-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="c6f68-159">`int`, PK, `IDENTITY(1,1)` , not null</span><span class="sxs-lookup"><span data-stu-id="c6f68-159">`int`, PK, `IDENTITY(1,1)`, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="c6f68-160">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="c6f68-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="c6f68-161">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="c6f68-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="c6f68-162">Vlastní úložiště klíčů</span><span class="sxs-lookup"><span data-stu-id="c6f68-162">Custom key repository</span></span>

<span data-ttu-id="c6f68-163">Pokud nejsou mechanismy v krabici vhodné, může vývojář určit vlastní mechanismus trvalosti klíčů tím, že poskytuje vlastní [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span><span class="sxs-lookup"><span data-stu-id="c6f68-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
