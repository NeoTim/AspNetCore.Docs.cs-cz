---
title: Distribuované ukládání do mezipaměti v ASP.NET Core
author: rick-anderson
description: Naučte se používat ASP.NET Core distribuovanou mezipaměť pro zlepšení výkonu a škálovatelnosti aplikace, zejména v prostředí cloudové nebo serverové farmy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/distributed
ms.openlocfilehash: d655e48f9504d337b0ffdbd6819f32101730310b
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106699"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="6f9fc-103">Distribuované ukládání do mezipaměti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f9fc-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="6f9fc-104">Od [Mohsin Nasir](https://github.com/mohsinnasir) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6f9fc-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f9fc-105">Distribuovaná mezipaměť je mezipaměť, kterou sdílí víc aplikačních serverů, obvykle se udržuje jako externí služba pro aplikační servery, které k ní přistupují.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="6f9fc-106">Distribuovaná mezipaměť může zlepšit výkon a škálovatelnost aplikace ASP.NET Core, zejména v případě, že je aplikace hostována cloudovou službou nebo serverovou farmou.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="6f9fc-107">Distribuovaná mezipaměť má několik výhod oproti jiným scénářům ukládání do mezipaměti, kdy se data uložená v mezipaměti ukládají na jednotlivé aplikační servery.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="6f9fc-108">Když jsou data v mezipaměti distribuována, data:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="6f9fc-109">Je *souvislý* (konzistentní) mezi požadavky na více serverů.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="6f9fc-110">Zachová se restarty serveru a nasazení aplikací.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="6f9fc-111">Nepoužívá místní paměť.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-111">Doesn't use local memory.</span></span>

<span data-ttu-id="6f9fc-112">Konfigurace distribuované mezipaměti je specifická pro implementaci.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="6f9fc-113">Tento článek popisuje, jak nakonfigurovat SQL Server a Redis distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="6f9fc-114">K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="6f9fc-115">Bez ohledu na to, která implementace je vybraná, aplikace komunikuje s mezipamětí pomocí <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="6f9fc-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6f9fc-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6f9fc-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6f9fc-117">Prerequisites</span></span>

