---
title: Zprostředkovatelé úložiště klíčů v ASP.NET Core
author: rick-anderson
description: Další informace o poskytovatele úložiště klíčů v ASP.NET Core a jak konfigurovat umístění úložiště klíčů.
ms.author: riande
ms.date: 12/19/2018
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: d6dabc9e4581e0891d1dd14f73e086d50b45bba4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903045"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="8ae5c-103">Zprostředkovatelé úložiště klíčů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8ae5c-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="8ae5c-104">Systém ochrany dat [využívá mechanismus zjišťování ve výchozím nastavení](xref:security/data-protection/configuration/default-settings) k určení, kde by měl trvalé kryptografických klíčů.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="8ae5c-105">Vývojář můžete přepsat výchozí mechanismus pro zjišťování a ručně zadejte jeho umístění.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="8ae5c-106">Pokud chcete zadat umístění služby explicitní trvalost klíče, systém ochrany dat deregisters výchozí šifrování klíčů v rest mechanismus, takže klíče jsou už v klidovém stavu zašifrovaná.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="8ae5c-107">Doporučujeme vám dále [zadejte mechanismus explicitní šifrování klíče](xref:security/data-protection/implementation/key-encryption-at-rest) pro nasazení v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="8ae5c-108">Systém souborů</span><span class="sxs-lookup"><span data-stu-id="8ae5c-108">File system</span></span>

<span data-ttu-id="8ae5c-109">Chcete-li konfigurovat klíče úložiště založené na systému souborů, zavolejte [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) konfigurace rutiny, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="8ae5c-110">Zadejte [DirectoryInfo](/dotnet/api/system.io.directoryinfo) bude odkazovat na úložiště ukládat klíče:</span><span class="sxs-lookup"><span data-stu-id="8ae5c-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="8ae5c-111">`DirectoryInfo` Může odkazovat na adresáře na místním počítači, nebo můžete přejít do složky ve sdílené síťové složce.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="8ae5c-112">Pokud přejdete do adresáře v místním počítači (a tento scénář je, že jenom aplikace v místním počítači vyžaduje přístup k použití tohoto úložiště), zvažte použití [rozhraní Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) k šifrování klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="8ae5c-113">V opačném případě zvažte použití [certifikát X.509](xref:security/data-protection/implementation/key-encryption-at-rest) šifrování klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-and-redis"></a><span data-ttu-id="8ae5c-114">Azure a Redis</span><span class="sxs-lookup"><span data-stu-id="8ae5c-114">Azure and Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8ae5c-115">[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) a [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) balíčky povolit ukládání klíče ochrany dat ve službě Azure Storage nebo Redis mezipaměť.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) and [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) packages allow storing data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="8ae5c-116">Klíče mohou být sdíleny napříč několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="8ae5c-117">Aplikace můžete sdílet soubory cookie pro ověřování nebo CSRF ochrany napříč několika servery.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="8ae5c-118">[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) a [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) balíčky povolit ukládání klíče ochrany dat v Azure Storage nebo Azure Redis cache.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-118">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) and [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) packages allow storing data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="8ae5c-119">Klíče mohou být sdíleny napříč několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-119">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="8ae5c-120">Aplikace můžete sdílet soubory cookie pro ověřování nebo CSRF ochrany napříč několika servery.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-120">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

<span data-ttu-id="8ae5c-121">Pokud chcete nakonfigurovat zprostředkovatele služby Azure Blob Storage, volání jednoho z [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) přetížení:</span><span class="sxs-lookup"><span data-stu-id="8ae5c-121">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8ae5c-122">Ke konfiguraci na Redis zavoláním jednoho z [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) přetížení:</span><span class="sxs-lookup"><span data-stu-id="8ae5c-122">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="8ae5c-123">Ke konfiguraci na Redis zavoláním jednoho z [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) přetížení:</span><span class="sxs-lookup"><span data-stu-id="8ae5c-123">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="8ae5c-124">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="8ae5c-124">For more information, see the following topics:</span></span>

