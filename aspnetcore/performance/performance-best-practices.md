---
title: ASP.NET Core Performance Best Practices
author: mjrousos
description: Tipy pro zvýšení výkonu aplikací ASP.NET Core a předcházení běžné problémy s výkonem.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 1/9/2019
uid: performance/performance-best-practices
ms.openlocfilehash: 25aa4c1e22ead7db4775c6e5e81b6fd627c6d7a6
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099062"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core Performance Best Practices

Podle [Mike Rousos](https://github.com/mjrousos)

Toto téma poskytuje pokyny pro výkon osvědčených postupů pomocí ASP.NET Core.

<a name="hot"></a> V tomto dokumentu horké kódové cestě je definován jako cestu kódu, který se často nazývá a kde většinu doby provádění dojde. Cesty k výměně kódu obvykle omezit horizontální navýšení kapacity aplikace a výkonu.

## <a name="cache-aggressively"></a>Agresivní do mezipaměti

Ukládání do mezipaměti je podrobněji popsána několik částí tohoto dokumentu. Další informace naleznete v tématu <xref:performance/caching/response>.

## <a name="avoid-blocking-calls"></a>Předcházet zablokování volání

Zpracovat velký počet požadavků současně by se měly navrhovat aplikace ASP.NET Core. Asynchronní rozhraní API umožňují malé fondu vláken zpracování tisíce souběžných požadavků tím, že není na neblokují volání. Nečekejte na synchronní časově náročné úkoly k dokončení vlákna můžete pracovat na jiná žádost.

Běžný problém výkon v aplikacích ASP.NET Core je neblokují volání, které mohou být asynchronní. Mnoho synchronní volání blokování vede k [vyčerpání fondu vláken](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) a snížení doby odezvy.

**Ne**:

* Blokovat spouštění asynchronní voláním [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) nebo [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Získejte zámky v běžných cest kódu. Aplikace ASP.NET Core se většina výkonné při navržen k paralelnímu spuštění kódu.

**Proveďte**:

* Ujistěte se, [horké cesty kódu](#hot) asynchronní.
* Asynchronní volání přístup k datům a dlouho běžící operace rozhraní API.
* Ujistěte se, řadiče nebo Razor akce stránek asynchronní. Celý zásobník volání musí být asynchronní, aby bylo možné využívat [async/await](/dotnet/csharp/programming-guide/concepts/async/) vzory.

Profiler, jako jsou [PerfView](https://github.com/Microsoft/perfview) lze použít k vyhledání často přidávaný do vlákna [vlákna fondu](/windows/desktop/procthread/thread-pool). `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` Událost ukazuje na vlákno přidávaný do fondu vláken. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a>Minimalizovat přidělení velkých objektů

<!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core --> [Systému uvolňování paměti .NET Core](/dotnet/standard/garbage-collection/) spravuje přidělování a uvolňování paměti automaticky v aplikacích ASP.NET Core. Automatické uvolňování paměti obecně znamená, že vývojáři nemusíte se starat o tom, jak a kdy je paměť uvolněna. Však čištění neodkazovaná objekty trvá čas procesoru, aby vývojáři měli minimalizovat přiděluje objekty ve [horké cesty kódu](#hot). Uvolňování paměti je zvláště nákladné na velké objekty (> 85 kB). Velké objekty jsou uloženy na [haldy pro velké objekty](/dotnet/standard/garbage-collection/large-object-heap) a vyžadují úplné (2. generace) uvolňování paměti pro vyčištění. Na rozdíl od generace 0 a 1. generace kolekce 2. generace kolekce vyžaduje spuštění aplikace chcete dočasně pozastavit. Časté přidělování a rušení přidělení velkých objektů může způsobit nekonzistentní výkonu.

Doporučení:

* **Proveďte** zvažte možnost ukládání do mezipaměti velké objekty, které jsou často používány. Ukládání do mezipaměti velké objekty brání náročné přidělení.
* **Proveďte** fondu vyrovnávací paměti s použitím [ `ArrayPool<T>` ](/dotnet/api/system.buffers.arraypool-1) ukládat velké pole.
* **Ne** přidělení paměti pro mnoho, krátkodobých velké objekty na [horké cesty kódu](#hot).

Problémy s pamětí jako předchozí můžete zjistit kontrolou statistiky uvolňování paměti kolekce (GC) v [PerfView](https://github.com/Microsoft/perfview) a zkontrolujete:

* Čas pozastavení kolekce uvolnění paměti.
* Jaké je procento času procesoru je trvání uvolňování paměti.
* Kolik kolekce uvolnění paměti jsou 0, 1 a 2. generace.

Další informace najdete v tématu [uvolňování paměti a výkonu](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access"></a>Optimalizace přístupu k datům

Interakce se úložiště dat nebo jiné vzdálené služby jsou často nejpomalejší součástí aplikace ASP.NET Core. Čtení a zápis dat, efektivně se dobrý výkon.

Doporučení:

* **Proveďte** asynchronní volání veškerý datový přístup rozhraní API.
* **Ne** načíst více dat, než je nezbytné. Zápis dotazů, které vrátí data, která je nezbytná pro aktuální žádost HTTP.
* **Proveďte** zvažte možnost ukládání do mezipaměti často používaných dat načtených z databáze nebo vzdálené služby, pokud je to přijatelné pro data, která mají být mírně zastaralá. V závislosti na scénáři, můžete použít [MemoryCache](xref:performance/caching/memory) nebo [DistributedCache](xref:performance/caching/distributed). Další informace naleznete v tématu <xref:performance/caching/response>.
* Minimalizovat síťových přenosů. Cílem je načíst všechna data, které se mají provést v jednom volání, nikoli několik volání.
* **Proveďte** použít [bez sledování dotazy](/ef/core/querying/tracking#no-tracking-queries) v Entity Framework Core při přístupu k datům pro účely jen pro čtení. EF Core může vrátit výsledky dotazů bez sledování efektivněji.
* **Proveďte** filtr a agregačních dotazů LINQ (s `.Where`, `.Select`, nebo `.Sum` příkazy, například) tak, aby toto filtrování se provádí v databázi.
* **Proveďte** vezměte v úvahu, že EF Core řeší některé operátory dotazu na klientovi, což může vést k provádění dotazů neefektivní. Další informace najdete v tématu [problémy s výkonem vyhodnocení klienta](/ef/core/querying/client-eval#client-evaluation-performance-issues)
* **Ne** použít na kolekce, které může vést k provádění "N + 1" na dotazy projekce dotazů SQL. Další informace najdete v tématu [optimalizace korelační poddotazů](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Zobrazit [vysoký výkon EF](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) u metod, které může zlepšit výkon v aplikacích pro zajištění vysoce škálovatelné:

* [Sdružování DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explicitně kompilované dotazy](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Doporučujeme, abyste že měřit dopad z předchozích přístupů výkonné před vašeho základu kódu. Další složitosti kompilované dotazy nemusí zarovnat zlepšení výkonu.

Dotaz problémy můžete zjistit kontrolou čas strávený přístupu k data s [Application Insights](/azure/application-insights/app-insights-overview) nebo pomocí nástroje pro profilaci. Většina databází také zpřístupnit statistiky týkající se často spuštěné dotazy.

## <a name="pool-http-connections-with-httpclientfactory"></a>Připojení fondu HTTP s HttpClientFactory

I když [HttpClient](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) implementuje `IDisposable` rozhraní, to znamenalo znovu použít. Zavření `HttpClient` instance zůstat otevřeno v sockets `TIME_WAIT` stavu na krátkou dobu. V důsledku toho pokud cestu kódu, která vytváří a odstraňuje `HttpClient` se často používá objekty, aplikace může vyčerpat dostupné sokety. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) byla zavedena v ASP.NET Core 2.1 jako řešení tohoto problému. Zpracovává sdružování připojení prostřednictvím protokolu HTTP k optimalizaci výkonu a spolehlivosti.

Doporučení:

* **Ne** vytvoření a uvolnění z `HttpClient` instance přímo.
* **Proveďte** použít [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) načíst `HttpClient` instancí. Další informace najdete v tématu [HttpClientFactory použijte k implementaci odolných požadavky HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Zachovat běžné rychlé cesty kódu

Chcete-li všech kódů bude rychlé, ale cesty často volaných kódu jsou nejdůležitější pro optimalizaci:

* Middlewarových komponent v kanálu zpracování žádosti o aplikace, zejména middleware spouštět již v rané fázi v kanálu. Tyto součásti mít velký dopad na výkon.
* Kód, který se spouští pro každý požadavek nebo více než jednou každý požadavek. Například vlastní protokolování, obslužné rutiny autorizace nebo inicializace přechodné služeb.

Doporučení:

* **Ne** vlastní middlewarových komponent pomocí dlouho běžících úloh.
* **Proveďte** pomocí nástroje pro profilaci výkonu (například [diagnostické nástroje sady Visual Studio](/visualstudio/profiling/profiling-feature-tour) nebo [PerfView](https://github.com/Microsoft/perfview)) k identifikaci [horké cesty kódu](#hot).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Dokončení dlouho běžící úlohy mimo požadavků HTTP

Většina žádostí na aplikace ASP.NET Core mohou být zpracovány kontrolér nebo model stránky volání nezbytných služeb a vrátí odpověď HTTP. U některých požadavků, které se týkají dlouho běžících úloh je lepší provést asynchronní proces celou žádost odpověď.

Doporučení:

* **Ne** čekat pro dlouho běžící úlohy k dokončení jako součást běžné zpracování požadavku HTTP.
* **Proveďte** vezměte v úvahu dlouhodobé požadavky s zpracování [služby na pozadí](/aspnet/core/fundamentals/host/hosted-services) nebo mimo proces s [funkce Azure Functions](/azure/azure-functions/). Dokončení práce na více instancí procesu je obzvláště užitečné pro úlohy náročné na CPU.
* **Proveďte** pomocí možností komunikace v reálném čase, jako jsou [SignalR](xref:signalr/introduction) ke komunikaci s klienty asynchronně.

## <a name="minify-client-assets"></a>Zrušíte minifikaci prostředky klienta

Aplikace ASP.NET Core s front endů komplexní často slouží mnoho jazyka JavaScript, CSS nebo soubory obrázků. Výkon žádosti o počátečním načtení lze zvýšit pomocí:

* Sdružování, který kombinuje několik souborů do jedné.
* Minifikace, což snižuje velikost souborů.

Doporučení:

* **Proveďte** používat ASP.NET Core [integrovanou podporu](xref:client-side/bundling-and-minification) pro sdružování a minifikace prostředků klienta.
* **Proveďte** vezměte v úvahu další nástroje třetích stran, jako je [Gulp](uid:client-side/bundling-and-minification#consume-bundleconfigjson-from-gulp) nebo [Webpacku](https://webpack.js.org/) pro správu prostředků složitější klienta.

## <a name="compress-responses"></a>Kompresi odpovědí

 Na odezvu aplikace, je obvykle nezmenšit velikost této odpovědi často výrazně zvyšuje. Jedním ze způsobů ke zmenšení velikosti datové části je kompresi odpovědí vaší aplikace. Další informace najdete v tématu [kompresi odpovědí](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Použijte nejnovější vydání ASP.NET Core

Každá nová verze technologie ASP.NET zahrnuje vylepšení výkonu. Optimalizace v .NET Core a ASP.NET Core znamená, že se novější verze překonat starší verze. Například, .NET Core 2.1 přidali jsme podporu pro zkompilovaných regulárních výrazů a benefitted z [ `Span<T>` ](https://msdn.microsoft.com/magazine/mt814808.aspx). ASP.NET Core 2.2 přidána podpora HTTP/2. Pokud je výkon prioritu, zvažte možnost upgradovat na nejnovější verzi ASP.NET Core.

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a>Minimalizovat výjimky

Výjimky by měly být výjimečný. Vzhledem k jiné vzory v toku kódu pomalý vyvolávání a zachycování výjimek. Z tohoto důvodu by neměly být výjimky použít k řízení normálního toku programu.

Doporučení:

* **Ne** použití vyvolávání a zachycování výjimek jako způsob normálního toku programu, zejména v kódu horké cesty.
* **Proveďte** obsahují logiku v aplikaci, aby detekoval a řešil podmínky, které by způsobila výjimku.
* **Proveďte** operaci throw nebo catch výjimky pro neobvyklé i neočekávané situace.

Diagnostické nástroje aplikací (např. Application Insights) může pomoct identifikovat běžné výjimky v aplikaci, která může mít vliv na výkon.
