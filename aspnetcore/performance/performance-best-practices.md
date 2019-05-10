---
title: ASP.NET Core Performance Best Practices
author: mjrousos
description: Tipy pro zvýšení výkonu aplikací ASP.NET Core a předcházení běžným problémům s výkonem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/13/2019
uid: performance/performance-best-practices
ms.openlocfilehash: 28dc7fb40c1b60f643108dcb44593a08942a1650
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087489"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core Performance Best Practices

Podle [Mike Rousos](https://github.com/mjrousos)

Toto téma poskytuje pokyny a osvědčené postupy pro zlepšení výkonu aplikací postavených s ASP.NET Core.

<a name="hot"></a>

V tomto dokumentu *horké kódové cestě* je definován jako cestu kódu, který se často nazývá a kde většinu doby provádění dojde. Kritické cesty obvykle omezují horizontální navýšení kapacity a výkonu aplikace.

## <a name="cache-aggressively"></a>Agresivní ukládání do mezipaměti

Ukládání do mezipaměti je podrobněji popsáno v několika částech tohoto dokumentu. Další informace naleznete v tématu <xref:performance/caching/response>.

## <a name="avoid-blocking-calls"></a>Předcházejte blokujícím voláním

Aplikace ASP.NET Core by měly být navrhovány tak, aby dokázaly zpracovat velký počet požadavků současně. Asynchronní rozhraní API umožňují malému fondu vláken zpracovat tisíce souběžných požadavků tím, že neblokují volání, takže namísto čekání na dokončení dlouhotrvající synchronní úlohy může vlákno zpracovávat další požadavek.

Častý problém výkonu v aplikacích ASP.NET Core je blokování volání, které mohlo být asynchronní. Mnoho synchronní volání blokování vést k [vyčerpání fondu vláken](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) a snížení doby odezvy.

**Nedělejte**:

* Neblokujte asynchronní běh zavoláním [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) nebo [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Nepoužívejte zámky v kritických cestách kódu. Aplikace ASP.NET Core dosahují nejlepšího výkonu, když jsou navrženy tak, aby spouštěly kód paralelně.

**Dělejte**:

* Ujistěte se, že [kritické cesty](#hot) jsou asynchronní.
* API pro přístup k datům a dlouho běžící operace volejte asynchronně.
* Ujistěte se, že jsou controllery a Razor akce stránek asynchronní. Celý zásobník volání je asynchronní, aby bylo možné využívat [async/await](/dotnet/csharp/programming-guide/concepts/async/) vzory.

A profileru jako například [PerfView](https://github.com/Microsoft/perfview), lze použít k vyhledání vlákna často přidá do [vlákna fondu](/windows/desktop/procthread/thread-pools). `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` Událost označuje vlákno, přidat do fondu vláken. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a>Minimalizujte velké alokace objektů

<!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core -->
[Systému uvolňování paměti .NET Core](/dotnet/standard/garbage-collection/) spravuje přidělování a uvolňování paměti automaticky v aplikacích ASP.NET Core. Automatické uvolňování paměti obecně znamená, že vývojáři nemusíte se starat o tom, jak a kdy je paměť uvolněna. Však čištění neodkazovaná objekty trvá čas procesoru, aby vývojáři měli minimalizovat přiděluje objekty ve [horké cesty kódu](#hot). Uvolňování paměti je zvláště nákladné na velké objekty (> 85 kB). Velké objekty jsou uloženy na [haldy pro velké objekty](/dotnet/standard/garbage-collection/large-object-heap) a vyžadují úplné (2. generace) uvolňování paměti pro vyčištění. Na rozdíl od generace 0 a 1. generace kolekce 2. generace kolekce vyžaduje pozastavení provádění aplikace. Časté přidělování a rušení přidělení velkých objektů může způsobit nekonzistentní výkonu.

Doporučení:

* **Dělejte:** Zvažte ukládání často používaných velkých objektů do mezipaměti. Zabráníte tím náročným alokacím.
* **Dělejte:** Použijte vyrovnávací paměť [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) pro ukládání velkých polí.
* **Nedělejte:** Nealokujte mnoho krátkodobých velkých objektů na [kritické cestě](#hot).

Problémy s pamětí, jako je například předchozím příkladu, můžete zjistit kontrolou statistiky uvolňování paměti kolekce (GC) v [PerfView](https://github.com/Microsoft/perfview) a zkontrolujete:

* Dobu pozastavení uvolnění paměti.
* Jaké procento času procesoru zabírá uvolňování paměti.
* Kolik uvolnění paměti jsou 0, 1 a 2. generace.

Další informace najdete v tématu [uvolňování paměti a výkon](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access"></a>Optimalizace přístupu k datům

Interakce s úložištěm dat a jiné vzdálené služby jsou často nejpomalejší součástí aplikace ASP.NET Core. Efektivní čtení a zápis dat jsou klíčové pro dobrý výkon.

Doporučení:

* **Dělejte:** Všechna rozhraní API pro přístup k datům volejte asynchronně.
* **Nedělejte:** Nenačítejte více dat, než je nezbytné. Zápis dotazů, které vrátí data, která je nezbytná pro aktuální žádost HTTP.
* **Proveďte** zvažte možnost ukládání do mezipaměti často používaných dat načtených z databáze nebo vzdálené služby, pokud je přijatelné mírně zastaralá data. V závislosti na scénáři, použití [MemoryCache](xref:performance/caching/memory) nebo [DistributedCache](xref:performance/caching/distributed). Další informace naleznete v tématu <xref:performance/caching/response>.
* **Proveďte** minimalizovat síťových přenosů. Cílem je k načtení požadovaných dat v jednom volání spíše než několik volání.
* **Dělejte:** Použijte [dotazy bez sledování](/ef/core/querying/tracking#no-tracking-queries) v Entity Framework Core při přístupu k datům pouze pro čtení. EF Core může vrátit výsledky dotazů bez sledování efektivněji.
* **Proveďte** filtr a agregačních dotazů LINQ (s `.Where`, `.Select`, nebo `.Sum` příkazy, například) tak, aby filtrování se provádí pomocí databáze.
* **Dělejte:** Vezměte v úvahu, že EF Core řeší některé operátory dotazu na klientovi, což může vést k neefektivnímu provádění dotazů. Další informace najdete v tématu [problémy s výkonem vyhodnocení klienta](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Nedělejte:** Nepoužívejte dotazy projekce na kolekce, pokud to může vést k provádění "N + 1" SQL dotazů. Další informace najdete v tématu [optimalizace korelovaných poddotazů](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Podívejte se na článek [vysoký výkon EF](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries), kde najdete metody, jak zlepšit výkon ve vysoce škálovatelných aplikacích:

* [Sdružování DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explicitně kompilované dotazy](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Doporučujeme, abyste měření dopadu předchozích přístupů vysokovýkonného před potvrzením základu kódu. Může se stát, že přidaná složitost kompilovaných dotazů bude větší než přínosy zlepšení výkonu.

Dotaz problémy můžete zjistit kontrolou čas strávený přístupu k data s [Application Insights](/azure/application-insights/app-insights-overview) nebo pomocí nástroje pro profilaci. Většina databází také poskytuje statistiky týkající se často spouštěných dotazů.

## <a name="pool-http-connections-with-httpclientfactory"></a>Sdružování HTTP spojení pomocí HttpClientFactory

I když [HttpClient](/dotnet/api/system.net.http.httpclient) implementuje `IDisposable` rozhraní, je určená pro opakované použití. Zavřené instance třídy `HttpClient` zanechávají na krátkou dobu otevřené sockety ve stavu `TIME_WAIT`. Pokud cestu kódu, která vytváří a odstraňuje `HttpClient` se často používá objekty, aplikace může vyčerpat dostupné sokety. Jako řešení tohoto problému byla v ASP.NET Core 2.1 zavedena třída [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests), která se stará o sdružování HTTP spojení za účelem optimalizace výkonu a spolehlivosti.

Doporučení:

* **Nedělejte:** Nevytvářejte a nerušte instance `HttpClient` napřímo.
* **Dělejte:** Načítejte instance `HttpClient` pomocí [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests). Další informace najdete v tématu [Použití HttpClientFactory k implementaci odolných HTTP požadavků](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Udržujte často volaný kód rychlý

Chcete všechny kódu pro rychlý, často volat cesty kódu jsou nejdůležitější pro optimalizaci:

* Komponenty middleware v průběhu zpracování požadavků aplikace - zejména ty, které se spouští v raných fázích. Tyto součásti mají velký dopad na výkon.
* Kód, který se spouští pro každý požadavek nebo více než jednou každý požadavek. Například vlastní protokolování, obslužné rutiny autorizace nebo inicializace přechodných služeb.

Doporučení:

* **Nedělejte:** Nepoužívejte vlastní komponenty middleware obsahující dlouho běžící úlohy.
* **Proveďte** pomocí nástroje pro profilaci, jako například výkonu [diagnostické nástroje sady Visual Studio](/visualstudio/profiling/profiling-feature-tour) nebo [PerfView](https://github.com/Microsoft/perfview)), k identifikaci [horké cesty kódu](#hot).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Dokončení dlouho běžící úlohy mimo požadavky HTTP

Většina požadavků na aplikace ASP.NET Core může být zpracována controllerem nebo modelem stránky pomocí volání nezbytných služeb a vrácení HTTP odpovědi. U některých požadavků, které se týkají dlouho běžících úloh, je ale lepší udělat celý proces požadavek-odpověď asynchronní.

Doporučení:

* **Nedělejte:** Nečekejte na dokončení dlouho běžících úloh jako běžnou součást zpracování HTTP požadavku.
* **Dělejte:** Vezměte v úvahu zpracování dlouhodobých požadavků pomocí [služeb na pozadí](xref:fundamentals/host/hosted-services) nebo úplně mimo proces za použití [Azure Functions](/azure/azure-functions/). Dokončení práce mimo proces je obzvláště užitečné pro úlohy náročné na CPU.
* **Proveďte** používat možnosti komunikaci v reálném čase, jako například [SignalR](xref:signalr/introduction), ke komunikaci s klienty asynchronně.

## <a name="minify-client-assets"></a>Minifikace prostředků klienta

Aplikace ASP.NET Core s komplexními front-endy často posílají klientům mnoho souborů JavaScript, CSS nebo obrázků. Výkon počátečních požadavků je možné zvýšit pomocí:

* Sdružování, které kombinuje několik souborů do jednoho.
* Minifikace, což snižuje velikost souborů tak, že odeberete prázdné znaky a komentáře.

Doporučení:

* **Dělejte:** Použijte [integrovanou podporu](xref:client-side/bundling-and-minification) ASP.NET Core pro sdružování a minifikaci prostředků klienta.
* **Proveďte** vezměte v úvahu další nástroje třetích stran, jako například [Gulp](xref:client-side/using-gulp) nebo [Webpacku](https://webpack.js.org/) pro klienta komplexní správu prostředků.

## <a name="compress-responses"></a>Komprese odpovědí

 Odezvu aplikace obvykle zlepšuje zmenšení velikosti odpovědi, a to často výrazně. Jedním ze způsobů zmenšení velikosti datové části je komprese odpovědí vaší aplikace. Další informace najdete v tématu [komprese odpovědí](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Použijte nejnovější vydání ASP.NET Core

Každá nová verze ASP.NET Core zahrnuje vylepšení výkonu. Optimalizace v .NET Core a ASP.NET Core znamená, že novější verze obecně překonat starší verze. Například v .NET Core 2.1 přibyla podpora zkompilovaných regulárních výrazů a využívají se výhody [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx). Verze ASP.NET Core 2.2 přidala podporu HTTP/2. Pokud je výkon prioritu, zvažte možnost upgradovat na aktuální verzi ASP.NET Core.

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a>Omezení počtu výjimek

Výjimky by měly být vzácné. V porovnání s ostatními typy kódu je vyvolávání a zachycování výjimek pomalé, Z tohoto důvodu by neměly být výjimky použít k řízení normálního toku programu.

Doporučení:

* **Ne** použití vyvolávání a zachycování výjimek jako způsob normálního toku programu, zejména v [horké cesty kódu](#hot).
* **Dělejte:** Zahrťne v aplikaci logiku, která detekuje a řeší podmínky, které by jinak způsobily výjimku.
* **Dělejte:** Používejte operaci throw a catch výjimky pro neobvyklé a neočekávané situace.

Diagnostické nástroje aplikace, jako jsou Application Insights může pomoct identifikovat běžné výjimky v aplikaci, která může mít vliv na výkon.