* [<span data-ttu-id="8ae5c-125">StackExchange.Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="8ae5c-125">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="8ae5c-126">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="8ae5c-126">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="8ae5c-127">Ukázky ASPNET/DataProtection</span><span class="sxs-lookup"><span data-stu-id="8ae5c-127">aspnet/DataProtection samples</span></span>](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="8ae5c-128">Registru</span><span class="sxs-lookup"><span data-stu-id="8ae5c-128">Registry</span></span>

<span data-ttu-id="8ae5c-129">**Platí jenom pro nasazení Windows.**</span><span class="sxs-lookup"><span data-stu-id="8ae5c-129">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="8ae5c-130">Aplikace v některých případech nemusí mít oprávnění k zápisu do systému souborů.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-130">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="8ae5c-131">Představte si třeba situaci, ve kterém je aplikace spuštěna jako účet virtuální služby (například *w3wp.exe*pro identitu fondu aplikací).</span><span class="sxs-lookup"><span data-stu-id="8ae5c-131">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="8ae5c-132">V těchto případech můžete zřizovat správce klíč registru, který je přístupný pro identitu účtu služby.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-132">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="8ae5c-133">Volání [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) rozšiřující metoda, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-133">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="8ae5c-134">Zadejte [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) odkazující na umístění pro uložení kryptografických klíčů:</span><span class="sxs-lookup"><span data-stu-id="8ae5c-134">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="8ae5c-135">Doporučujeme používat [rozhraní Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) šifrování klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-135">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="8ae5c-136">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8ae5c-136">Entity Framework Core</span></span>

<span data-ttu-id="8ae5c-137">[Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) balíček poskytuje mechanismus pro ukládání klíčů ochrany dat k databázi pomocí Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-137">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="8ae5c-138">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` Balíček NuGet musí být přidán do souboru projektu není součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8ae5c-138">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8ae5c-139">S tímto balíčkem klíče mohou být sdíleny napříč několika instancemi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-139">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="8ae5c-140">Chcete-li nakonfigurovat poskytovatele EF Core, zavolejte [ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) metody:</span><span class="sxs-lookup"><span data-stu-id="8ae5c-140">To configure the EF Core provider, call the [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

<span data-ttu-id="8ae5c-141">Obecný parametr `TContext`, musí dědit z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="8ae5c-141">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="8ae5c-142">Vytvořte `DataProtectionKeys` tabulky.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-142">Create the `DataProtectionKeys` table.</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8ae5c-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ae5c-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8ae5c-144">Spusťte následující příkazy v **Konzola správce balíčků** okno (PMC):</span><span class="sxs-lookup"><span data-stu-id="8ae5c-144">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8ae5c-145">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ae5c-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8ae5c-146">V příkazovém řádku spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8ae5c-146">Execute the following commands in a command shell:</span></span>

```console
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="8ae5c-147">`MyKeysContext` je `DbContext` definované v předchozím příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-147">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="8ae5c-148">Pokud používáte `DbContext` s jiným názvem, nahraďte vaše `DbContext` název `MyKeysContext`.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-148">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="8ae5c-149">`DataProtectionKeys` Třídy na entitu přijme strukturu je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="8ae5c-149">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="8ae5c-150">Vlastnost či pole</span><span class="sxs-lookup"><span data-stu-id="8ae5c-150">Property/Field</span></span> | <span data-ttu-id="8ae5c-151">Typ CLR</span><span class="sxs-lookup"><span data-stu-id="8ae5c-151">CLR Type</span></span> | <span data-ttu-id="8ae5c-152">SQL Type</span><span class="sxs-lookup"><span data-stu-id="8ae5c-152">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="8ae5c-153">`int`, PK, není null</span><span class="sxs-lookup"><span data-stu-id="8ae5c-153">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="8ae5c-154">`nvarchar(MAX)`, hodnotu null</span><span class="sxs-lookup"><span data-stu-id="8ae5c-154">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="8ae5c-155">`nvarchar(MAX)`, hodnotu null</span><span class="sxs-lookup"><span data-stu-id="8ae5c-155">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="8ae5c-156">Vlastní klíče úložiště</span><span class="sxs-lookup"><span data-stu-id="8ae5c-156">Custom key repository</span></span>

<span data-ttu-id="8ae5c-157">Pokud nejsou integrované mechanismy vhodné, Vývojář můžete zadat vlastní mechanismus trvalost klíče poskytnutím vlastní [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span><span class="sxs-lookup"><span data-stu-id="8ae5c-157">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
