---
title: Mezipaměť v paměti v ASP.NET Core
author: rick-anderson
description: Naučte se, jak ukládat data do mezipaměti v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/memory
ms.openlocfilehash: c4d21992695828e81e03eca92f167c0a3d69c724
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627283"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Mezipaměť v paměti v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor – [Rick Anderson](https://twitter.com/RickAndMSFT), [Jan Luo](https://github.com/JunTaoLuo)a [Steve Smith](https://ardalis.com/)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Základy ukládání do mezipaměti

Ukládání do mezipaměti může významně zlepšit výkon a škálovatelnost aplikace tím, že zkracuje práci potřebnou k vygenerování obsahu. Ukládání do mezipaměti funguje nejlépe s daty, která se mění zřídka **a** jsou nákladné pro generování. Ukládání do mezipaměti vytváří kopii dat, která může být vrácena mnohem rychleji než ze zdroje. Aplikace by měly být napsány a testovány, aby **nikdy nebyly** závislé na datech uložených v mezipaměti.

ASP.NET Core podporuje několik různých mezipamětí. Nejjednodušší mezipaměť je založena na [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache). `IMemoryCache` představuje mezipaměť uloženou v paměti webového serveru. Aplikace spuštěné na serverové farmě (více serverů) by měly zajistit, aby relace byly při použití mezipaměti v paměti rychlé. Relace typu Sticky se zajišťují, aby následné požadavky z klienta přešly na stejný server. Například Azure Web Apps používá [Směrování žádostí o aplikace](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) ke směrování všech dalších požadavků na stejný server.

Nesticky relace ve webové farmě vyžadují [distribuovanou mezipaměť](distributed.md) , aby nedocházelo k problémům s konzistencí mezipaměti. Pro některé aplikace může distribuovaná mezipaměť podporovat větší škálování než mezipaměť v paměti. Použití distribuované mezipaměti přesměruje paměť mezipaměti do externího procesu.

Mezipaměť v paměti může ukládat libovolný objekt. Rozhraní distribuované mezipaměti je omezené na `byte[]` . Položky mezipaměti v paměti a distribuované mezipaměti ukládají jako páry klíč-hodnota.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Balíček NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) se dá použít s:

* .NET Standard 2,0 nebo novější.
* Jakákoli [implementace .NET](/dotnet/standard/net-standard#net-implementation-support) , která cílí na .NET Standard 2,0 nebo novější. Například ASP.NET Core 2,0 nebo novější.
* .NET Framework 4,5 nebo novější.

[Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (popsaná v tomto článku) se doporučuje, `System.Runtime.Caching` / `MemoryCache` protože je lépe integrovaná do ASP.NET Core. Například `IMemoryCache` funguje nativně s [vkládáním závislostí](xref:fundamentals/dependency-injection)ASP.NET Core.

Použijte `System.Runtime.Caching` / `MemoryCache` jako most kompatibility při přenosu kódu z ASP.NET 4. x do ASP.NET Core.

## <a name="cache-guidelines"></a>Pokyny pro mezipaměť

* Kód by měl vždy mít možnost Fallback načíst data a **nemusí** být závislý na dostupné hodnotě uložené v mezipaměti.
* Mezipaměť používá prostředek omezených, paměť. Omezit nárůst mezipaměti:
  * Nepoužívejte **externí** vstup jako klíče mezipaměti.
  * K omezení růstu mezipaměti použijte vypršení platnosti.
  * [Pro omezení velikosti mezipaměti použijte možnost setSize, Size a SizeLimit](#use-setsize-size-and-sizelimit-to-limit-cache-size). Modul runtime ASP.NET Core **neomezuje velikost** mezipaměti na základě tlaku paměti. Velikost mezipaměti můžete omezit na vývojáře.

## <a name="use-imemorycache"></a>Použití IMemoryCache

> [!WARNING]
> Použití *sdílené* mezipaměti paměti ze [Injektáže](xref:fundamentals/dependency-injection) a volání závislosti `SetSize` , `Size` nebo `SizeLimit` pro omezení velikosti mezipaměti může způsobit selhání aplikace. Pokud je u mezipaměti nastaveno omezení velikosti, všechny položky musí při přidávání určovat velikost. To může vést k problémům, protože vývojáři nemusí mít úplnou kontrolu nad tím, co používá sdílenou mezipaměť. Entity Framework Core například používá sdílenou mezipaměť a neurčuje velikost. Pokud aplikace nastaví omezení velikosti mezipaměti a používá EF Core, aplikace vyvolá `InvalidOperationException` .
> Při použití `SetSize` , `Size` nebo `SizeLimit` k omezení mezipaměti vytvořte pro ukládání do mezipaměti typ singleton. Další informace a příklad najdete v tématu [použití setSize, Size a SizeLimit k omezení velikosti mezipaměti](#use-setsize-size-and-sizelimit-to-limit-cache-size).
> Sdílená mezipaměť je jedna sdílená jinými architekturami nebo knihovnami. EF Core například používá sdílenou mezipaměť a neurčuje velikost. 

Mezipaměť v paměti je *Služba* , na kterou se odkazuje z aplikace pomocí [Injektáže závislostí](xref:fundamentals/dependency-injection). Vyžádejte `IMemoryCache` instanci v konstruktoru:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

Následující kód používá [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) ke kontrole, zda je čas v mezipaměti. Pokud čas není uložen v mezipaměti, je vytvořena nová položka a přidána do mezipaměti se [sadou](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_). `CacheKeys`Třída je součástí ukázky stahování.

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

Aktuální čas a čas v mezipaměti se zobrazí:

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

Hodnota uložená v mezipaměti `DateTime` zůstává v mezipaměti a v době, kdy jsou požadavky v rámci časového limitu.

Následující kód používá [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) a [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) k ukládání dat do mezipaměti.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Následující kód volá [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) k načtení času uloženého v mezipaměti:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

Následující kód Získá nebo vytvoří položku uloženou v mezipaměti s absolutním vypršením platnosti:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

Sada položek v mezipaměti s klouzavé vypršení platnosti je ohrožena pouze v případě, že je zastaralá. Pokud se k němu dostanete častěji než klouzavé období vypršení platnosti, tato položka nikdy nevyprší. Zkombinujte klouzavé vypršení platnosti s absolutním vypršením platnosti a zajistěte tak, že platnost položky vyprší po uplynutí absolutního času vypršení. Absolutní vypršení platnosti nastaví horní mez na dobu, po kterou může být položka ukládána do mezipaměti, a stále umožňuje, aby položka vypršela dříve, pokud se nepožaduje v klouzavém intervalu vypršení platnosti. Při zadání absolutního i klouzavého vypršení platnosti jsou vypršení platnosti logicky ORed. Pokud je interval klouzavého vypršení platnosti *nebo* absolutního času vypršení platnosti, položka bude z mezipaměti vyřazena.

Následující kód Získá nebo vytvoří položku uloženou v mezipaměti s použitím klouzavého *i* absolutního vypršení platnosti:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

Předchozí kód zaručuje, že data nebudou ukládána do mezipaměti déle než absolutní čas.

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> a <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> jsou rozšiřující metody <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> třídy. Tyto metody rozšiřuje schopnost <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

Následující ukázka:

* Nastaví posuvný čas vypršení platnosti. Požadavky, které přistupují k této položce v mezipaměti, budou resetovat hodiny klouzavého vypršení platnosti.
* Nastaví prioritu mezipaměti na [CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).
* Nastaví [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , který bude volán po vyřazení položky z mezipaměti. Zpětné volání je spuštěno v jiném vlákně z kódu, který odebere položku z mezipaměti.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Omezení velikosti mezipaměti pomocí SetSize, velikosti a SizeLimit

`MemoryCache`Instance může volitelně určovat a vymáhat omezení velikosti. Omezení velikosti mezipaměti nemá definovanou měrnou jednotku, protože mezipaměť nemá žádný mechanismus pro měření velikosti položek. Pokud je nastaven limit velikosti mezipaměti, musí všechny položky určovat velikost. Modul runtime ASP.NET Core neomezuje velikost mezipaměti na základě tlaku paměti. Velikost mezipaměti můžete omezit na vývojáře. Zadaná velikost je v jednotkách, které vývojář zvolí.

Příklad:

* Pokud byla webová aplikace primárně do mezipaměti řetězců, každá velikost položky mezipaměti může být délka řetězce.
* Aplikace může určit velikost všech položek jako 1 a limit velikosti je počet položek.

Pokud <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> není nastaveno, mezipaměť se rozrůstá bez vazby. Modul runtime ASP.NET Core neořízne mezipaměť, pokud je systémová paměť nízká. Aplikace musí být navrženy tak, aby:

* Omezte nárůst mezipaměti.
* Zavolejte <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> nebo <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> když je dostupná paměť omezená:

Následující kód vytvoří pevnou velikost jednotky <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> dostupnou pro [vkládání závislostí](xref:fundamentals/dependency-injection).

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` nemá jednotky. Položky v mezipaměti musí určovat velikost v jakémkoli z jednotek, které považují za nejvhodnější, pokud byl nastaven limit velikosti mezipaměti. Všichni uživatelé instance mezipaměti by měli používat stejný systém jednotek. Záznam nebude uložen do mezipaměti, pokud součet velikostí záznamů uložených v mezipaměti překročí hodnotu zadanou parametrem `SizeLimit` . Pokud není nastavené žádné omezení velikosti mezipaměti, bude velikost mezipaměti nastavená u položky ignorována.

Následující kód se registruje v `MyMemoryCache` kontejneru [Injektáže pro vkládání závislostí](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

`MyMemoryCache` je vytvořen jako nezávislá mezipaměť paměti pro součásti, které mají informace o této velikosti omezené mezipaměti a také o tom, jak správně nastavit velikost položky mezipaměti.

Následující kód používá `MyMemoryCache` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

Velikost položky mezipaměti lze nastavit pomocí <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> metod rozšíření:

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` pokusí se odebrat zadané procento mezipaměti v následujícím pořadí:

* Všechny položky, jejichž platnost vypršela.
* Položky podle priority. Nejprve se odeberou položky s nejnižší prioritou.
* Nejméně naposledy použité objekty.
* Položky s nejdřívějším absolutním vypršením platnosti.
* Položky s nejdřívějším klouzavém vypršením platnosti.

Připnuté položky s prioritou <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> nejsou nikdy odebrány. Následující kód odstraní položku mezipaměti a volání `Compact` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Další informace najdete v tématu [Compact source na GitHubu](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Závislosti mezipaměti

Následující příklad ukazuje, jak vyprší platnost položky mezipaměti, pokud vyprší platnost závislé položky. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>Přidá se k položce v mezipaměti. Když `Cancel` je volána na `CancellationTokenSource` , obě položky mezipaměti jsou vyřazeny.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

Použití <xref:System.Threading.CancellationTokenSource> Možnosti umožňuje vyřazení více položek mezipaměti jako skupiny. Pomocí `using` vzoru ve výše uvedeném kódu budou položky mezipaměti vytvořené uvnitř `using` bloku dědit triggery a nastavení vypršení platnosti.

## <a name="additional-notes"></a>Další poznámky

* K vypršení platnosti nedojde na pozadí. Neexistuje žádný časovač, který aktivně hledá neplatné položky v mezipaměti. Jakákoli aktivita v mezipaměti ( `Get` , `Set` , `Remove` ) může aktivovat kontrolu na pozadí pro položky s vypršenou platností. Časovač na `CancellationTokenSource` ( <xref:System.Threading.CancellationTokenSource.CancelAfter*> ) také odebere položku a aktivuje kontrolu pro položky, jejichž platnost vypršela. Následující příklad používá [CancellationTokenSource (TimeSpan)](/dotnet/api/system.threading.cancellationtokensource.-ctor) pro registrovaný token. Když se tento token aktivuje, okamžitě odstraní položku a aktivuje zpětná volání vyřazení:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ae)]

* Při použití zpětného volání k naplnění položky mezipaměti:

  * Více požadavků může najít hodnotu klíče uloženou v mezipaměti jako prázdné, protože zpětné volání nebylo dokončeno.
  * To může mít za následek, že v několika vláknech se znovu naplní položka v mezipaměti.

* Když se jedna položka mezipaměti používá k vytvoření jiného, podřízená položka zkopíruje tokeny vypršení platnosti nadřazené položky a nastavení vypršení platnosti na základě času. K podřízenému objektu nevypršela platnost ručním odebráním nebo aktualizací nadřazené položky.

* Slouží <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> k nastavení zpětných volání, která budou aktivována po vyřazení položky mezipaměti z mezipaměti.
* Pro většinu aplikací `IMemoryCache` je povolený. Například volání,, `AddMvc` `AddControllersWithViews` `AddRazorPages` , `AddMvcCore().AddRazorViewEngine` a mnoho dalších `Add{Service}` metod v `ConfigureServices` umožňuje `IMemoryCache` . U aplikací, které nevolají jednu z předchozích `Add{Service}` metod, může být nutné zavolat <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> na `ConfigureServices` .

## <a name="background-cache-update"></a>Aktualizace mezipaměti na pozadí

Použijte [službu na pozadí](xref:fundamentals/host/hosted-services) , například <xref:Microsoft.Extensions.Hosting.IHostedService> k aktualizaci mezipaměti. Služba na pozadí může přepočítat položky a pak je přiřadit do mezipaměti pouze v případě, že jsou připravené.

## <a name="additional-resources"></a>Další zdroje informací

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
Autor – [Rick Anderson](https://twitter.com/RickAndMSFT), [Jan Luo](https://github.com/JunTaoLuo)a [Steve Smith](https://ardalis.com/)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Základy ukládání do mezipaměti

Ukládání do mezipaměti může významně zlepšit výkon a škálovatelnost aplikace tím, že zkracuje práci potřebnou k vygenerování obsahu. Ukládání do mezipaměti funguje nejlépe s daty, která se mění zřídka. Ukládání do mezipaměti vytváří kopii dat, která může být vrácena mnohem rychleji než z původního zdroje. Kód by měl být napsán a testován, aby **nikdy nebyl** závislý na datech uložených v mezipaměti.

ASP.NET Core podporuje několik různých mezipamětí. Nejjednodušší mezipaměť je založena na [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), který představuje mezipaměť uloženou v paměti webového serveru. Aplikace, které běží na serverové farmě (více serverů), by měly zajistit, aby relace byly při použití mezipaměti v paměti rychlé. Relace typu Sticky se zajišťují, aby pozdější požadavky z klienta přešly na stejný server. Například Azure Web Apps používá [Směrování žádostí o aplikace](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) ke směrování všech požadavků od uživatelského agenta na stejný server.

Nesticky relace ve webové farmě vyžadují [distribuovanou mezipaměť](distributed.md) , aby nedocházelo k problémům s konzistencí mezipaměti. Pro některé aplikace může distribuovaná mezipaměť podporovat větší škálování než mezipaměť v paměti. Použití distribuované mezipaměti přesměruje paměť mezipaměti do externího procesu.

Mezipaměť v paměti může ukládat libovolný objekt. Rozhraní distribuované mezipaměti je omezené na `byte[]` . Položky mezipaměti v paměti a distribuované mezipaměti ukládají jako páry klíč-hodnota.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Balíček NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) se dá použít s:

* .NET Standard 2,0 nebo novější.
* Jakákoli [implementace .NET](/dotnet/standard/net-standard#net-implementation-support) , která cílí na .NET Standard 2,0 nebo novější. Například ASP.NET Core 2,0 nebo novější.
* .NET Framework 4,5 nebo novější.

[Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (popsaná v tomto článku) se doporučuje, `System.Runtime.Caching` / `MemoryCache` protože je lépe integrovaná do ASP.NET Core. Například `IMemoryCache` funguje nativně s [vkládáním závislostí](xref:fundamentals/dependency-injection)ASP.NET Core.

Použijte `System.Runtime.Caching` / `MemoryCache` jako most kompatibility při přenosu kódu z ASP.NET 4. x do ASP.NET Core.

## <a name="cache-guidelines"></a>Pokyny pro mezipaměť

* Kód by měl vždy mít možnost Fallback načíst data a **nemusí** být závislý na dostupné hodnotě uložené v mezipaměti.
* Mezipaměť používá prostředek omezených, paměť. Omezit nárůst mezipaměti:
  * Nepoužívejte **externí** vstup jako klíče mezipaměti.
  * K omezení růstu mezipaměti použijte vypršení platnosti.
  * [Pro omezení velikosti mezipaměti použijte možnost setSize, Size a SizeLimit](#use-setsize-size-and-sizelimit-to-limit-cache-size). Modul runtime ASP.NET Core neomezuje velikost mezipaměti na základě tlaku paměti. Velikost mezipaměti můžete omezit na vývojáře.

## <a name="using-imemorycache"></a>Použití IMemoryCache

> [!WARNING]
> Použití *sdílené* mezipaměti paměti ze [Injektáže](xref:fundamentals/dependency-injection) a volání závislosti `SetSize` , `Size` nebo `SizeLimit` pro omezení velikosti mezipaměti může způsobit selhání aplikace. Pokud je u mezipaměti nastaveno omezení velikosti, všechny položky musí při přidávání určovat velikost. To může vést k problémům, protože vývojáři nemusí mít úplnou kontrolu nad tím, co používá sdílenou mezipaměť. Entity Framework Core například používá sdílenou mezipaměť a neurčuje velikost. Pokud aplikace nastaví omezení velikosti mezipaměti a používá EF Core, aplikace vyvolá `InvalidOperationException` .
> Při použití `SetSize` , `Size` nebo `SizeLimit` k omezení mezipaměti vytvořte pro ukládání do mezipaměti typ singleton. Další informace a příklad najdete v tématu [použití setSize, Size a SizeLimit k omezení velikosti mezipaměti](#use-setsize-size-and-sizelimit-to-limit-cache-size).

Mezipaměť v paměti je *Služba* , na kterou se odkazuje z vaší aplikace pomocí [Injektáže závislostí](../../fundamentals/dependency-injection.md). Zavolat `AddMemoryCache` na `ConfigureServices` :

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Vyžádejte `IMemoryCache` instanci v konstruktoru:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

`IMemoryCache` vyžaduje balíček NuGet [Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), který je k dispozici ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

Následující kód používá [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) ke kontrole, zda je čas v mezipaměti. Pokud čas není uložen v mezipaměti, je vytvořena nová položka a přidána do mezipaměti se [sadou](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

Aktuální čas a čas v mezipaměti se zobrazí:

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

Hodnota uložená v mezipaměti `DateTime` zůstává v mezipaměti a v době, kdy jsou požadavky v rámci časového limitu. Následující obrázek ukazuje aktuální čas a starší čas načtený z mezipaměti:

![Zobrazení indexu se dvěma různými časy zobrazení](memory/_static/time.png)

Následující kód používá [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) a [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) k ukládání dat do mezipaměti.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Následující kód volá [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) k načtení času uloženého v mezipaměti:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>metody rozšíření, a [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) jsou součástí třídy [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) , která rozšiřuje možnosti <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . Popis dalších metod mezipaměti naleznete v tématu [metody IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) a [metody CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) .

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

Následující ukázka:

* Nastaví posuvný čas vypršení platnosti. Požadavky, které přistupují k této položce v mezipaměti, budou resetovat hodiny klouzavého vypršení platnosti.
* Nastaví prioritu mezipaměti na `CacheItemPriority.NeverRemove` .
* Nastaví [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , který bude volán po vyřazení položky z mezipaměti. Zpětné volání je spuštěno v jiném vlákně z kódu, který odebere položku z mezipaměti.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Omezení velikosti mezipaměti pomocí SetSize, velikosti a SizeLimit

`MemoryCache`Instance může volitelně určovat a vymáhat omezení velikosti. Omezení velikosti mezipaměti nemá definovanou měrnou jednotku, protože mezipaměť nemá žádný mechanismus pro měření velikosti položek. Pokud je nastaven limit velikosti mezipaměti, musí všechny položky určovat velikost. Modul runtime ASP.NET Core neomezuje velikost mezipaměti na základě tlaku paměti. Velikost mezipaměti můžete omezit na vývojáře. Zadaná velikost je v jednotkách, které vývojář zvolí.

Příklad:

* Pokud byla webová aplikace primárně do mezipaměti řetězců, každá velikost položky mezipaměti může být délka řetězce.
* Aplikace může určit velikost všech položek jako 1 a limit velikosti je počet položek.

Pokud není <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> nastaven, mezipaměť se rozrůstá bez vazby. Modul runtime ASP.NET Core neořízne mezipaměť, pokud je systémová paměť nízká. Aplikace jsou v podstatě navrženy na:

* Omezte nárůst mezipaměti.
* Zavolejte <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> nebo <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> když je dostupná paměť omezená:

Následující kód vytvoří pevnou velikost jednotky <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> dostupnou pro [vkládání závislostí](xref:fundamentals/dependency-injection).

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` nemá jednotky. Položky v mezipaměti musí určovat velikost v jakémkoli z jednotek, které považují za nejvhodnější, pokud byl nastaven limit velikosti mezipaměti. Všichni uživatelé instance mezipaměti by měli používat stejný systém jednotek. Záznam nebude uložen do mezipaměti, pokud součet velikostí záznamů uložených v mezipaměti překročí hodnotu zadanou parametrem `SizeLimit` . Pokud není nastavené žádné omezení velikosti mezipaměti, bude velikost mezipaměti nastavená u položky ignorována.

Následující kód se registruje v `MyMemoryCache` kontejneru [Injektáže pro vkládání závislostí](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` je vytvořen jako nezávislá mezipaměť paměti pro součásti, které mají informace o této velikosti omezené mezipaměti a také o tom, jak správně nastavit velikost položky mezipaměti.

Následující kód používá `MyMemoryCache` :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

Velikost položky mezipaměti lze nastavit podle [velikosti](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) nebo metody rozšíření [setSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` pokusí se odebrat zadané procento mezipaměti v následujícím pořadí:

* Všechny položky, jejichž platnost vypršela.
* Položky podle priority. Nejprve se odeberou položky s nejnižší prioritou.
* Nejméně naposledy použité objekty.
* Položky s nejdřívějším absolutním vypršením platnosti.
* Položky s nejdřívějším klouzavém vypršením platnosti.

Připnuté položky s prioritou <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> nejsou nikdy odebrány.

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Další informace najdete v tématu [Compact source na GitHubu](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Závislosti mezipaměti

Následující příklad ukazuje, jak vyprší platnost položky mezipaměti, pokud vyprší platnost závislé položky. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>Přidá se k položce v mezipaměti. Když `Cancel` je volána na `CancellationTokenSource` , obě položky mezipaměti jsou vyřazeny.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

Použití `CancellationTokenSource` Možnosti umožňuje vyřazení více položek mezipaměti jako skupiny. Pomocí `using` vzoru ve výše uvedeném kódu budou položky mezipaměti vytvořené uvnitř `using` bloku dědit triggery a nastavení vypršení platnosti.

## <a name="additional-notes"></a>Další poznámky

* Při použití zpětného volání k naplnění položky mezipaměti:

  * Více požadavků může najít hodnotu klíče uloženou v mezipaměti jako prázdné, protože zpětné volání nebylo dokončeno.
  * To může mít za následek, že v několika vláknech se znovu naplní položka v mezipaměti.

* Když se jedna položka mezipaměti používá k vytvoření jiného, podřízená položka zkopíruje tokeny vypršení platnosti nadřazené položky a nastavení vypršení platnosti na základě času. K podřízenému objektu nevypršela platnost ručním odebráním nebo aktualizací nadřazené položky.

* Pomocí [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) můžete nastavit zpětná volání, která budou aktivována po vyřazení položky mezipaměti z mezipaměti.

## <a name="background-cache-update"></a>Aktualizace mezipaměti na pozadí

Použijte [službu na pozadí](xref:fundamentals/host/hosted-services) , například <xref:Microsoft.Extensions.Hosting.IHostedService> k aktualizaci mezipaměti. Služba na pozadí může přepočítat položky a pak je přiřadit do mezipaměti pouze v případě, že jsou připravené.

## <a name="additional-resources"></a>Další materiály

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
