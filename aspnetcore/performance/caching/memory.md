---
title: Mezipaměť v paměti v ASP.NET Core
author: rick-anderson
description: Naučte se, jak ukládat data do mezipaměti v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 8/11/2019
uid: performance/caching/memory
ms.openlocfilehash: 474f71225371ea89b023ee077d4ecc03e9751681
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030318"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Mezipaměť v paměti v ASP.NET Core

Autor – [Rick Anderson](https://twitter.com/RickAndMSFT), [Jan Luo](https://github.com/JunTaoLuo)a [Steve Smith](https://ardalis.com/)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Základy ukládání do mezipaměti

Ukládání do mezipaměti může významně zlepšit výkon a škálovatelnost aplikace tím, že zkracuje práci potřebnou k vygenerování obsahu. Ukládání do mezipaměti funguje nejlépe s daty, která se mění zřídka. Ukládání do mezipaměti vytváří kopii dat, která může být vrácena mnohem rychleji než z původního zdroje. Aplikace by měly být napsány a testovány, aby **nikdy nebyly** závislé na datech uložených v mezipaměti.

ASP.NET Core podporuje několik různých mezipamětí. Nejjednodušší mezipaměť je založena na [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), který představuje mezipaměť uloženou v paměti webového serveru. Aplikace, které běží na serverové farmě více serverů, by měly zajistit, aby relace byly při použití mezipaměti v paměti rychlé. Relace typu Sticky se zajišťují, aby následné požadavky z klienta přešly na stejný server. Například Azure Web Apps používá [Směrování žádostí o aplikace](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) ke směrování všech dalších požadavků na stejný server.

Nesticky relace ve webové farmě vyžadují [distribuovanou mezipaměť](distributed.md) , aby nedocházelo k problémům s konzistencí mezipaměti. Pro některé aplikace může distribuovaná mezipaměť podporovat větší škálování než mezipaměť v paměti. Použití distribuované mezipaměti přesměruje paměť mezipaměti do externího procesu.

::: moniker range="< aspnetcore-2.0"

Mezipaměť vyřadí položky mezipaměti s tlakem paměti, pokud není [Priorita mezipaměti](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) nastavena na `CacheItemPriority.NeverRemove`hodnotu. `IMemoryCache` Můžete nastavit, `CacheItemPriority` aby se upravila priorita, se kterou mezipaměť vyloučí položky pod tlakem.

::: moniker-end

Mezipaměť v paměti může ukládat libovolný objekt. rozhraní distribuované mezipaměti je omezené na `byte[]`. Položky mezipaměti v paměti a distribuované mezipaměti ukládají jako páry klíč-hodnota.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache>([Balíček NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) se dá použít s:

* .NET Standard 2,0 nebo novější.
* Jakákoli [implementace .NET](/dotnet/standard/net-standard#net-implementation-support) , která cílí na .NET Standard 2,0 nebo novější. Například ASP.NET Core 2,0 nebo novější.
* .NET Framework 4,5 nebo novější.

[Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (popsaná v `System.Runtime.Caching` / `MemoryCache` tomto článku) se doporučuje, protože je lépe integrovaná do ASP.NET Core. Například `IMemoryCache` funguje nativně s [vkládáním závislostí](xref:fundamentals/dependency-injection)ASP.NET Core.

Použijte `System.Runtime.Caching` jakomostkompatibility`MemoryCache` při přenosu kódu z ASP.NET 4. x do ASP.NET Core. /

## <a name="cache-guidelines"></a>Pokyny pro mezipaměť

* Kód by měl vždy mít možnost Fallback načíst data a **nemusí** být závislý na dostupné hodnotě uložené v mezipaměti.
* Mezipaměť používá prostředek omezených, paměť. Omezit nárůst mezipaměti:
  * Nepoužívejte externí vstup jako klíče mezipaměti.
  * K omezení růstu mezipaměti použijte vypršení platnosti.
  * [Pro omezení velikosti mezipaměti použijte možnost setSize, Size a SizeLimit](#use-setsize-size-and-sizelimit-to-limit-cache-size). Modul runtime ASP.NET Core neomezuje velikost mezipaměti na základě tlaku paměti. Velikost mezipaměti můžete omezit na vývojáře.

## <a name="using-imemorycache"></a>Použití IMemoryCache

> [!WARNING]
> Použití *sdílené* mezipaměti paměti ze [Injektáže](xref:fundamentals/dependency-injection) a volání `SetSize`závislosti, `Size`nebo `SizeLimit` pro omezení velikosti mezipaměti může způsobit selhání aplikace. Pokud je u mezipaměti nastaveno omezení velikosti, všechny položky musí při přidávání určovat velikost. To může vést k problémům, protože vývojáři nemusí mít úplnou kontrolu nad tím, co používá sdílenou mezipaměť. Entity Framework Core například používá sdílenou mezipaměť a neurčuje velikost. Pokud aplikace nastaví omezení velikosti mezipaměti a používá EF Core, aplikace vyvolá `InvalidOperationException`.
> Při použití `SetSize`, `Size`nebo `SizeLimit` k omezení mezipaměti vytvořte pro ukládání do mezipaměti typ singleton. Další informace a příklad najdete v tématu [použití setSize, Size a SizeLimit k omezení velikosti mezipaměti](#use-setsize-size-and-sizelimit-to-limit-cache-size).

Mezipaměť v paměti je *Služba* , na kterou se odkazuje z vaší aplikace pomocí [Injektáže závislostí](xref:fundamentals/dependency-injection). Zavolat `AddMemoryCache` na `ConfigureServices`:

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Vyžádejte `IMemoryCache` instanci v konstruktoru:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

`IMemoryCache`vyžaduje balíček NuGet [Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`IMemoryCache`vyžaduje balíček NuGet [Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), který je k dispozici ve [službě Microsoft. AspNetCore. All Metapackage](xref:fundamentals/metapackage).

::: moniker-end

::: moniker range="> aspnetcore-2.0"

`IMemoryCache`vyžaduje balíček NuGet [Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), který je k dispozici ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

::: moniker-end

Následující kód používá [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) ke kontrole, zda je čas v mezipaměti. Pokud čas není uložen v mezipaměti, je vytvořena nová položka a přidána do mezipaměti se [sadou](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

Aktuální čas a čas v mezipaměti se zobrazí:

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

Hodnota uložená `DateTime` v mezipaměti zůstává v mezipaměti a v době, kdy jsou požadavky v rámci časového limitu. Následující obrázek ukazuje aktuální čas a starší čas načtený z mezipaměti:

![Zobrazení indexu se dvěma různými časy zobrazení](memory/_static/time.png)

Následující kód používá [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) a [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) k ukládání dat do mezipaměti.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Následující kód volá [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) k načtení času uloženého v mezipaměti:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>metody <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>rozšíření, a [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) jsou součástí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>třídy [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) , která rozšiřuje možnosti. Popis dalších metod mezipaměti naleznete v tématu [metody IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) a [metody CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) .

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

Následující ukázka:

* Nastaví absolutní čas vypršení platnosti. Toto je maximální doba, po kterou může být položka ukládána do mezipaměti, a zabraňuje tomu, aby se položka přestala být příliš zastaralá, když se průběžná doba
* Nastaví posuvný čas vypršení platnosti. Požadavky, které přistupují k této položce v mezipaměti, budou resetovat hodiny klouzavého vypršení platnosti.
* Nastaví prioritu mezipaměti na `CacheItemPriority.NeverRemove`.
* Nastaví [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , který bude volán po vyřazení položky z mezipaměti. Zpětné volání je spuštěno v jiném vlákně z kódu, který odebere položku z mezipaměti.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Omezení velikosti mezipaměti pomocí SetSize, velikosti a SizeLimit

`MemoryCache` Instance může volitelně určovat a vymáhat omezení velikosti. Omezení velikosti paměti nemá definovanou měrnou jednotku, protože mezipaměť nemá žádný mechanismus pro měření velikosti položek. Pokud je nastaven limit velikosti paměti mezipaměti, všechny položky musí určovat velikost. Modul runtime ASP.NET Core neomezuje velikost mezipaměti na základě tlaku paměti. Velikost mezipaměti můžete omezit na vývojáře. Zadaná velikost je v jednotkách, které vývojář zvolí.

Příklad:

* Pokud byla webová aplikace primárně do mezipaměti řetězců, každá velikost položky mezipaměti může být délka řetězce.
* Aplikace může určit velikost všech položek jako 1 a limit velikosti je počet položek.

Následující kód vytvoří pevnou velikost jednotky [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) přístupná pro [vkládání závislostí](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) nemá jednotky. Položky v mezipaměti musí určovat velikost v jakémkoli z jednotek, které považují za nejvhodnější, pokud byla nastavena velikost paměti mezipaměti. Všichni uživatelé instance mezipaměti by měli používat stejný systém jednotek. Záznam nebude uložen do mezipaměti, pokud součet velikostí záznamů uložených v mezipaměti překročí hodnotu zadanou parametrem `SizeLimit`. Pokud není nastavené žádné omezení velikosti mezipaměti, bude velikost mezipaměti nastavená u položky ignorována.

Následující kód se registruje `MyMemoryCache` v kontejneru injektáže pro [vkládání závislostí](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache`je vytvořen jako nezávislá mezipaměť paměti pro součásti, které mají informace o této velikosti omezené mezipaměti a také o tom, jak správně nastavit velikost položky mezipaměti.

Následující kód používá `MyMemoryCache`:

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

Velikost položky mezipaměti lze nastavit podle [velikosti](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) nebo metody rozšíření [setSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a>Závislosti mezipaměti

Následující příklad ukazuje, jak vyprší platnost položky mezipaměti, pokud vyprší platnost závislé položky. `CancellationChangeToken` Přidá se k položce v mezipaměti. Když `Cancel` je volána `CancellationTokenSource`na, obě položky mezipaměti jsou vyřazeny.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

Použití možnosti `CancellationTokenSource` umožňuje vyřazení více položek mezipaměti jako skupiny. Pomocí vzoru ve výše uvedeném kódu budou položky mezipaměti vytvořené `using` uvnitř bloku dědit triggery a nastavení vypršení platnosti. `using`

## <a name="additional-notes"></a>Další poznámky

* Při použití zpětného volání k naplnění položky mezipaměti:

  * Více požadavků může najít hodnotu klíče uloženou v mezipaměti jako prázdné, protože zpětné volání nebylo dokončeno.
  * To může mít za následek, že v několika vláknech se znovu naplní položka v mezipaměti.

* Když se jedna položka mezipaměti používá k vytvoření jiného, podřízená položka zkopíruje tokeny vypršení platnosti nadřazené položky a nastavení vypršení platnosti na základě času. K podřízenému objektu nevypršela platnost ručním odebráním nebo aktualizací nadřazené položky.

* Pomocí [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) můžete nastavit zpětná volání, která budou aktivována po vyřazení položky mezipaměti z mezipaměti.

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