<span data-ttu-id="6f9fc-118">Pokud chcete použít SQL Server distribuovanou mezipaměť, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="6f9fc-119">Chcete-li použít distribuovanou mezipaměť Redis, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="6f9fc-120">Pokud chcete použít distribuovanou mezipaměť NCache, přidejte odkaz na balíček do balíčku [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="6f9fc-121">Rozhraní IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-121">IDistributedCache interface</span></span>

<span data-ttu-id="6f9fc-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Rozhraní poskytuje následující metody pro manipulaci s položkami v implementaci distribuované mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="6f9fc-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Přijme klíč řetězce a načte položku uloženou v mezipaměti jako `byte[]` pole, pokud je v mezipaměti nalezeno.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="6f9fc-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Přidá položku (jako `byte[]` pole) do mezipaměti pomocí klíče řetězce.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="6f9fc-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aktualizuje položku v mezipaměti na základě jejího klíče a resetuje časový limit klouzavého vypršení platnosti (pokud existuje).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="6f9fc-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Odebere položku mezipaměti na základě jejího řetězcového klíče.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="6f9fc-127">Vytvoření služby distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="6f9fc-127">Establish distributed caching services</span></span>

<span data-ttu-id="6f9fc-128">Zaregistrujte implementaci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f9fc-129">Implementace implementované v tomto tématu zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="6f9fc-130">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="6f9fc-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="6f9fc-131">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="6f9fc-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="6f9fc-132">Distribuovaná mezipaměť Redis</span><span class="sxs-lookup"><span data-stu-id="6f9fc-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="6f9fc-133">Distribuovaná mezipaměť NCache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="6f9fc-134">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="6f9fc-134">Distributed Memory Cache</span></span>

<span data-ttu-id="6f9fc-135">Mezipaměť distribuované paměti ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) je implementací rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , které ukládá položky v paměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="6f9fc-136">Mezipaměť distribuované paměti není skutečnou distribuovanou mezipamětí.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="6f9fc-137">Položky uložené v mezipaměti jsou uloženy instancí aplikace na serveru, na kterém je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="6f9fc-138">Mezipaměť distribuované paměti je užitečnou implementací:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="6f9fc-139">Ve scénářích vývoje a testování.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="6f9fc-140">Pokud se jeden server používá v produkčním prostředí a spotřeba paměti není problém.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="6f9fc-141">Implementace vydaných mezipamětí mezipaměti úložiště dat v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="6f9fc-142">Umožňuje implementaci skutečného distribuovaného řešení pro ukládání do mezipaměti v budoucnu, pokud je potřeba několik uzlů nebo odolnost proti chybám.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="6f9fc-143">Ukázková aplikace využívá mezipaměť distribuované paměti při spuštění aplikace ve vývojovém prostředí v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="6f9fc-144">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="6f9fc-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="6f9fc-145">Distributed SQL Server cache Implementation ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) umožňuje distribuované mezipaměti používat jako záložní úložiště databázi SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="6f9fc-146">Chcete-li vytvořit tabulku položek SQL Server v mezipaměti v instanci SQL Server, můžete použít `sql-cache` nástroj.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="6f9fc-147">Nástroj vytvoří tabulku se zadaným názvem a schématem.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="6f9fc-148">Vytvořte tabulku v SQL Server spuštěním `sql-cache create` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="6f9fc-149">Zadejte instanci SQL Server ( `Data Source` ), databázi ( `Initial Catalog` ), schéma (například `dbo` ) a název tabulky (například `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="6f9fc-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="6f9fc-150">Zpráva, která označuje, že nástroj byl úspěšný, je zaznamenána do protokolu:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="6f9fc-151">Tabulka vytvořená `sql-cache` nástrojem má následující schéma:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabulka mezipaměti SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="6f9fc-153">Aplikace by měla manipulovat s hodnotami mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , nikoli <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="6f9fc-154">Ukázková aplikace implementuje <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> v prostředí bez vývoje `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="6f9fc-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (a volitelně <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> i <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) se obvykle ukládají mimo správu zdrojového kódu (například uložené správcem [tajných klíčů](xref:security/app-secrets) nebo v souboru *appSettings. JSON* / *appSettings. { ENVIRONMENT}. JSON* soubory.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="6f9fc-156">Připojovací řetězec může obsahovat přihlašovací údaje, které by měly být zachovány ze systémů správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="6f9fc-157">Distribuované Redis Cache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-157">Distributed Redis Cache</span></span>

<span data-ttu-id="6f9fc-158">[Redis](https://redis.io/) je open source úložiště dat v paměti, které se často používá jako distribuovaná mezipaměť.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="6f9fc-159">Redis můžete použít místně a můžete nakonfigurovat [Azure Redis Cache](https://azure.microsoft.com/services/cache/) pro aplikace ASP.NET Core hostované pro Azure.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="6f9fc-160">Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) v nevývojovém prostředí v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="6f9fc-161">Instalace Redis na místní počítač:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="6f9fc-162">Nainstalujte [balíček Redis pro čokolády](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="6f9fc-163">Spusťte `redis-server` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="6f9fc-164">Distribuovaná mezipaměť NCache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-164">Distributed NCache Cache</span></span>

<span data-ttu-id="6f9fc-165">[NCache](https://github.com/Alachisoft/NCache) je open source distribuovaná mezipaměť v paměti vyvinutá nativně v rozhraní .NET a .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="6f9fc-166">NCache funguje místně i nakonfigurovaná jako cluster distribuovaných mezipamětí pro ASP.NET Core aplikaci běžící v Azure nebo na jiných hostujících platformách.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="6f9fc-167">Pokud chcete nainstalovat a nakonfigurovat NCache na místním počítači, přečtěte si téma [průvodce Začínáme NCache pro Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="6f9fc-168">Konfigurace NCache:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-168">To configure NCache:</span></span>

1. <span data-ttu-id="6f9fc-169">Nainstalujte [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="6f9fc-170">Nakonfigurujte cluster mezipaměti v [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="6f9fc-171">Přidejte následující kód do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="6f9fc-172">Použít distribuovanou mezipaměť</span><span class="sxs-lookup"><span data-stu-id="6f9fc-172">Use the distributed cache</span></span>

<span data-ttu-id="6f9fc-173">Chcete-li použít <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní, vyžádejte si instanci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z libovolného konstruktoru v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="6f9fc-174">Instance je poskytována [vložením závislosti (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6f9fc-175">Po spuštění ukázkové aplikace je vložena <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="6f9fc-176">Aktuální čas je uložen v mezipaměti pomocí <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (Další informace najdete v tématu [Generic host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="6f9fc-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="6f9fc-177">Ukázková aplikace se vloží <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `IndexModel` pro použití stránkou indexu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="6f9fc-178">Pokaždé, když je načtena stránka indexu, je v mezipaměti kontrolována doba uložená v mezipaměti `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="6f9fc-179">Pokud doba v mezipaměti nevypršela, zobrazí se čas.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="6f9fc-180">Pokud uplynula 20 sekund od poslední posledního přistupu k době ukládání do mezipaměti (při poslední načtení této stránky), stránka zobrazuje časový limit *v mezipaměti*.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="6f9fc-181">Okamžitě aktualizovat čas uložený v mezipaměti na aktuální čas výběrem tlačítka **obnovit čas v mezipaměti** .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="6f9fc-182">Tlačítko aktivuje `OnPostResetCachedTime` metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="6f9fc-183">Pro instance není nutné používat singleton nebo vymezenou dobu života <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (alespoň pro předdefinované implementace).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="6f9fc-184">Můžete také vytvořit <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instanci, kdykoli budete možná potřebovat místo použití di, ale vytvoření instance v kódu může ztížit testování a porušování [explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="6f9fc-185">Doporučení</span><span class="sxs-lookup"><span data-stu-id="6f9fc-185">Recommendations</span></span>

<span data-ttu-id="6f9fc-186">Při rozhodování, která implementace nástroje <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> je pro vaši aplikaci nejvhodnější, vezměte v úvahu následující skutečnosti:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="6f9fc-187">Stávající infrastruktura</span><span class="sxs-lookup"><span data-stu-id="6f9fc-187">Existing infrastructure</span></span>
* <span data-ttu-id="6f9fc-188">Požadavky na výkon</span><span class="sxs-lookup"><span data-stu-id="6f9fc-188">Performance requirements</span></span>
* <span data-ttu-id="6f9fc-189">Náklady</span><span class="sxs-lookup"><span data-stu-id="6f9fc-189">Cost</span></span>
* <span data-ttu-id="6f9fc-190">Prostředí týmu</span><span class="sxs-lookup"><span data-stu-id="6f9fc-190">Team experience</span></span>

<span data-ttu-id="6f9fc-191">Řešení ukládání do mezipaměti obvykle spoléhá na úložiště v paměti, které poskytuje rychlé načítání dat uložených v mezipaměti, ale paměť je omezeného prostředku a je nákladná k rozšíření.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="6f9fc-192">Ukládat běžně používaná data do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="6f9fc-193">Obecně platí, že mezipaměť Redis poskytuje vyšší propustnost a nižší latenci než mezipaměť SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="6f9fc-194">Srovnávací testy se ale obvykle vyžadují k určení charakteristik výkonu pro strategie ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="6f9fc-195">Když se SQL Server používá jako úložiště zálohování distribuované mezipaměti, použití stejné databáze pro mezipaměť a běžné ukládání a načítání dat aplikace může negativně ovlivnit výkon obou.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="6f9fc-196">Pro úložiště záloh distribuované mezipaměti doporučujeme použít vyhrazenou instanci SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f9fc-197">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6f9fc-197">Additional resources</span></span>

* [<span data-ttu-id="6f9fc-198">Redis Cache v Azure</span><span class="sxs-lookup"><span data-stu-id="6f9fc-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="6f9fc-199">Databáze SQL v Azure</span><span class="sxs-lookup"><span data-stu-id="6f9fc-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="6f9fc-200">[ASP.NET Core poskytovatel IDistributedCache pro NCache ve webových farmách](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="6f9fc-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6f9fc-201">Distribuovaná mezipaměť je mezipaměť, kterou sdílí víc aplikačních serverů, obvykle se udržuje jako externí služba pro aplikační servery, které k ní přistupují.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="6f9fc-202">Distribuovaná mezipaměť může zlepšit výkon a škálovatelnost aplikace ASP.NET Core, zejména v případě, že je aplikace hostována cloudovou službou nebo serverovou farmou.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="6f9fc-203">Distribuovaná mezipaměť má několik výhod oproti jiným scénářům ukládání do mezipaměti, kdy se data uložená v mezipaměti ukládají na jednotlivé aplikační servery.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="6f9fc-204">Když jsou data v mezipaměti distribuována, data:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="6f9fc-205">Je *souvislý* (konzistentní) mezi požadavky na více serverů.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="6f9fc-206">Zachová se restarty serveru a nasazení aplikací.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="6f9fc-207">Nepoužívá místní paměť.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-207">Doesn't use local memory.</span></span>

<span data-ttu-id="6f9fc-208">Konfigurace distribuované mezipaměti je specifická pro implementaci.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="6f9fc-209">Tento článek popisuje, jak nakonfigurovat SQL Server a Redis distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="6f9fc-210">K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="6f9fc-211">Bez ohledu na to, která implementace je vybraná, aplikace komunikuje s mezipamětí pomocí <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="6f9fc-212">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6f9fc-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6f9fc-213">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6f9fc-213">Prerequisites</span></span>

<span data-ttu-id="6f9fc-214">Pokud chcete použít SQL Server distribuovanou mezipaměť, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="6f9fc-215">Pokud chcete použít distribuovanou mezipaměť Redis, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="6f9fc-216">Balíček Redis není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček Redis samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="6f9fc-217">Pokud chcete použít distribuovanou mezipaměť NCache, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="6f9fc-218">Balíček NCache není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček NCache samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="6f9fc-219">Rozhraní IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-219">IDistributedCache interface</span></span>

<span data-ttu-id="6f9fc-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Rozhraní poskytuje následující metody pro manipulaci s položkami v implementaci distribuované mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="6f9fc-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Přijme klíč řetězce a načte položku uloženou v mezipaměti jako `byte[]` pole, pokud je v mezipaměti nalezeno.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="6f9fc-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Přidá položku (jako `byte[]` pole) do mezipaměti pomocí klíče řetězce.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="6f9fc-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aktualizuje položku v mezipaměti na základě jejího klíče a resetuje časový limit klouzavého vypršení platnosti (pokud existuje).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="6f9fc-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Odebere položku mezipaměti na základě jejího řetězcového klíče.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="6f9fc-225">Vytvoření služby distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="6f9fc-225">Establish distributed caching services</span></span>

<span data-ttu-id="6f9fc-226">Zaregistrujte implementaci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f9fc-227">Implementace implementované v tomto tématu zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="6f9fc-228">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="6f9fc-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="6f9fc-229">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="6f9fc-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="6f9fc-230">Distribuovaná mezipaměť Redis</span><span class="sxs-lookup"><span data-stu-id="6f9fc-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="6f9fc-231">Distribuovaná mezipaměť NCache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="6f9fc-232">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="6f9fc-232">Distributed Memory Cache</span></span>

<span data-ttu-id="6f9fc-233">Mezipaměť distribuované paměti ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) je implementací rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , které ukládá položky v paměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="6f9fc-234">Mezipaměť distribuované paměti není skutečnou distribuovanou mezipamětí.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="6f9fc-235">Položky uložené v mezipaměti jsou uloženy instancí aplikace na serveru, na kterém je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="6f9fc-236">Mezipaměť distribuované paměti je užitečnou implementací:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="6f9fc-237">Ve scénářích vývoje a testování.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="6f9fc-238">Pokud se jeden server používá v produkčním prostředí a spotřeba paměti není problém.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="6f9fc-239">Implementace vydaných mezipamětí mezipaměti úložiště dat v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="6f9fc-240">Umožňuje implementaci skutečného distribuovaného řešení pro ukládání do mezipaměti v budoucnu, pokud je potřeba několik uzlů nebo odolnost proti chybám.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="6f9fc-241">Ukázková aplikace využívá mezipaměť distribuované paměti při spuštění aplikace ve vývojovém prostředí v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="6f9fc-242">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="6f9fc-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="6f9fc-243">Distributed SQL Server cache Implementation ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) umožňuje distribuované mezipaměti používat jako záložní úložiště databázi SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="6f9fc-244">Chcete-li vytvořit tabulku položek SQL Server v mezipaměti v instanci SQL Server, můžete použít `sql-cache` nástroj.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="6f9fc-245">Nástroj vytvoří tabulku se zadaným názvem a schématem.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="6f9fc-246">Vytvořte tabulku v SQL Server spuštěním `sql-cache create` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="6f9fc-247">Zadejte instanci SQL Server ( `Data Source` ), databázi ( `Initial Catalog` ), schéma (například `dbo` ) a název tabulky (například `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="6f9fc-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="6f9fc-248">Zpráva, která označuje, že nástroj byl úspěšný, je zaznamenána do protokolu:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="6f9fc-249">Tabulka vytvořená `sql-cache` nástrojem má následující schéma:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabulka mezipaměti SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="6f9fc-251">Aplikace by měla manipulovat s hodnotami mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , nikoli <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="6f9fc-252">Ukázková aplikace implementuje <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> v prostředí bez vývoje `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="6f9fc-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (a volitelně <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> i <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) se obvykle ukládají mimo správu zdrojového kódu (například uložené správcem [tajných klíčů](xref:security/app-secrets) nebo v souboru *appSettings. JSON* / *appSettings. { ENVIRONMENT}. JSON* soubory.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="6f9fc-254">Připojovací řetězec může obsahovat přihlašovací údaje, které by měly být zachovány ze systémů správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="6f9fc-255">Distribuované Redis Cache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-255">Distributed Redis Cache</span></span>

<span data-ttu-id="6f9fc-256">[Redis](https://redis.io/) je open source úložiště dat v paměti, které se často používá jako distribuovaná mezipaměť.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="6f9fc-257">Redis můžete použít místně a můžete nakonfigurovat [Azure Redis Cache](https://azure.microsoft.com/services/cache/) pro aplikace ASP.NET Core hostované pro Azure.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="6f9fc-258">Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) v nevývojovém prostředí v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="6f9fc-259">Instalace Redis na místní počítač:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="6f9fc-260">Nainstalujte [balíček Redis pro čokolády](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="6f9fc-261">Spusťte `redis-server` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="6f9fc-262">Distribuovaná mezipaměť NCache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-262">Distributed NCache Cache</span></span>

<span data-ttu-id="6f9fc-263">[NCache](https://github.com/Alachisoft/NCache) je open source distribuovaná mezipaměť v paměti vyvinutá nativně v rozhraní .NET a .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="6f9fc-264">NCache funguje místně i nakonfigurovaná jako cluster distribuovaných mezipamětí pro ASP.NET Core aplikaci běžící v Azure nebo na jiných hostujících platformách.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="6f9fc-265">Pokud chcete nainstalovat a nakonfigurovat NCache na místním počítači, přečtěte si téma [průvodce Začínáme NCache pro Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="6f9fc-266">Konfigurace NCache:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-266">To configure NCache:</span></span>

1. <span data-ttu-id="6f9fc-267">Nainstalujte [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="6f9fc-268">Nakonfigurujte cluster mezipaměti v [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="6f9fc-269">Přidejte následující kód do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="6f9fc-270">Použít distribuovanou mezipaměť</span><span class="sxs-lookup"><span data-stu-id="6f9fc-270">Use the distributed cache</span></span>

<span data-ttu-id="6f9fc-271">Chcete-li použít <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní, vyžádejte si instanci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z libovolného konstruktoru v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="6f9fc-272">Instance je poskytována [vložením závislosti (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6f9fc-273">Po spuštění ukázkové aplikace je vložena <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="6f9fc-274">Aktuální čas je uložen v mezipaměti s použitím <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Další informace najdete v tématu [webový hostitel: rozhraní IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="6f9fc-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="6f9fc-275">Ukázková aplikace se vloží <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `IndexModel` pro použití stránkou indexu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="6f9fc-276">Pokaždé, když je načtena stránka indexu, je v mezipaměti kontrolována doba uložená v mezipaměti `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="6f9fc-277">Pokud doba v mezipaměti nevypršela, zobrazí se čas.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="6f9fc-278">Pokud uplynula 20 sekund od poslední posledního přistupu k době ukládání do mezipaměti (při poslední načtení této stránky), stránka zobrazuje časový limit *v mezipaměti*.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="6f9fc-279">Okamžitě aktualizovat čas uložený v mezipaměti na aktuální čas výběrem tlačítka **obnovit čas v mezipaměti** .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="6f9fc-280">Tlačítko aktivuje `OnPostResetCachedTime` metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="6f9fc-281">Pro instance není nutné používat singleton nebo vymezenou dobu života <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (alespoň pro předdefinované implementace).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="6f9fc-282">Můžete také vytvořit <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instanci, kdykoli budete možná potřebovat místo použití di, ale vytvoření instance v kódu může ztížit testování a porušování [explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="6f9fc-283">Doporučení</span><span class="sxs-lookup"><span data-stu-id="6f9fc-283">Recommendations</span></span>

<span data-ttu-id="6f9fc-284">Při rozhodování, která implementace nástroje <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> je pro vaši aplikaci nejvhodnější, vezměte v úvahu následující skutečnosti:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="6f9fc-285">Stávající infrastruktura</span><span class="sxs-lookup"><span data-stu-id="6f9fc-285">Existing infrastructure</span></span>
* <span data-ttu-id="6f9fc-286">Požadavky na výkon</span><span class="sxs-lookup"><span data-stu-id="6f9fc-286">Performance requirements</span></span>
* <span data-ttu-id="6f9fc-287">Náklady</span><span class="sxs-lookup"><span data-stu-id="6f9fc-287">Cost</span></span>
* <span data-ttu-id="6f9fc-288">Prostředí týmu</span><span class="sxs-lookup"><span data-stu-id="6f9fc-288">Team experience</span></span>

<span data-ttu-id="6f9fc-289">Řešení ukládání do mezipaměti obvykle spoléhá na úložiště v paměti, které poskytuje rychlé načítání dat uložených v mezipaměti, ale paměť je omezeného prostředku a je nákladná k rozšíření.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="6f9fc-290">Ukládat běžně používaná data do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="6f9fc-291">Obecně platí, že mezipaměť Redis poskytuje vyšší propustnost a nižší latenci než mezipaměť SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="6f9fc-292">Srovnávací testy se ale obvykle vyžadují k určení charakteristik výkonu pro strategie ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="6f9fc-293">Když se SQL Server používá jako úložiště zálohování distribuované mezipaměti, použití stejné databáze pro mezipaměť a běžné ukládání a načítání dat aplikace může negativně ovlivnit výkon obou.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="6f9fc-294">Pro úložiště záloh distribuované mezipaměti doporučujeme použít vyhrazenou instanci SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f9fc-295">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6f9fc-295">Additional resources</span></span>

* [<span data-ttu-id="6f9fc-296">Redis Cache v Azure</span><span class="sxs-lookup"><span data-stu-id="6f9fc-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="6f9fc-297">Databáze SQL v Azure</span><span class="sxs-lookup"><span data-stu-id="6f9fc-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="6f9fc-298">[ASP.NET Core poskytovatel IDistributedCache pro NCache ve webových farmách](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="6f9fc-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6f9fc-299">Distribuovaná mezipaměť je mezipaměť, kterou sdílí víc aplikačních serverů, obvykle se udržuje jako externí služba pro aplikační servery, které k ní přistupují.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="6f9fc-300">Distribuovaná mezipaměť může zlepšit výkon a škálovatelnost aplikace ASP.NET Core, zejména v případě, že je aplikace hostována cloudovou službou nebo serverovou farmou.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="6f9fc-301">Distribuovaná mezipaměť má několik výhod oproti jiným scénářům ukládání do mezipaměti, kdy se data uložená v mezipaměti ukládají na jednotlivé aplikační servery.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="6f9fc-302">Když jsou data v mezipaměti distribuována, data:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="6f9fc-303">Je *souvislý* (konzistentní) mezi požadavky na více serverů.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="6f9fc-304">Zachová se restarty serveru a nasazení aplikací.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="6f9fc-305">Nepoužívá místní paměť.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-305">Doesn't use local memory.</span></span>

<span data-ttu-id="6f9fc-306">Konfigurace distribuované mezipaměti je specifická pro implementaci.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="6f9fc-307">Tento článek popisuje, jak nakonfigurovat SQL Server a Redis distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="6f9fc-308">K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="6f9fc-309">Bez ohledu na to, která implementace je vybraná, aplikace komunikuje s mezipamětí pomocí <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="6f9fc-310">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6f9fc-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6f9fc-311">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6f9fc-311">Prerequisites</span></span>

<span data-ttu-id="6f9fc-312">Pokud chcete použít SQL Server distribuovanou mezipaměť, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="6f9fc-313">Pokud chcete použít distribuovanou mezipaměť Redis, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="6f9fc-314">Balíček Redis není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček Redis samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="6f9fc-315">Pokud chcete použít distribuovanou mezipaměť NCache, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="6f9fc-316">Balíček NCache není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček NCache samostatně v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="6f9fc-317">Rozhraní IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-317">IDistributedCache interface</span></span>

<span data-ttu-id="6f9fc-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Rozhraní poskytuje následující metody pro manipulaci s položkami v implementaci distribuované mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="6f9fc-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Přijme klíč řetězce a načte položku uloženou v mezipaměti jako `byte[]` pole, pokud je v mezipaměti nalezeno.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="6f9fc-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Přidá položku (jako `byte[]` pole) do mezipaměti pomocí klíče řetězce.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="6f9fc-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aktualizuje položku v mezipaměti na základě jejího klíče a resetuje časový limit klouzavého vypršení platnosti (pokud existuje).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="6f9fc-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Odebere položku mezipaměti na základě jejího řetězcového klíče.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="6f9fc-323">Vytvoření služby distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="6f9fc-323">Establish distributed caching services</span></span>

<span data-ttu-id="6f9fc-324">Zaregistrujte implementaci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f9fc-325">Implementace implementované v tomto tématu zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="6f9fc-326">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="6f9fc-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="6f9fc-327">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="6f9fc-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="6f9fc-328">Distribuovaná mezipaměť Redis</span><span class="sxs-lookup"><span data-stu-id="6f9fc-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="6f9fc-329">Distribuovaná mezipaměť NCache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="6f9fc-330">Mezipaměť distribuované paměti</span><span class="sxs-lookup"><span data-stu-id="6f9fc-330">Distributed Memory Cache</span></span>

<span data-ttu-id="6f9fc-331">Mezipaměť distribuované paměti ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) je implementací rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , které ukládá položky v paměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="6f9fc-332">Mezipaměť distribuované paměti není skutečnou distribuovanou mezipamětí.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="6f9fc-333">Položky uložené v mezipaměti jsou uloženy instancí aplikace na serveru, na kterém je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="6f9fc-334">Mezipaměť distribuované paměti je užitečnou implementací:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="6f9fc-335">Ve scénářích vývoje a testování.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="6f9fc-336">Pokud se jeden server používá v produkčním prostředí a spotřeba paměti není problém.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="6f9fc-337">Implementace vydaných mezipamětí mezipaměti úložiště dat v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="6f9fc-338">Umožňuje implementaci skutečného distribuovaného řešení pro ukládání do mezipaměti v budoucnu, pokud je potřeba několik uzlů nebo odolnost proti chybám.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="6f9fc-339">Ukázková aplikace využívá mezipaměť distribuované paměti při spuštění aplikace ve vývojovém prostředí v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="6f9fc-340">Distribuovaná mezipaměť SQL Server</span><span class="sxs-lookup"><span data-stu-id="6f9fc-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="6f9fc-341">Distributed SQL Server cache Implementation ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) umožňuje distribuované mezipaměti používat jako záložní úložiště databázi SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="6f9fc-342">Chcete-li vytvořit tabulku položek SQL Server v mezipaměti v instanci SQL Server, můžete použít `sql-cache` nástroj.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="6f9fc-343">Nástroj vytvoří tabulku se zadaným názvem a schématem.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="6f9fc-344">Vytvořte tabulku v SQL Server spuštěním `sql-cache create` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="6f9fc-345">Zadejte instanci SQL Server ( `Data Source` ), databázi ( `Initial Catalog` ), schéma (například `dbo` ) a název tabulky (například `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="6f9fc-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="6f9fc-346">Zpráva, která označuje, že nástroj byl úspěšný, je zaznamenána do protokolu:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="6f9fc-347">Tabulka vytvořená `sql-cache` nástrojem má následující schéma:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabulka mezipaměti SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="6f9fc-349">Aplikace by měla manipulovat s hodnotami mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , nikoli <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="6f9fc-350">Ukázková aplikace implementuje <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> v prostředí bez vývoje `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="6f9fc-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (a volitelně <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> i <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) se obvykle ukládají mimo správu zdrojového kódu (například uložené správcem [tajných klíčů](xref:security/app-secrets) nebo v souboru *appSettings. JSON* / *appSettings. { ENVIRONMENT}. JSON* soubory.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="6f9fc-352">Připojovací řetězec může obsahovat přihlašovací údaje, které by měly být zachovány ze systémů správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="6f9fc-353">Distribuované Redis Cache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-353">Distributed Redis Cache</span></span>

<span data-ttu-id="6f9fc-354">[Redis](https://redis.io/) je open source úložiště dat v paměti, které se často používá jako distribuovaná mezipaměť.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="6f9fc-355">Redis můžete použít místně a můžete nakonfigurovat [Azure Redis Cache](https://azure.microsoft.com/services/cache/) pro aplikace ASP.NET Core hostované pro Azure.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="6f9fc-356">Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ):</span><span class="sxs-lookup"><span data-stu-id="6f9fc-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="6f9fc-357">Instalace Redis na místní počítač:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="6f9fc-358">Nainstalujte [balíček Redis pro čokolády](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="6f9fc-359">Spusťte `redis-server` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="6f9fc-360">Distribuovaná mezipaměť NCache</span><span class="sxs-lookup"><span data-stu-id="6f9fc-360">Distributed NCache Cache</span></span>

<span data-ttu-id="6f9fc-361">[NCache](https://github.com/Alachisoft/NCache) je open source distribuovaná mezipaměť v paměti vyvinutá nativně v rozhraní .NET a .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="6f9fc-362">NCache funguje místně i nakonfigurovaná jako cluster distribuovaných mezipamětí pro ASP.NET Core aplikaci běžící v Azure nebo na jiných hostujících platformách.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="6f9fc-363">Pokud chcete nainstalovat a nakonfigurovat NCache na místním počítači, přečtěte si téma [průvodce Začínáme NCache pro Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="6f9fc-364">Konfigurace NCache:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-364">To configure NCache:</span></span>

1. <span data-ttu-id="6f9fc-365">Nainstalujte [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="6f9fc-366">Nakonfigurujte cluster mezipaměti v [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="6f9fc-367">Přidejte následující kód do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f9fc-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="6f9fc-368">Použít distribuovanou mezipaměť</span><span class="sxs-lookup"><span data-stu-id="6f9fc-368">Use the distributed cache</span></span>

<span data-ttu-id="6f9fc-369">Chcete-li použít <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní, vyžádejte si instanci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z libovolného konstruktoru v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="6f9fc-370">Instance je poskytována [vložením závislosti (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6f9fc-371">Po spuštění ukázkové aplikace je vložena <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="6f9fc-372">Aktuální čas je uložen v mezipaměti s použitím <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Další informace najdete v tématu [webový hostitel: rozhraní IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="6f9fc-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="6f9fc-373">Ukázková aplikace se vloží <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `IndexModel` pro použití stránkou indexu.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="6f9fc-374">Pokaždé, když je načtena stránka indexu, je v mezipaměti kontrolována doba uložená v mezipaměti `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="6f9fc-375">Pokud doba v mezipaměti nevypršela, zobrazí se čas.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="6f9fc-376">Pokud uplynula 20 sekund od poslední posledního přistupu k době ukládání do mezipaměti (při poslední načtení této stránky), stránka zobrazuje časový limit *v mezipaměti*.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="6f9fc-377">Okamžitě aktualizovat čas uložený v mezipaměti na aktuální čas výběrem tlačítka **obnovit čas v mezipaměti** .</span><span class="sxs-lookup"><span data-stu-id="6f9fc-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="6f9fc-378">Tlačítko aktivuje `OnPostResetCachedTime` metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="6f9fc-379">Pro instance není nutné používat singleton nebo vymezenou dobu života <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (alespoň pro předdefinované implementace).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="6f9fc-380">Můžete také vytvořit <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instanci, kdykoli budete možná potřebovat místo použití di, ale vytvoření instance v kódu může ztížit testování a porušování [explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="6f9fc-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="6f9fc-381">Doporučení</span><span class="sxs-lookup"><span data-stu-id="6f9fc-381">Recommendations</span></span>

<span data-ttu-id="6f9fc-382">Při rozhodování, která implementace nástroje <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> je pro vaši aplikaci nejvhodnější, vezměte v úvahu následující skutečnosti:</span><span class="sxs-lookup"><span data-stu-id="6f9fc-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="6f9fc-383">Stávající infrastruktura</span><span class="sxs-lookup"><span data-stu-id="6f9fc-383">Existing infrastructure</span></span>
* <span data-ttu-id="6f9fc-384">Požadavky na výkon</span><span class="sxs-lookup"><span data-stu-id="6f9fc-384">Performance requirements</span></span>
* <span data-ttu-id="6f9fc-385">Náklady</span><span class="sxs-lookup"><span data-stu-id="6f9fc-385">Cost</span></span>
* <span data-ttu-id="6f9fc-386">Prostředí týmu</span><span class="sxs-lookup"><span data-stu-id="6f9fc-386">Team experience</span></span>

<span data-ttu-id="6f9fc-387">Řešení ukládání do mezipaměti obvykle spoléhá na úložiště v paměti, které poskytuje rychlé načítání dat uložených v mezipaměti, ale paměť je omezeného prostředku a je nákladná k rozšíření.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="6f9fc-388">Ukládat běžně používaná data do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="6f9fc-389">Obecně platí, že mezipaměť Redis poskytuje vyšší propustnost a nižší latenci než mezipaměť SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="6f9fc-390">Srovnávací testy se ale obvykle vyžadují k určení charakteristik výkonu pro strategie ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="6f9fc-391">Když se SQL Server používá jako úložiště zálohování distribuované mezipaměti, použití stejné databáze pro mezipaměť a běžné ukládání a načítání dat aplikace může negativně ovlivnit výkon obou.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="6f9fc-392">Pro úložiště záloh distribuované mezipaměti doporučujeme použít vyhrazenou instanci SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6f9fc-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f9fc-393">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6f9fc-393">Additional resources</span></span>

* [<span data-ttu-id="6f9fc-394">Redis Cache v Azure</span><span class="sxs-lookup"><span data-stu-id="6f9fc-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="6f9fc-395">Databáze SQL v Azure</span><span class="sxs-lookup"><span data-stu-id="6f9fc-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="6f9fc-396">[ASP.NET Core poskytovatel IDistributedCache pro NCache ve webových farmách](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="6f9fc-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 