---
title: Distribuované ukládání do mezipaměti v ASP.NET Core
author: guardrex
description: Naučte se používat ASP.NET Core distribuovanou mezipaměť pro zlepšení výkonu a škálovatelnosti aplikace, zejména v prostředí cloudové nebo serverové farmy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: performance/caching/distributed
ms.openlocfilehash: 8417463038bcdc0f77852bec3c3bb8a618153009
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773855"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="f6f97-103">Distribuované ukládání do mezipaměti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6f97-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="f6f97-104">Od [Luke Latham](https://github.com/guardrex) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f6f97-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f6f97-105">Distribuovaná mezipaměť je mezipaměť, kterou sdílí víc aplikačních serverů, obvykle se udržuje jako externí služba pro aplikační servery, které k ní přistupují.</span><span class="sxs-lookup"><span data-stu-id="f6f97-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="f6f97-106">Distribuovaná mezipaměť může zlepšit výkon a škálovatelnost aplikace ASP.NET Core, zejména v případě, že je aplikace hostována cloudovou službou nebo serverovou farmou.</span><span class="sxs-lookup"><span data-stu-id="f6f97-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="f6f97-107">Distribuovaná mezipaměť má několik výhod oproti jiným scénářům ukládání do mezipaměti, kdy se data uložená v mezipaměti ukládají na jednotlivé aplikační servery.</span><span class="sxs-lookup"><span data-stu-id="f6f97-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="f6f97-108">Když jsou data v mezipaměti distribuována, data:</span><span class="sxs-lookup"><span data-stu-id="f6f97-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="f6f97-109">Je *souvislý* (konzistentní) mezi požadavky na více serverů.</span><span class="sxs-lookup"><span data-stu-id="f6f97-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="f6f97-110">Zachová se restarty serveru a nasazení aplikací.</span><span class="sxs-lookup"><span data-stu-id="f6f97-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="f6f97-111">Nepoužívá místní paměť.</span><span class="sxs-lookup"><span data-stu-id="f6f97-111">Doesn't use local memory.</span></span>

<span data-ttu-id="f6f97-112">Konfigurace distribuované mezipaměti je specifická pro implementaci.</span><span class="sxs-lookup"><span data-stu-id="f6f97-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="f6f97-113">Tento článek popisuje, jak nakonfigurovat SQL Server a Redis distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f6f97-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="f6f97-114">K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="f6f97-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="f6f97-115">Bez ohledu na to, která implementace je vybraná, aplikace komunikuje s mezipamětí pomocí <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f6f97-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="f6f97-116">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f6f97-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f6f97-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f6f97-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6f97-118">Pokud chcete použít SQL Server distribuovanou mezipaměť, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="f6f97-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="f6f97-119">Chcete-li použít distribuovanou mezipaměť Redis, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="f6f97-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f6f97-120">Pokud chcete použít SQL Server distribuovanou mezipaměť, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="f6f97-120">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="f6f97-121">Pokud chcete použít distribuovanou mezipaměť Redis, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="f6f97-121">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="f6f97-122">Balíček Redis není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček Redis samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="f6f97-122">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f6f97-123">Pokud chcete použít SQL Server distribuovanou mezipaměť, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="f6f97-123">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="f6f97-124">Pokud chcete použít distribuovanou mezipaměť Redis, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="f6f97-124">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="f6f97-125">Balíček Redis není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček Redis samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="f6f97-125">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="f6f97-126">Rozhraní IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="f6f97-126">IDistributedCache interface</span></span>

