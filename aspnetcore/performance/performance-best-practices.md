---
title: ASP.NET Core Performance Best Practices
author: mjrousos
description: Tipy pro zvýšení výkonu aplikací ASP.NET Core a předcházení běžným problémům s výkonem.
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

Toto téma poskytuje pokyny a osvědčené postupy pro zlepšení výkonu aplikací postavených s ASP.NET Core.

<a name="hot"></a> V tomto dokumentu horké kódové cestě je definován jako cestu kódu, který se často nazývá a kde většinu doby provádění dojde. Cesty k výměně kódu obvykle omezit horizontální navýšení kapacity aplikace a výkonu.

## <a name="cache-aggressively"></a>Agresivní ukládání do mezipaměti

Ukládání do mezipaměti je podrobněji popsáno v několika částech tohoto dokumentu. Další informace naleznete v tématu <xref:performance/caching/response>.

## <a name="avoid-blocking-calls"></a>Předcházejte blokujícím voláním

Zpracovat velký počet požadavků současně by se měly navrhovat aplikace ASP.NET Core. Asynchronní rozhraní API umožňují malé fondu vláken zpracování tisíce souběžných požadavků tím, že není na neblokují volání. Nečekejte na synchronní časově náročné úkoly k dokončení vlákna můžete pracovat na jiná žádost.

Častý problém výkonu v aplikacích ASP.NET Core je blokování volání, které mohlo být asynchronní. Mnoho synchronních blokujících volání vede k [vyčerpání fondu vláken](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) a snížení doby odezvy.

**Nedělejte**:

* Neblokujte asynchronní běh zavoláním [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) nebo [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Nepoužívejte zámky v kritických cestách kódu. Aplikace ASP.NET Core dosahují nejlepšího výkonu, když jsou navrženy tak, aby spouštěly kód paralelně.

**Proveďte**:

* Ujistěte se, že [kritické cesty](#hot) jsou asynchronní.
* API pro přístup k datům a dlouho běžící operace volejte asynchronně.
* Ujistěte se, že jsou controllery a Razor akce stránek asynchronní. Celý zásobník volání musí být asynchronní, aby bylo možné využívat vzory [async/await](/dotnet/csharp/programming-guide/concepts/async/).

Profiler, jako například [PerfView](https://github.com/Microsoft/perfview), lze použít k vyhledání vláken často přidávaných do [fondu vláken](/windows/desktop/procthread/thread-pool). Na vlákno přidané do fondu vláken ukazuje událost `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a>Minimalizujte velké alokace objektů

<!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core --> [Systému uvolňování paměti .NET Core](/dotnet/standard/garbage-collection/) spravuje přidělování a uvolňování paměti automaticky v aplikacích ASP.NET Core. Automatické uvolňování paměti obecně znamená, že vývojáři nemusíte se starat o tom, jak a kdy je paměť uvolněna. Však čištění neodkazovaná objekty trvá čas procesoru, aby vývojáři měli minimalizovat přiděluje objekty ve [horké cesty kódu](#hot). Uvolňování paměti je zvláště nákladné na velké objekty (> 85 kB). Velké objekty jsou uloženy na [haldy pro velké objekty](/dotnet/standard/garbage-collection/large-object-heap) a vyžadují úplné (2. generace) uvolňování paměti pro vyčištění. Na rozdíl od generace 0 a 1. generace kolekce 2. generace kolekce vyžaduje spuštění aplikace chcete dočasně pozastavit. Časté přidělování a rušení přidělení velkých objektů může způsobit nekonzistentní výkonu.

Doporučení:

* **Dělejte:** Zvažte ukládání často používaných velkých objektů do mezipaměti. Zabráníte tím náročným alokacím.
* **Dělejte:** Použijte vyrovnávací paměť [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) pro ukládání velkých polí.
* **Nedělejte:** Nealokujte mnoho krátkodobých velkých objektů na [kritické cestě](#hot).

Problémy s pamětí uvedené výše můžete identifikovat kontrolou statistiky uvolňování paměti (garbage collection, GC) v [PerfView](https://github.com/Microsoft/perfview):

* Čas pozastavení kolekce uvolnění paměti.
* Jaké je procento času procesoru je trvání uvolňování paměti.
* Kolik kolekce uvolnění paměti jsou 0, 1 a 2. generace.

Další informace najdete v tématu [uvolňování paměti a výkon](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access"></a>Optimalizace přístupu k datům

Interakce s úložišti dat nebo jinými vzdálenými službami jsou často nejpomalejšími součástmi aplikace ASP.NET Core. Efektivní čtení a zápis dat jsou klíčové pro dobrý výkon.

Doporučení:

* **Proveďte** asynchronní volání veškerý datový přístup rozhraní API.
* **Ne** načíst více dat, než je nezbytné. Zápis dotazů, které vrátí data, která je nezbytná pro aktuální žádost HTTP.
* **Proveďte** zvažte možnost ukládání do mezipaměti často používaných dat načtených z databáze nebo vzdálené služby, pokud je to přijatelné pro data, která mají být mírně zastaralá. V závislosti na scénáři, můžete použít [MemoryCache](xref:performance/caching/memory) nebo [DistributedCache](xref:performance/caching/distributed). Další informace naleznete v tématu <xref:performance/caching/response>.
* Minimalizovat síťových přenosů. Cílem je načíst všechna data, které se mají provést v jednom volání, nikoli několik volání.
* **Proveďte** použít [bez sledování dotazy](/ef/core/querying/tracking#no-tracking-queries) v Entity Framework Core při přístupu k datům pro účely jen pro čtení. EF Core může vrátit výsledky dotazů bez sledování efektivněji.
* **Proveďte** filtr a agregačních dotazů LINQ (s `.Where`, `.Select`, nebo `.Sum` příkazy, například) tak, aby toto filtrování se provádí v databázi.
* **Proveďte** vezměte v úvahu, že EF Core řeší některé operátory dotazu na klientovi, což může vést k provádění dotazů neefektivní. Další informace najdete v tématu [problémy s výkonem vyhodnocení klienta](/ef/core/querying/client-eval#client-evaluation-performance-issues)
* **Ne** použít na kolekce, které může vést k provádění "N + 1" na dotazy projekce dotazů SQL. Další informace najdete v tématu [optimalizace korelační poddotazů](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Podívejte se na článek [vysoký výkon EF](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries), kde najdete metody, jak zlepšit výkon ve vysoce škálovatelných aplikacích:

* [Sdružování DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explicitně kompilované dotazy](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Doporučujeme, abyste změřili dopady výše zmíněných postupů na zvýšení výkonu ještě před tím, než změny uložíte do správy zdrojových kódů. Může se stát, že přidaná složitost kompilovaných dotazů bude větší než přínosy zlepšení výkonu.

Problémy dotazů je možné identifikovat kontrolou doby strávené přístupem k datům pomocí [Application Insights](/azure/application-insights/app-insights-overview) nebo jiného nástroje pro profilování. Většina databází také poskytuje statistiky týkající se často spouštěných dotazů.

## <a name="pool-http-connections-with-httpclientfactory"></a>Sdružování HTTP spojení pomocí HttpClientFactory

I když [HttpClient](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) implementuje `IDisposable` rozhraní, to znamenalo znovu použít. Zavření `HttpClient` instance zůstat otevřeno v sockets `TIME_WAIT` stavu na krátkou dobu. V důsledku toho pokud cestu kódu, která vytváří a odstraňuje `HttpClient` se často používá objekty, aplikace může vyčerpat dostupné sokety. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) byla zavedena v ASP.NET Core 2.1 jako řešení tohoto problému. Zpracovává sdružování připojení prostřednictvím protokolu HTTP k optimalizaci výkonu a spolehlivosti.

Doporučení:

* **Nedělejte:** Nevytvářejte a nerušte instance `HttpClient` napřímo.
* **Dělejte:** Načítejte instance `HttpClient` pomocí [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests). Další informace najdete v tématu [Použití HttpClientFactory k implementaci odolných HTTP požadavků](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Zachovat běžné rychlé cesty kódu

Rychlý chcete mít veškerý svůj kód, ale z hlediska optimalizace jsou nejdůležitější často volané části kódu:

* Komponenty middleware v průběhu zpracování požadavků aplikace - zejména ty, které se spouští v raných fázích. Tyto součásti mají velký dopad na výkon.
* Kód, který se spouští při každém požadavku nebo více než jednou pro každý požadavek. Například vlastní protokolování, obslužné rutiny autorizace nebo inicializace přechodných služeb.

Doporučení:

* **Nedělejte:** Nepoužívejte vlastní komponenty middleware obsahující dlouho běžící úlohy.
* **Dělejte:** Pomocí nástroje pro profilaci výkonu (například [diagnostické nástroje sady Visual Studio](/visualstudio/profiling/profiling-feature-tour) nebo [PerfView](https://github.com/Microsoft/perfview)) identifikujte [kritickou cestu](#hot).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Dokončení dlouho běžící úlohy mimo požadavky HTTP

Většina požadavků na aplikace ASP.NET Core může být zpracována controllerem nebo modelem stránky pomocí volání nezbytných služeb a vrácení HTTP odpovědi. U některých požadavků, které se týkají dlouho běžících úloh, je ale lepší udělat celý proces požadavek-odpověď asynchronní.

Doporučení:

* **Nedělejte:** Nečekejte na dokončení dlouho běžících úloh jako běžnou součást zpracování HTTP požadavku.
* **Dělejte:** Vezměte v úvahu zpracování dlouhodobých požadavků pomocí [služeb na pozadí](/aspnet/core/fundamentals/host/hosted-services) nebo úplně mimo proces za použití [Azure Functions](/azure/azure-functions/). Dokončení práce mimo proces je obzvláště užitečné pro úlohy náročné na CPU.
* **Dělejte:** Použijte prostředky pro komunikaci v reálném čase, jako je [SignalR](xref:signalr/introduction), k asynchronní komunikaci s klienty.

## <a name="minify-client-assets"></a>Minifikace prostředků klienta

Aplikace ASP.NET Core s komplexními front-endy často posílají klientům mnoho souborů JavaScript, CSS nebo obrázků. Výkon počátečních požadavků je možné zvýšit pomocí:

* Sdružování, které kombinuje několik souborů do jednoho.
* Minifikace, která snižuje velikost souborů.

Doporučení:

* **Proveďte** používat ASP.NET Core [integrovanou podporu](xref:client-side/bundling-and-minification) pro sdružování a minifikace prostředků klienta.
* **Proveďte** vezměte v úvahu další nástroje třetích stran, jako je [Gulp](uid:client-side/bundling-and-minification#consume-bundleconfigjson-from-gulp) nebo [Webpacku](https://webpack.js.org/) pro správu prostředků složitější klienta.

## <a name="compress-responses"></a>Komprese odpovědí

 Odezvu aplikace obvykle zlepšuje zmenšení velikosti odpovědi, a to často výrazně. Jedním ze způsobů zmenšení velikosti datové části je komprese odpovědí vaší aplikace. Další informace najdete v tématu [komprese odpovědí](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Použijte nejnovější vydání ASP.NET Core

Každá nová verze technologie ASP.NET zahrnuje vylepšení výkonu. Optimalizace v .NET Core a ASP.NET Core znamená, že novější verze překonají výkon starších verzí. Například v .NET Core 2.1 přibyla podpora zkompilovaných regulárních výrazů a využívají se výhody [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx). Verze ASP.NET Core 2.2 přidala podporu HTTP/2. Pokud je výkon prioritou, zvažte upgrade na nejnovější verzi ASP.NET Core.

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a>Omezení počtu výjimek

Výjimky by měly být výjimečný. Vzhledem k jiné vzory v toku kódu pomalý vyvolávání a zachycování výjimek. Z tohoto důvodu by neměly být výjimky použít k řízení normálního toku programu.

Doporučení:

* **Nedělejte:** Nepoužívejte vyvolávání a zachycování výjimek jako způsob řízení normálního toku programu, zejména v kritické cestě.
* **Dělejte:** Zahrťne v aplikaci logiku, která detekuje a řeší podmínky, které by jinak způsobily výjimku.
* **Dělejte:** Používejte operaci throw a catch výjimky pro neobvyklé a neočekávané situace.

Diagnostické nástroje aplikací (např. Application Insights) může pomoct identifikovat běžné výjimky v aplikaci, která může mít vliv na výkon.
