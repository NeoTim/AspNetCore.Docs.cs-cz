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

<a name="hot"></a> V tomto dokumentu je pojem **kritická cesta** definován jako kód, který je volán velmi často a v kterém se tráví nejvíce výpočetního času. Kritické cesty obvykle omezují horizontální navýšení kapacity a výkonu aplikace.

## <a name="cache-aggressively"></a>Agresivní ukládání do mezipaměti

Ukládání do mezipaměti je podrobněji popsáno v několika částech tohoto dokumentu. Další informace naleznete v tématu <xref:performance/caching/response>.

## <a name="avoid-blocking-calls"></a>Předcházejte blokujícím voláním

Aplikace ASP.NET Core by měly být navrhovány tak, aby dokázaly zpracovat velký počet požadavků současně. Asynchronní rozhraní API umožňují malému fondu vláken zpracovat tisíce souběžných požadavků tím, že neblokují volání, takže namísto čekání na dokončení dlouhotrvající synchronní úlohy může vlákno zpracovávat další požadavek.

Častý problém výkonu v aplikacích ASP.NET Core je blokování volání, které mohlo být asynchronní. Mnoho synchronních blokujících volání vede k [vyčerpání fondu vláken](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) a snížení doby odezvy.

**Nedělejte**:

* Neblokujte asynchronní běh zavoláním [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) nebo [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Nepoužívejte zámky v kritických cestách kódu. Aplikace ASP.NET Core dosahují nejlepšího výkonu, když jsou navrženy tak, aby spouštěly kód paralelně.

**Dělejte**:

* Ujistěte se, že [kritické cesty](#hot) jsou asynchronní.
* API pro přístup k datům a dlouho běžící operace volejte asynchronně.
* Ujistěte se, že jsou controllery a Razor akce stránek asynchronní. Celý zásobník volání musí být asynchronní, aby bylo možné využívat vzory [async/await](/dotnet/csharp/programming-guide/concepts/async/).

Profiler, jako například [PerfView](https://github.com/Microsoft/perfview), lze použít k vyhledání vláken často přidávaných do [fondu vláken](/windows/desktop/procthread/thread-pool). Na vlákno přidané do fondu vláken ukazuje událost `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a>Minimalizujte velké alokace objektů

<!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core --> 
V aplikacích ASP.NET Core spravuje přidělování a uvolňování paměti automaticky [garbage collector .NET Core](/dotnet/standard/garbage-collection/). Automatické uvolňování paměti obecně znamená, že vývojáři se nemusí starat o to, jak a kdy je paměť uvolněna. Čištění neodkazovaných objektů však zabírá čas procesoru, takže vývojáři by měli minimalizovat alokování objektů v [kritické cestě](#hot). Uvolňování paměti je obzvlášť nákladné u velkých objektů (> 85 kB). Velké objekty jsou uloženy na [haldě pro velké objekty](/dotnet/standard/garbage-collection/large-object-heap) a vyžadují úplné (2. generace) uvolňování paměti pro vyčištění. Na rozdíl od generace 0 a 1. uvolnění paměti 2. generace vyžaduje dočasné pozastavení aplikace. Časté vytváření a rušení velkých objektů může způsobit nekonzistentní výkon.

Doporučení:

* **Dělejte:** Zvažte ukládání často používaných velkých objektů do mezipaměti. Zabráníte tím náročným alokacím.
* **Dělejte:** Použijte vyrovnávací paměť [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) pro ukládání velkých polí.
* **Nedělejte:** Nealokujte mnoho krátkodobých velkých objektů na [kritické cestě](#hot).

Problémy s pamětí uvedené výše můžete identifikovat kontrolou statistiky uvolňování paměti (garbage collection, GC) v [PerfView](https://github.com/Microsoft/perfview):

* Dobu pozastavení uvolnění paměti.
* Jaké procento času procesoru zabírá uvolňování paměti.
* Kolik uvolnění paměti jsou 0, 1 a 2. generace.

Další informace najdete v tématu [uvolňování paměti a výkon](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access"></a>Optimalizace přístupu k datům

Interakce s úložišti dat nebo jinými vzdálenými službami jsou často nejpomalejšími součástmi aplikace ASP.NET Core. Efektivní čtení a zápis dat jsou klíčové pro dobrý výkon.

Doporučení:

* **Dělejte:** Všechna rozhraní API pro přístup k datům volejte asynchronně.
* **Nedělejte:** Nenačítejte více dat, než je nezbytné. Pište dotazy tak, abyste dostali pouze ta data, která potřebujete pro vyřízení aktuálního HTTP požadavku.
* **Dělejte:** Zvažte ukládání často používaných dat z databáze nebo vzdálené služby do mezipaměti, pokud je přijatelné, že data mohou být mírně zastaralá. V závislosti na vašem scénáři můžete použít [MemoryCache](xref:performance/caching/memory) nebo [DistributedCache](xref:performance/caching/distributed). Další informace naleznete v tématu <xref:performance/caching/response>.
* **Dělejte:** Minimalizujte síťové přenosy. Cílem je načíst všechna data, která jsou potřeba, v jednom volání, nikoli v několika.
* **Dělejte:** Použijte [dotazy bez sledování](/ef/core/querying/tracking#no-tracking-queries) v Entity Framework Core při přístupu k datům pouze pro čtení. EF Core může vrátit výsledky dotazů bez sledování efektivněji.
* **Dělejte:** Na filtrační a agregační dotazy použijte LINQ (například příkazy `.Where`, `.Select` nebo `.Sum`) tak, aby se toto filtrování provedlo ještě v databázi.
* **Dělejte:** Vezměte v úvahu, že EF Core řeší některé operátory dotazu na klientovi, což může vést k neefektivnímu provádění dotazů. Další informace najdete v tématu [problémy s výkonem při vyhodnocení na klientovi](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Nedělejte:** Nepoužívejte dotazy projekce na kolekce, pokud to může vést k provádění "N + 1" SQL dotazů. Další informace najdete v tématu [optimalizace korelovaných poddotazů](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Podívejte se na článek [vysoký výkon EF](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries), kde najdete metody, jak zlepšit výkon ve vysoce škálovatelných aplikacích:

* [Sdružování DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explicitně kompilované dotazy](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Doporučujeme, abyste změřili dopady výše zmíněných postupů na zvýšení výkonu ještě před tím, než změny uložíte do správy zdrojových kódů. Může se stát, že přidaná složitost kompilovaných dotazů bude větší než přínosy zlepšení výkonu.

Problémy dotazů je možné identifikovat kontrolou doby strávené přístupem k datům pomocí [Application Insights](/azure/application-insights/app-insights-overview) nebo jiného nástroje pro profilování. Většina databází také poskytuje statistiky týkající se často spouštěných dotazů.

## <a name="pool-http-connections-with-httpclientfactory"></a>Sdružování HTTP spojení pomocí HttpClientFactory

Přestože třída [HttpClient](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) implementuje rozhraní `IDisposable`, je určena ke znovupoužívaní. Zavřené instance třídy `HttpClient` zanechávají na krátkou dobu otevřené sockety ve stavu `TIME_WAIT`. V důsledku toho se může stát, že pokud kód často vytváří a odstraňuje objekty `HttpClient`, aplikace může vyčerpat dostupné sokety. Jako řešení tohoto problému byla v ASP.NET Core 2.1 zavedena třída [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests), která se stará o sdružování HTTP spojení za účelem optimalizace výkonu a spolehlivosti.

Doporučení:

* **Nedělejte:** Nevytvářejte a nerušte instance `HttpClient` napřímo.
* **Dělejte:** Načítejte instance `HttpClient` pomocí [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests). Další informace najdete v tématu [Použití HttpClientFactory k implementaci odolných HTTP požadavků](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Udržujte často volaný kód rychlý

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

* **Dělejte:** Použijte [integrovanou podporu](xref:client-side/bundling-and-minification) ASP.NET Core pro sdružování a minifikaci prostředků klienta.
* **Dělejte:** Vezměte v úvahu další nástroje třetích stran, jako je [Gulp](uid:client-side/bundling-and-minification#consume-bundleconfigjson-from-gulp) nebo [Webpack](https://webpack.js.org/) pro komplexnější správu prostředků klienta.

## <a name="compress-responses"></a>Komprese odpovědí

Odezvu aplikace obvykle zlepšuje zmenšení velikosti odpovědi, a to často výrazně. Jedním ze způsobů zmenšení velikosti datové části je komprese odpovědí vaší aplikace. Další informace najdete v tématu [komprese odpovědí](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Použijte nejnovější vydání ASP.NET Core

Každá nová verze technologie ASP.NET zahrnuje vylepšení výkonu. Optimalizace v .NET Core a ASP.NET Core znamená, že novější verze překonají výkon starších verzí. Například v .NET Core 2.1 přibyla podpora zkompilovaných regulárních výrazů a využívají se výhody [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx). Verze ASP.NET Core 2.2 přidala podporu HTTP/2. Pokud je výkon prioritou, zvažte upgrade na nejnovější verzi ASP.NET Core.

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a>Omezení počtu výjimek

Výjimky by měly být vzácné. V porovnání s ostatními typy kódu je vyvolávání a zachycování výjimek pomalé, proto by neměly být používány k řízení normálního toku programu.

Doporučení:

* **Nedělejte:** Nepoužívejte vyvolávání a zachycování výjimek jako způsob řízení normálního toku programu, zejména v kritické cestě.
* **Dělejte:** Zahrťne v aplikaci logiku, která detekuje a řeší podmínky, které by jinak způsobily výjimku.
* **Dělejte:** Používejte operaci throw a catch výjimky pro neobvyklé a neočekávané situace.

Diagnostické nástroje aplikací (např. Application Insights) mohou v aplikaci pomoct s identifikací běžných výjimek, které mají vliv na výkon.