<span data-ttu-id="f6f97-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Rozhraní poskytuje následující metody pro manipulaci s položkami v implementaci distribuované mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="f6f97-127">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="f6f97-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> `byte[]` Přijímá klíč řetězce a načte položku uloženou v mezipaměti jako pole, pokud je v mezipaměti nalezeno. &ndash;</span><span class="sxs-lookup"><span data-stu-id="f6f97-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="f6f97-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>Přidá do mezipaměti položku (jako `byte[]` pole) pomocí řetězcového klíče. <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash;</span><span class="sxs-lookup"><span data-stu-id="f6f97-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="f6f97-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ,&ndash; Aktualizuje položku v mezipaměti na základě jejího klíče a resetuje časový limit klouzavého vypršení platnosti (pokud existuje).</span><span class="sxs-lookup"><span data-stu-id="f6f97-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="f6f97-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> ,&ndash; Odebere položku mezipaměti na základě jejího řetězcového klíče.</span><span class="sxs-lookup"><span data-stu-id="f6f97-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="f6f97-132">Vytvoření služby distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="f6f97-132">Establish distributed caching services</span></span>

<span data-ttu-id="f6f97-133">Zaregistrujte implementaci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f6f97-133">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f6f97-134">Implementace implementované v tomto tématu zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="f6f97-134">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="f6f97-135">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="f6f97-135">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="f6f97-136">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="f6f97-136">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="f6f97-137">Distribuovaná mezipaměť Redis</span><span class="sxs-lookup"><span data-stu-id="f6f97-137">Distributed Redis cache</span></span>](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="f6f97-138">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="f6f97-138">Distributed Memory Cache</span></span>

<span data-ttu-id="f6f97-139">Mezipaměť distribuované paměti (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) je <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> implementací rozhraní, které ukládá položky v paměti.</span><span class="sxs-lookup"><span data-stu-id="f6f97-139">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="f6f97-140">Mezipaměť distribuované paměti není skutečnou distribuovanou mezipamětí.</span><span class="sxs-lookup"><span data-stu-id="f6f97-140">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="f6f97-141">Položky uložené v mezipaměti jsou uloženy instancí aplikace na serveru, na kterém je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="f6f97-141">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="f6f97-142">Mezipaměť distribuované paměti je užitečnou implementací:</span><span class="sxs-lookup"><span data-stu-id="f6f97-142">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="f6f97-143">Ve scénářích vývoje a testování.</span><span class="sxs-lookup"><span data-stu-id="f6f97-143">In development and testing scenarios.</span></span>
* <span data-ttu-id="f6f97-144">Pokud se jeden server používá v produkčním prostředí a spotřeba paměti není problém.</span><span class="sxs-lookup"><span data-stu-id="f6f97-144">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="f6f97-145">Implementace vydaných mezipamětí mezipaměti úložiště dat v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f6f97-145">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="f6f97-146">Umožňuje implementaci skutečného distribuovaného řešení pro ukládání do mezipaměti v budoucnu, pokud je potřeba několik uzlů nebo odolnost proti chybám.</span><span class="sxs-lookup"><span data-stu-id="f6f97-146">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="f6f97-147">Ukázková aplikace využívá mezipaměť distribuované paměti při spuštění aplikace ve vývojovém prostředí v `Startup.ConfigureServices`nástroji:</span><span class="sxs-lookup"><span data-stu-id="f6f97-147">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="f6f97-148">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="f6f97-148">Distributed SQL Server Cache</span></span>

<span data-ttu-id="f6f97-149">Distributed SQL Server cache Implementation<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>() umožňuje distribuované mezipaměti používat jako záložní úložiště databázi SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f6f97-149">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="f6f97-150">Chcete-li vytvořit tabulku položek SQL Server v mezipaměti v instanci SQL Server, můžete použít `sql-cache` nástroj.</span><span class="sxs-lookup"><span data-stu-id="f6f97-150">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="f6f97-151">Nástroj vytvoří tabulku se zadaným názvem a schématem.</span><span class="sxs-lookup"><span data-stu-id="f6f97-151">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="f6f97-152">Vytvořte tabulku v SQL Server spuštěním `sql-cache create` příkazu.</span><span class="sxs-lookup"><span data-stu-id="f6f97-152">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="f6f97-153">Zadejte instanci SQL Server`Data Source`(), databázi (`Initial Catalog`), `dbo`schéma (například) a název tabulky (například `TestCache`):</span><span class="sxs-lookup"><span data-stu-id="f6f97-153">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="f6f97-154">Zpráva, která označuje, že nástroj byl úspěšný, je zaznamenána do protokolu:</span><span class="sxs-lookup"><span data-stu-id="f6f97-154">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="f6f97-155">Tabulka vytvořená `sql-cache` nástrojem má následující schéma:</span><span class="sxs-lookup"><span data-stu-id="f6f97-155">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabulka mezipaměti SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="f6f97-157">Aplikace by měla manipulovat s hodnotami mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>nikoli.</span><span class="sxs-lookup"><span data-stu-id="f6f97-157">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="f6f97-158">Ukázková aplikace implementuje <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> v `Startup.ConfigureServices`prostředí bez vývoje:</span><span class="sxs-lookup"><span data-stu-id="f6f97-158">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f6f97-159"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>/ [](xref:security/app-secrets) A (a volitelně i) se obvykle ukládají mimo správu zdrojového kódu (například uložené správcem tajných klíčů nebo v souboru appSettings. JSON appSettings. { <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>  *ENVIRONMENT}. JSON* soubory.</span><span class="sxs-lookup"><span data-stu-id="f6f97-159">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="f6f97-160">Připojovací řetězec může obsahovat přihlašovací údaje, které by měly být zachovány ze systémů správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="f6f97-160">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="f6f97-161">Distribuované Redis Cache</span><span class="sxs-lookup"><span data-stu-id="f6f97-161">Distributed Redis Cache</span></span>

<span data-ttu-id="f6f97-162">[Redis](https://redis.io/) je open source úložiště dat v paměti, které se často používá jako distribuovaná mezipaměť.</span><span class="sxs-lookup"><span data-stu-id="f6f97-162">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="f6f97-163">Redis můžete použít místně a můžete nakonfigurovat [Azure Redis Cache](https://azure.microsoft.com/services/cache/) pro aplikace ASP.NET Core hostované pro Azure.</span><span class="sxs-lookup"><span data-stu-id="f6f97-163">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6f97-164">Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) v nevývojovém prostředí v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f6f97-164">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f6f97-165">Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) v nevývojovém prostředí v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f6f97-165">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f6f97-166">Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="f6f97-166">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="f6f97-167">Instalace Redis na místní počítač:</span><span class="sxs-lookup"><span data-stu-id="f6f97-167">To install Redis on your local machine:</span></span>

* <span data-ttu-id="f6f97-168">Nainstalujte [balíček Redis pro čokolády](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="f6f97-168">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
* <span data-ttu-id="f6f97-169">Spusťte `redis-server` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="f6f97-169">Run `redis-server` from a command prompt.</span></span>

## <a name="use-the-distributed-cache"></a><span data-ttu-id="f6f97-170">Použít distribuovanou mezipaměť</span><span class="sxs-lookup"><span data-stu-id="f6f97-170">Use the distributed cache</span></span>

<span data-ttu-id="f6f97-171">Chcete-li <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> použít rozhraní, vyžádejte si <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instanci z libovolného konstruktoru v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f6f97-171">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="f6f97-172">Instance je poskytována [vložením závislosti (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f6f97-172">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6f97-173">Po spuštění <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ukázkové aplikace je vložena do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f6f97-173">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="f6f97-174">Aktuální čas je uložen v mezipaměti <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> pomocí (Další informace najdete v [tématu obecný hostitel: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="f6f97-174">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6f97-175">Po spuštění <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ukázkové aplikace je vložena do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f6f97-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="f6f97-176">Aktuální čas je uložen v mezipaměti <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> s použitím (Další informace najdete [v tématu webový hostitel: Rozhraní](xref:fundamentals/host/web-host#iapplicationlifetime-interface)IApplicationLifetime):</span><span class="sxs-lookup"><span data-stu-id="f6f97-176">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

<span data-ttu-id="f6f97-177">Ukázková aplikace se vloží <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` do pro použití stránkou indexu.</span><span class="sxs-lookup"><span data-stu-id="f6f97-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="f6f97-178">Pokaždé, když je načtena stránka indexu, je v `OnGetAsync`mezipaměti kontrolována doba uložená v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f6f97-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="f6f97-179">Pokud doba v mezipaměti nevypršela, zobrazí se čas.</span><span class="sxs-lookup"><span data-stu-id="f6f97-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="f6f97-180">Pokud uplynula 20 sekund od poslední posledního přistupu k době ukládání do mezipaměti (při poslední načtení této stránky), stránka zobrazuje časový limit *v mezipaměti*.</span><span class="sxs-lookup"><span data-stu-id="f6f97-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="f6f97-181">Okamžitě aktualizovat čas uložený v mezipaměti na aktuální čas výběrem tlačítka **obnovit čas v mezipaměti** .</span><span class="sxs-lookup"><span data-stu-id="f6f97-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="f6f97-182">Tlačítko aktivuje `OnPostResetCachedTime` metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="f6f97-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f6f97-183">Pro <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance není nutné používat singleton nebo vymezenou dobu života (alespoň pro předdefinované implementace).</span><span class="sxs-lookup"><span data-stu-id="f6f97-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="f6f97-184">Můžete také vytvořit <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instanci, kdykoli budete možná potřebovat místo použití di, ale vytvoření instance v kódu může ztížit testování a porušování [explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="f6f97-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="f6f97-185">Doporučení</span><span class="sxs-lookup"><span data-stu-id="f6f97-185">Recommendations</span></span>

<span data-ttu-id="f6f97-186">Při rozhodování, která implementace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> nástroje je pro vaši aplikaci nejvhodnější, vezměte v úvahu následující skutečnosti:</span><span class="sxs-lookup"><span data-stu-id="f6f97-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="f6f97-187">Stávající infrastruktura</span><span class="sxs-lookup"><span data-stu-id="f6f97-187">Existing infrastructure</span></span>
* <span data-ttu-id="f6f97-188">Požadavky na výkon</span><span class="sxs-lookup"><span data-stu-id="f6f97-188">Performance requirements</span></span>
* <span data-ttu-id="f6f97-189">Ze</span><span class="sxs-lookup"><span data-stu-id="f6f97-189">Cost</span></span>
* <span data-ttu-id="f6f97-190">Prostředí týmu</span><span class="sxs-lookup"><span data-stu-id="f6f97-190">Team experience</span></span>

<span data-ttu-id="f6f97-191">Řešení ukládání do mezipaměti obvykle spoléhá na úložiště v paměti, které poskytuje rychlé načítání dat uložených v mezipaměti, ale paměť je omezeného prostředku a je nákladná k rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f6f97-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="f6f97-192">Ukládat běžně používaná data do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f6f97-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="f6f97-193">Obecně platí, že mezipaměť Redis poskytuje vyšší propustnost a nižší latenci než mezipaměť SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f6f97-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="f6f97-194">Srovnávací testy se ale obvykle vyžadují k určení charakteristik výkonu pro strategie ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f6f97-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="f6f97-195">Když se SQL Server používá jako úložiště zálohování distribuované mezipaměti, použití stejné databáze pro mezipaměť a běžné ukládání a načítání dat aplikace může negativně ovlivnit výkon obou.</span><span class="sxs-lookup"><span data-stu-id="f6f97-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="f6f97-196">Pro úložiště záloh distribuované mezipaměti doporučujeme použít vyhrazenou instanci SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f6f97-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6f97-197">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f6f97-197">Additional resources</span></span>

* [<span data-ttu-id="f6f97-198">Redis Cache v Azure</span><span class="sxs-lookup"><span data-stu-id="f6f97-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="f6f97-199">SQL Database v Azure</span><span class="sxs-lookup"><span data-stu-id="f6f97-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="f6f97-200">[ASP.NET Core poskytovatel IDistributedCache pro NCache ve webových farmách](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="f6f97-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
