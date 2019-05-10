---
title: Mezipaměť in-memory v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak data v paměti v ASP.NET Core do mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: performance/caching/memory
ms.openlocfilehash: 1474dd397f2a8f83c7a1b0ead511b7f5751865bb
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900513"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="9ae66-103">Mezipaměť in-memory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ae66-103">Cache in-memory in ASP.NET Core</span></span>

<span data-ttu-id="9ae66-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Jan Luo](https://github.com/JunTaoLuo), a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9ae66-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9ae66-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ae66-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="9ae66-106">Základní informace o ukládání do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="9ae66-106">Caching basics</span></span>

<span data-ttu-id="9ae66-107">Ukládání do mezipaměti může výrazně zlepšit výkon a škálovatelnost aplikace tím, že snižuje práci potřebnou k generování obsahu.</span><span class="sxs-lookup"><span data-stu-id="9ae66-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="9ae66-108">Ukládání do mezipaměti funguje nejlépe s daty, která se mění jen zřídka.</span><span class="sxs-lookup"><span data-stu-id="9ae66-108">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="9ae66-109">Ukládání do mezipaměti ke zkopírování dat, které mohou být vráceny mnohem rychlejší než z původního zdroje.</span><span class="sxs-lookup"><span data-stu-id="9ae66-109">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="9ae66-110">Aplikace by měla bude sepsána a otestovaný na **nikdy** závisí na data uložená v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="9ae66-111">ASP.NET Core podporuje několik různé mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="9ae66-112">Nejjednodušší cache je založená na [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), která představuje mezipaměti uloženy v paměti na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="9ae66-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="9ae66-113">Aplikace, které běží na serverové farmě několika serverů by měly zajistit relace jsou vždy navrchu, při použití mezipaměti umístěné v paměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-113">Apps that run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="9ae66-114">Rychlé relace Ujistěte se, že následné žádosti z klienta všech přejít na stejném serveru.</span><span class="sxs-lookup"><span data-stu-id="9ae66-114">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="9ae66-115">Například Azure Web apps pomocí [směrování žádostí na aplikace](https://www.iis.net/learn/extensions/planning-for-arr) (směrování žádostí na aplikace) směrovat všechny následné požadavky na stejný server.</span><span class="sxs-lookup"><span data-stu-id="9ae66-115">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="9ae66-116">Vyžadují jiné rychlé relace ve webové farmě [distribuovaná mezipaměť](distributed.md) abyste se vyhnuli potížím konzistence mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-116">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="9ae66-117">U některých aplikací může podporovat distribuované mezipaměti vyšší horizontální navýšení kapacity než mezipaměti v paměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-117">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="9ae66-118">Pomocí distribuované mezipaměti snižuje zátěž při mezipaměť k externímu procesu.</span><span class="sxs-lookup"><span data-stu-id="9ae66-118">Using a distributed cache offloads the cache memory to an external process.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9ae66-119">`IMemoryCache` Mezipaměti vyřazení položky mezipaměti zatížení paměti, není-li [mezipaměti priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) je nastavena na `CacheItemPriority.NeverRemove`.</span><span class="sxs-lookup"><span data-stu-id="9ae66-119">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="9ae66-120">Můžete nastavit `CacheItemPriority` nastavte prioritu, se kterým mezipaměti vyloučí položky tím snižuje jejich přetížení paměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-120">You can set the `CacheItemPriority` to adjust the priority with which the cache evicts items under memory pressure.</span></span>

::: moniker-end

