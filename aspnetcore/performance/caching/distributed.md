---
title: Distribuované ukládání do mezipaměti v ASP.NET Core
author: guardrex
description: Naučte se používat ASP.NET Core distribuovanou mezipaměť pro zlepšení výkonu a škálovatelnosti aplikace, zejména v prostředí cloudové nebo serverové farmy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/caching/distributed
ms.openlocfilehash: 3e039a26505aed1bcc0299880039760fef19fd67
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727244"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="153d9-103">Distribuované ukládání do mezipaměti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="153d9-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="153d9-104">Od [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="153d9-104">By [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="153d9-105">Distribuovaná mezipaměť je mezipaměť, kterou sdílí víc aplikačních serverů, obvykle se udržuje jako externí služba pro aplikační servery, které k ní přistupují.</span><span class="sxs-lookup"><span data-stu-id="153d9-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="153d9-106">Distribuovaná mezipaměť může zlepšit výkon a škálovatelnost aplikace ASP.NET Core, zejména v případě, že je aplikace hostována cloudovou službou nebo serverovou farmou.</span><span class="sxs-lookup"><span data-stu-id="153d9-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="153d9-107">Distribuovaná mezipaměť má několik výhod oproti jiným scénářům ukládání do mezipaměti, kdy se data uložená v mezipaměti ukládají na jednotlivé aplikační servery.</span><span class="sxs-lookup"><span data-stu-id="153d9-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="153d9-108">Když jsou data v mezipaměti distribuována, data:</span><span class="sxs-lookup"><span data-stu-id="153d9-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="153d9-109">Je *souvislý* (konzistentní) mezi požadavky na více serverů.</span><span class="sxs-lookup"><span data-stu-id="153d9-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="153d9-110">Zachová se restarty serveru a nasazení aplikací.</span><span class="sxs-lookup"><span data-stu-id="153d9-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="153d9-111">Nepoužívá místní paměť.</span><span class="sxs-lookup"><span data-stu-id="153d9-111">Doesn't use local memory.</span></span>

<span data-ttu-id="153d9-112">Konfigurace distribuované mezipaměti je specifická pro implementaci.</span><span class="sxs-lookup"><span data-stu-id="153d9-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="153d9-113">Tento článek popisuje, jak nakonfigurovat SQL Server a Redis distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="153d9-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="153d9-114">K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="153d9-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="153d9-115">Bez ohledu na to, která implementace je vybraná, aplikace komunikuje s mezipamětí pomocí rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>.</span><span class="sxs-lookup"><span data-stu-id="153d9-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="153d9-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="153d9-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="153d9-117">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="153d9-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="153d9-118">Pokud chcete použít SQL Server distribuovanou mezipaměť, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="153d9-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="153d9-119">Chcete-li použít distribuovanou mezipaměť Redis, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="153d9-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="153d9-120">Pokud chcete použít distribuovanou mezipaměť NCache, přidejte odkaz na balíček do balíčku [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="153d9-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="153d9-121">Pokud chcete použít SQL Server distribuovanou mezipaměť, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="153d9-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="153d9-122">Pokud chcete použít distribuovanou mezipaměť Redis, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="153d9-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="153d9-123">Balíček Redis není zahrnutý v balíčku `Microsoft.AspNetCore.App`, takže musíte odkazovat na balíček Redis samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="153d9-123">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="153d9-124">Pokud chcete použít distribuovanou mezipaměť NCache, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="153d9-124">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="153d9-125">Balíček NCache není zahrnutý v balíčku `Microsoft.AspNetCore.App`, takže musíte odkazovat na balíček NCache samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="153d9-125">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="153d9-126">Pokud chcete použít SQL Server distribuovanou mezipaměť, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="153d9-126">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="153d9-127">Pokud chcete použít distribuovanou mezipaměť Redis, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="153d9-127">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="153d9-128">Balíček Redis není zahrnutý v balíčku `Microsoft.AspNetCore.App`, takže musíte odkazovat na balíček Redis samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="153d9-128">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="153d9-129">Pokud chcete použít distribuovanou mezipaměť NCache, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="153d9-129">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="153d9-130">Balíček NCache není zahrnutý v balíčku `Microsoft.AspNetCore.App`, takže musíte odkazovat na balíček NCache samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="153d9-130">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="153d9-131">Rozhraní IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="153d9-131">IDistributedCache interface</span></span>

<span data-ttu-id="153d9-132">Rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> poskytuje následující metody pro manipulaci s položkami v implementaci distribuované mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="153d9-132">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="153d9-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; přijímá klíč řetězce a načítá položku uloženou v mezipaměti jako `byte[]` pole, pokud je v mezipaměti nalezeno.</span><span class="sxs-lookup"><span data-stu-id="153d9-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="153d9-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; přidá do mezipaměti položku (jako `byte[]` Array) pomocí řetězcového klíče.</span><span class="sxs-lookup"><span data-stu-id="153d9-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="153d9-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; aktualizuje položku v mezipaměti na základě jejího klíče a resetuje časový limit klouzavého vypršení platnosti (pokud existuje).</span><span class="sxs-lookup"><span data-stu-id="153d9-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="153d9-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; odstraní položku mezipaměti na základě jejího řetězcového klíče.</span><span class="sxs-lookup"><span data-stu-id="153d9-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="153d9-137">Vytvoření služby distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="153d9-137">Establish distributed caching services</span></span>

<span data-ttu-id="153d9-138">Zaregistrujte implementaci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="153d9-138">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="153d9-139">Implementace implementované v tomto tématu zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="153d9-139">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="153d9-140">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="153d9-140">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="153d9-141">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="153d9-141">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="153d9-142">Distribuovaná mezipaměť Redis</span><span class="sxs-lookup"><span data-stu-id="153d9-142">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="153d9-143">Distribuovaná mezipaměť NCache</span><span class="sxs-lookup"><span data-stu-id="153d9-143">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="153d9-144">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="153d9-144">Distributed Memory Cache</span></span>

<span data-ttu-id="153d9-145">Mezipaměť distribuované paměti (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) je implementace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, která ukládá položky do paměti.</span><span class="sxs-lookup"><span data-stu-id="153d9-145">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="153d9-146">Mezipaměť distribuované paměti není skutečnou distribuovanou mezipamětí.</span><span class="sxs-lookup"><span data-stu-id="153d9-146">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="153d9-147">Položky uložené v mezipaměti jsou uloženy instancí aplikace na serveru, na kterém je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="153d9-147">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="153d9-148">Mezipaměť distribuované paměti je užitečnou implementací:</span><span class="sxs-lookup"><span data-stu-id="153d9-148">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="153d9-149">Ve scénářích vývoje a testování.</span><span class="sxs-lookup"><span data-stu-id="153d9-149">In development and testing scenarios.</span></span>
* <span data-ttu-id="153d9-150">Pokud se jeden server používá v produkčním prostředí a spotřeba paměti není problém.</span><span class="sxs-lookup"><span data-stu-id="153d9-150">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="153d9-151">Implementace vydaných mezipamětí mezipaměti úložiště dat v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="153d9-151">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="153d9-152">Umožňuje implementaci skutečného distribuovaného řešení pro ukládání do mezipaměti v budoucnu, pokud je potřeba několik uzlů nebo odolnost proti chybám.</span><span class="sxs-lookup"><span data-stu-id="153d9-152">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="153d9-153">Ukázková aplikace využívá mezipaměť distribuované paměti při spuštění aplikace ve vývojovém prostředí v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="153d9-153">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="153d9-154">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="153d9-154">Distributed SQL Server Cache</span></span>

<span data-ttu-id="153d9-155">Implementace distribuované mezipaměti SQL Server (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) umožňuje distribuované mezipaměti používat jako záložní úložiště databázi SQL Server.</span><span class="sxs-lookup"><span data-stu-id="153d9-155">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="153d9-156">Chcete-li vytvořit tabulku položek SQL Server v mezipaměti v instanci SQL Server, můžete použít nástroj pro `sql-cache`.</span><span class="sxs-lookup"><span data-stu-id="153d9-156">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="153d9-157">Nástroj vytvoří tabulku se zadaným názvem a schématem.</span><span class="sxs-lookup"><span data-stu-id="153d9-157">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="153d9-158">Vytvořte tabulku v SQL Server spuštěním příkazu `sql-cache create`.</span><span class="sxs-lookup"><span data-stu-id="153d9-158">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="153d9-159">Zadejte instanci SQL Server (`Data Source`), databázi (`Initial Catalog`), schéma (například `dbo`) a název tabulky (například `TestCache`):</span><span class="sxs-lookup"><span data-stu-id="153d9-159">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="153d9-160">Zpráva, která označuje, že nástroj byl úspěšný, je zaznamenána do protokolu:</span><span class="sxs-lookup"><span data-stu-id="153d9-160">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="153d9-161">Tabulka vytvořená nástrojem `sql-cache` má následující schéma:</span><span class="sxs-lookup"><span data-stu-id="153d9-161">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabulka mezipaměti SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="153d9-163">Aplikace by měla manipulovat s hodnotami mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, nikoli <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span><span class="sxs-lookup"><span data-stu-id="153d9-163">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="153d9-164">Ukázková aplikace implementuje <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> v nevývojovém prostředí v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="153d9-164">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="153d9-165"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (a volitelně <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) se obvykle ukládají mimo správu zdrojového kódu (například uložená [správcem tajných klíčů](xref:security/app-secrets) nebo v souboru *appsettings. JSON*/*appSettings. { ENVIRONMENT}. JSON* soubory.</span><span class="sxs-lookup"><span data-stu-id="153d9-165">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="153d9-166">Připojovací řetězec může obsahovat přihlašovací údaje, které by měly být zachovány ze systémů správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="153d9-166">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="153d9-167">Distribuované Redis Cache</span><span class="sxs-lookup"><span data-stu-id="153d9-167">Distributed Redis Cache</span></span>

<span data-ttu-id="153d9-168">[Redis](https://redis.io/) je open source úložiště dat v paměti, které se často používá jako distribuovaná mezipaměť.</span><span class="sxs-lookup"><span data-stu-id="153d9-168">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="153d9-169">Redis můžete použít místně a můžete nakonfigurovat [Azure Redis Cache](https://azure.microsoft.com/services/cache/) pro aplikace ASP.NET Core hostované pro Azure.</span><span class="sxs-lookup"><span data-stu-id="153d9-169">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="153d9-170">Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) v nevývojovém prostředí v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="153d9-170">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="153d9-171">Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) v nevývojovém prostředí v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="153d9-171">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="153d9-172">Aplikace konfiguruje implementaci mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Redis.RedisCache> (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="153d9-172">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="153d9-173">Instalace Redis na místní počítač:</span><span class="sxs-lookup"><span data-stu-id="153d9-173">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="153d9-174">Nainstalujte [balíček Redis pro čokolády](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="153d9-174">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="153d9-175">Spusťte `redis-server` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="153d9-175">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="153d9-176">Distribuovaná mezipaměť NCache</span><span class="sxs-lookup"><span data-stu-id="153d9-176">Distributed NCache Cache</span></span>

<span data-ttu-id="153d9-177">[NCache](https://github.com/Alachisoft/NCache) je open source distribuovaná mezipaměť v paměti vyvinutá nativně v rozhraní .NET a .NET Core.</span><span class="sxs-lookup"><span data-stu-id="153d9-177">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="153d9-178">NCache funguje místně i nakonfigurovaná jako cluster distribuovaných mezipamětí pro ASP.NET Core aplikaci běžící v Azure nebo na jiných hostujících platformách.</span><span class="sxs-lookup"><span data-stu-id="153d9-178">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="153d9-179">Pokud chcete nainstalovat a nakonfigurovat NCache na místním počítači, přečtěte si téma [průvodce Začínáme NCache pro Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="153d9-179">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="153d9-180">Konfigurace NCache:</span><span class="sxs-lookup"><span data-stu-id="153d9-180">To configure NCache:</span></span>

1. <span data-ttu-id="153d9-181">Nainstalujte [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="153d9-181">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="153d9-182">Nakonfigurujte cluster mezipaměti v [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="153d9-182">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="153d9-183">Přidejte následující kód pro `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="153d9-183">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="153d9-184">Použít distribuovanou mezipaměť</span><span class="sxs-lookup"><span data-stu-id="153d9-184">Use the distributed cache</span></span>

<span data-ttu-id="153d9-185">Chcete-li použít rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, požádejte o instanci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z libovolného konstruktoru v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="153d9-185">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="153d9-186">Instance je poskytována [vložením závislosti (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="153d9-186">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="153d9-187">Po spuštění ukázkové aplikace se <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> vloží do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="153d9-187">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="153d9-188">Aktuální čas je uložen v mezipaměti pomocí <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (Další informace najdete v tématu [Generic host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="153d9-188">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="153d9-189">Po spuštění ukázkové aplikace se <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> vloží do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="153d9-189">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="153d9-190">Aktuální čas je uložen v mezipaměti pomocí <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Další informace najdete v tématu [webový hostitel: rozhraní IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="153d9-190">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

<span data-ttu-id="153d9-191">Ukázková aplikace vloží <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `IndexModel` pro použití stránkou indexu.</span><span class="sxs-lookup"><span data-stu-id="153d9-191">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="153d9-192">Pokaždé, když je načtena stránka indexu, je v `OnGetAsync`zkontrolován čas uložený v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="153d9-192">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="153d9-193">Pokud doba v mezipaměti nevypršela, zobrazí se čas.</span><span class="sxs-lookup"><span data-stu-id="153d9-193">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="153d9-194">Pokud uplynula 20 sekund od poslední posledního přistupu k době ukládání do mezipaměti (při poslední načtení této stránky), stránka zobrazuje časový limit *v mezipaměti*.</span><span class="sxs-lookup"><span data-stu-id="153d9-194">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="153d9-195">Okamžitě aktualizovat čas uložený v mezipaměti na aktuální čas výběrem tlačítka **obnovit čas v mezipaměti** .</span><span class="sxs-lookup"><span data-stu-id="153d9-195">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="153d9-196">Tlačítko aktivuje metodu obslužné rutiny `OnPostResetCachedTime`.</span><span class="sxs-lookup"><span data-stu-id="153d9-196">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="153d9-197">Pro instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (alespoň pro předdefinované implementace) nemusíte používat singleton ani vymezenou dobu života.</span><span class="sxs-lookup"><span data-stu-id="153d9-197">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="153d9-198">Můžete také vytvořit instanci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, kdykoli budete možná potřebovat místo použití metody DI, ale vytvoření instance v kódu může ztížit testování a porušování [explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="153d9-198">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="153d9-199">Doporučení</span><span class="sxs-lookup"><span data-stu-id="153d9-199">Recommendations</span></span>

<span data-ttu-id="153d9-200">Při rozhodování, která implementace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> je pro vaši aplikaci nejvhodnější, vezměte v úvahu následující skutečnosti:</span><span class="sxs-lookup"><span data-stu-id="153d9-200">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="153d9-201">Stávající infrastruktura</span><span class="sxs-lookup"><span data-stu-id="153d9-201">Existing infrastructure</span></span>
* <span data-ttu-id="153d9-202">Požadavky na výkon</span><span class="sxs-lookup"><span data-stu-id="153d9-202">Performance requirements</span></span>
* <span data-ttu-id="153d9-203">Náklady</span><span class="sxs-lookup"><span data-stu-id="153d9-203">Cost</span></span>
* <span data-ttu-id="153d9-204">Prostředí týmu</span><span class="sxs-lookup"><span data-stu-id="153d9-204">Team experience</span></span>

<span data-ttu-id="153d9-205">Řešení ukládání do mezipaměti obvykle spoléhá na úložiště v paměti, které poskytuje rychlé načítání dat uložených v mezipaměti, ale paměť je omezeného prostředku a je nákladná k rozšíření.</span><span class="sxs-lookup"><span data-stu-id="153d9-205">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="153d9-206">Ukládat běžně používaná data do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="153d9-206">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="153d9-207">Obecně platí, že mezipaměť Redis poskytuje vyšší propustnost a nižší latenci než mezipaměť SQL Server.</span><span class="sxs-lookup"><span data-stu-id="153d9-207">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="153d9-208">Srovnávací testy se ale obvykle vyžadují k určení charakteristik výkonu pro strategie ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="153d9-208">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="153d9-209">Když se SQL Server používá jako úložiště zálohování distribuované mezipaměti, použití stejné databáze pro mezipaměť a běžné ukládání a načítání dat aplikace může negativně ovlivnit výkon obou.</span><span class="sxs-lookup"><span data-stu-id="153d9-209">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="153d9-210">Pro úložiště záloh distribuované mezipaměti doporučujeme použít vyhrazenou instanci SQL Server.</span><span class="sxs-lookup"><span data-stu-id="153d9-210">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="153d9-211">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="153d9-211">Additional resources</span></span>

* [<span data-ttu-id="153d9-212">Redis Cache v Azure</span><span class="sxs-lookup"><span data-stu-id="153d9-212">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="153d9-213">SQL Database v Azure</span><span class="sxs-lookup"><span data-stu-id="153d9-213">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="153d9-214">[ASP.NET Core poskytovatel IDistributedCache pro NCache ve webových farmách](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="153d9-214">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