<span data-ttu-id="9ae66-121">Mezipaměť v paměti můžete uložit libovolný objekt; rozhraní distribuované mezipaměti je omezená na `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="9ae66-121">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="9ae66-122">Úložiště mezipaměti v paměti a distribuované mezipaměti položky jako páry klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ae66-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="9ae66-123">System.Runtime.Caching/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="9ae66-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="9ae66-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Balíček NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) lze použít s:</span><span class="sxs-lookup"><span data-stu-id="9ae66-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="9ae66-125">.NET standard 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="9ae66-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="9ae66-126">Žádné [implementace .NET](/dotnet/standard/net-standard#net-implementation-support) , který cílí na .NET Standard 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="9ae66-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="9ae66-127">Například, ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="9ae66-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="9ae66-128">Rozhraní .NET framework 4.5 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="9ae66-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="9ae66-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (popsané v tomto článku) se doporučuje namísto používat `System.Runtime.Caching` / `MemoryCache` lépe integrovaná do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9ae66-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="9ae66-130">Například `IMemoryCache` nativně funguje s ASP.NET Core [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9ae66-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="9ae66-131">Použití `System.Runtime.Caching` / `MemoryCache` jako most kompatibility během portování kódu z ASP.NET 4.x ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9ae66-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="9ae66-132">Pokyny k mezipaměti</span><span class="sxs-lookup"><span data-stu-id="9ae66-132">Cache guidelines</span></span>

* <span data-ttu-id="9ae66-133">Kód by měl mít vždy záložní volbu pro načtení dat a **není** závisí na hodnotu uloženou v mezipaměti k dispozici.</span><span class="sxs-lookup"><span data-stu-id="9ae66-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="9ae66-134">Mezipaměť používá omezených zdrojů paměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="9ae66-135">Omezit růst mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="9ae66-135">Limit cache growth:</span></span>
  * <span data-ttu-id="9ae66-136">Proveďte **není** použít externí vstup jako klíče mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="9ae66-137">Chcete-li omezit růst mezipaměti pomocí vypršení platnosti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="9ae66-138">[Můžete omezit velikost mezipaměti SetSize, velikost a hodnota parametru SizeLimit](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span><span class="sxs-lookup"><span data-stu-id="9ae66-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="9ae66-139">Modul runtime ASP.NET Core neomezuje velikost mezipaměti podle tlaku na paměť.</span><span class="sxs-lookup"><span data-stu-id="9ae66-139">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="9ae66-140">Záleží jen na vývojářské omezení velikosti mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-140">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="9ae66-141">Pomocí IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="9ae66-141">Using IMemoryCache</span></span>

<span data-ttu-id="9ae66-142">Ukládání do mezipaměti v paměti je *služby* , který se odkazuje z vaší aplikace pomocí [injektáž závislostí](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="9ae66-142">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="9ae66-143">Volání `AddMemoryCache` v `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9ae66-143">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="9ae66-144">Žádosti `IMemoryCache` instance v konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="9ae66-144">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9ae66-145">`IMemoryCache` vyžaduje balíček NuGet [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span><span class="sxs-lookup"><span data-stu-id="9ae66-145">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="9ae66-146">`IMemoryCache` vyžaduje balíček NuGet [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), která je k dispozici v [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="9ae66-146">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="9ae66-147">`IMemoryCache` vyžaduje balíček NuGet [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), která je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9ae66-147">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="9ae66-148">Následující kód používá [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) ke kontrole, jestli časem spadá do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-148">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="9ae66-149">Pokud čas neukládají do mezipaměti, je vytvořen a přidán do mezipaměti se nová položka [nastavit](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span><span class="sxs-lookup"><span data-stu-id="9ae66-149">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="9ae66-150">Aktuální čas a čas v mezipaměti se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="9ae66-150">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="9ae66-151">V mezipaměti `DateTime` hodnota zůstává v mezipaměti, i když existují požadavky v rámci časového limitu.</span><span class="sxs-lookup"><span data-stu-id="9ae66-151">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="9ae66-152">Následující obrázek ukazuje aktuální čas a starší čas načtení z mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="9ae66-152">The following image shows the current time and an older time retrieved from the cache:</span></span>

![Index zobrazení se zobrazí dva různé časy.](memory/_static/time.png)

<span data-ttu-id="9ae66-154">Následující kód používá [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) a [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) ukládání dat do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-154">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="9ae66-155">Následující kód volá [získat](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) načíst uložené v mezipaměti Doba:</span><span class="sxs-lookup"><span data-stu-id="9ae66-155">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="9ae66-156"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, a [získat](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) jsou součástí metody rozšíření [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) třídu, která rozšiřuje možnosti <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="9ae66-156"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="9ae66-157">Zobrazit [IMemoryCache metody](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) a [CacheExtensions metody](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) popis jiné metody mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-157">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="9ae66-158">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="9ae66-158">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="9ae66-159">Následující ukázka:</span><span class="sxs-lookup"><span data-stu-id="9ae66-159">The following sample:</span></span>

* <span data-ttu-id="9ae66-160">Nastaví čas, absolutní vypršení platnosti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-160">Sets the absolute expiration time.</span></span> <span data-ttu-id="9ae66-161">Toto je maximální doba, kterou položka mezipaměti a zabrání přílišnému zastarávání při průběžně obnovení klouzavé vypršení platnosti položky.</span><span class="sxs-lookup"><span data-stu-id="9ae66-161">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
* <span data-ttu-id="9ae66-162">Nastaví klouzavou dobu vypršení platnosti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-162">Sets a sliding expiration time.</span></span> <span data-ttu-id="9ae66-163">Požadavky, které přístup k této položce v mezipaměti se resetuje klouzavé vypršení platnosti hodiny.</span><span class="sxs-lookup"><span data-stu-id="9ae66-163">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="9ae66-164">Nastaví prioritu mezipaměti `CacheItemPriority.NeverRemove`.</span><span class="sxs-lookup"><span data-stu-id="9ae66-164">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="9ae66-165">Nastaví [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , která bude volána po provedení položky dojde k jeho vyřazení z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-165">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="9ae66-166">Zpětné volání je spuštěn na jiném podprocesu než kód, který odebere položku z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-166">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="9ae66-167">Použití SetSize, velikost a hodnota parametru SizeLimit omezení velikosti mezipaměti</span><span class="sxs-lookup"><span data-stu-id="9ae66-167">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="9ae66-168">A `MemoryCache` instance může volitelně zadat a vynutit omezení velikosti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-168">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="9ae66-169">Omezení velikosti paměti nemá definovanou jednotka měření vzhledem k tomu, že mezipaměť nemá žádný mechanismus pro měření velikosti položky.</span><span class="sxs-lookup"><span data-stu-id="9ae66-169">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="9ae66-170">Pokud je nastaven limit velikosti mezipaměti, musíte zadat všechny položky velikosti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-170">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="9ae66-171">Modul runtime ASP.NET Core neomezuje velikost mezipaměti podle tlaku na paměť.</span><span class="sxs-lookup"><span data-stu-id="9ae66-171">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="9ae66-172">Záleží jen na vývojářské omezení velikosti mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-172">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="9ae66-173">Zadaná velikost je v jednotkách, které vývojář klikne.</span><span class="sxs-lookup"><span data-stu-id="9ae66-173">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="9ae66-174">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9ae66-174">For example:</span></span>

* <span data-ttu-id="9ae66-175">Pokud webová aplikace byla primárně ukládání do mezipaměti řetězce, každý velikost položky mezipaměti může být délka řetězce.</span><span class="sxs-lookup"><span data-stu-id="9ae66-175">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="9ae66-176">Aplikace může určit velikost všechny položky jako 1 a maximální velikost je počet záznamů.</span><span class="sxs-lookup"><span data-stu-id="9ae66-176">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="9ae66-177">Následující kód vytvoří unitless pevnou velikost [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) přístupné [injektáž závislostí](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="9ae66-177">The following code creates a unitless fixed size [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="9ae66-178">[Hodnota parametru SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) nemá žádné jednotky.</span><span class="sxs-lookup"><span data-stu-id="9ae66-178">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) does not have units.</span></span> <span data-ttu-id="9ae66-179">Položky uložené v mezipaměti musí zadejte velikost v libovolné jednotky, které považují za nejvhodnější, pokud byla nastavena velikost mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-179">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="9ae66-180">Všichni uživatelé instance mezipaměti by měla používat stejné jednotky systému.</span><span class="sxs-lookup"><span data-stu-id="9ae66-180">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="9ae66-181">Položky nebudou zapisována do mezipaměti pokud součet velikostí položka uložená v mezipaměti přesahuje hodnotu zadanou pomocí `SizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="9ae66-181">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="9ae66-182">Pokud je nastaveno žádné omezení velikosti mezipaměti, se bude ignorovat velikost mezipaměti, nastavte v položce.</span><span class="sxs-lookup"><span data-stu-id="9ae66-182">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="9ae66-183">Následující kód registrů `MyMemoryCache` s [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="9ae66-183">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="9ae66-184">`MyMemoryCache` je vytvořen jako nezávislé mezipaměti pro součásti, které jsou tato velikost omezená mezipaměť si vědomi a vědět, jak odpovídajícím způsobem nastavit velikost položky mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-184">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="9ae66-185">Následující kód používá `MyMemoryCache`:</span><span class="sxs-lookup"><span data-stu-id="9ae66-185">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="9ae66-186">Velikost položky mezipaměti můžete nastavit [velikost](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) nebo [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="9ae66-186">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a><span data-ttu-id="9ae66-187">Závislosti mezipaměti</span><span class="sxs-lookup"><span data-stu-id="9ae66-187">Cache dependencies</span></span>

<span data-ttu-id="9ae66-188">Následující příklad ukazuje, jak do vypršení platnosti položky mezipaměti, když vyprší platnost závislé položky.</span><span class="sxs-lookup"><span data-stu-id="9ae66-188">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="9ae66-189">A `CancellationChangeToken` se přidá do položku z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-189">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="9ae66-190">Když `Cancel` je volán na `CancellationTokenSource`, obě položky mezipaměti se vyřadí.</span><span class="sxs-lookup"><span data-stu-id="9ae66-190">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="9ae66-191">Použití `CancellationTokenSource` umožňuje více položek mezipaměti vyřazení jako skupinu.</span><span class="sxs-lookup"><span data-stu-id="9ae66-191">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="9ae66-192">S `using` vzor ve výše uvedeném kódu, vytvářejí položky mezipaměti `using` bloku zdědí nastavení vypršení platnosti a aktivačních událostí.</span><span class="sxs-lookup"><span data-stu-id="9ae66-192">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="9ae66-193">Další poznámky</span><span class="sxs-lookup"><span data-stu-id="9ae66-193">Additional notes</span></span>

* <span data-ttu-id="9ae66-194">Při použití zpětné volání k znovu vytvořit položku mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="9ae66-194">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="9ae66-195">Více požadavků můžete najít hodnotu uloženou v mezipaměti klíče prázdný protože zpětného volání nebyl dokončen.</span><span class="sxs-lookup"><span data-stu-id="9ae66-195">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="9ae66-196">Výsledkem může být několik vláken opětovného vyplnění položku z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-196">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="9ae66-197">Při použití jedné položky cache a vytvořte další podřízené zkopíruje nastavení podle času vypršení platnosti a vypršení platnosti tokenů nadřazená položka.</span><span class="sxs-lookup"><span data-stu-id="9ae66-197">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="9ae66-198">Podřízené není ruční odebrání vypršela platnost, nebo aktualizace nadřazené položky.</span><span class="sxs-lookup"><span data-stu-id="9ae66-198">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="9ae66-199">Použití [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) nastavit zpětná volání, které aktivuje po položka mezipaměti je odstraněn z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9ae66-199">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ae66-200">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9ae66-200">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
