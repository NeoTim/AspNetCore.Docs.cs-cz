---
title: ASP.NET základní doporučené postupy pro výkon
author: mjrousos
description: Tipy pro zvýšení výkonu v aplikacích ASP.NET Core a zamezení běžným problémům s výkonem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977181"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET základní doporučené postupy pro výkon

Podle [Mike Rousos](https://github.com/mjrousos)

Tento článek obsahuje pokyny pro doporučené postupy výkonu s ASP.NET Core.

## <a name="cache-aggressively"></a>Mezipaměť agresivně

Ukládání do mezipaměti je diskutováno v několika částech tohoto dokumentu. Další informace naleznete v tématu <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Porozumět horkým cestám kódu

V tomto dokumentu je *horká cesta kódu* definována jako cesta kódu, která je často volána a kde dochází k velké části doby provádění. Cesty horkého kódu obvykle omezují horizontální navýšení kapacity aplikací a výkon a jsou popsány v několika částech tohoto dokumentu.

## <a name="avoid-blocking-calls"></a>Vyhněte se blokování hovorů

ASP.NET základní aplikace by měly být navrženy tak, aby zpracovávaly mnoho požadavků současně. Asynchronní rozhraní API umožňují malý fond podprocesů pro zpracování tisíců souběžných požadavků tím, že nečeká na blokování volání. Spíše než čekat na dlouhotrvající synchronní úlohu k dokončení, vlákno může pracovat na jiný požadavek.

Běžnýproblém výkonu v aplikacích ASP.NET Core blokuje volání, která mohou být asynchronní. Mnoho synchronní blokování volání vést k [fondu vláken hladovění](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) a zhoršené doby odezvy.

**Ne :**

* Blokovat asynchronní spuštění voláním [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) nebo [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Získejte zámky v běžných cestách kódu. ASP.NET Základní aplikace jsou nejvýkonnější při navrhování pro paralelní spouštění kódu.
* Volejte [Task.Run](/dotnet/api/system.threading.tasks.task.run) a okamžitě na něj čekat. ASP.NET Core již spouští kód aplikace v normálních vláknech fondu vláken, takže volání Task.Run má za následek pouze další zbytečné plánování fondu vláken. I v případě, že naplánovaný kód by blokovat vlákno, Task.Run nezabrání tomu.

**Do**:

* Vytvořte [horké cesty kódu](#understand-hot-code-paths) asynchronní.
* Volání přístupu k datům, vstupně-místa a dlouhotrvající operace rozhraní API asynchronně, pokud je k dispozici asynchronní rozhraní API. **Nepoužívejte** [Task.Run,](/dotnet/api/system.threading.tasks.task.run) aby synchronní rozhraní API asynchronní.
* Aby akce řadiče/Holicí strojek asynchronně. Celý zásobník volání je asynchronní, aby bylo možné využívat vzory [async/await.](/dotnet/csharp/programming-guide/concepts/async/)

Profiler, například [PerfView](https://github.com/Microsoft/perfview), lze použít k vyhledání podprocesů často přidaných do [fondu vláken](/windows/desktop/procthread/thread-pools). Událost `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` označuje vlákno přidané do fondu vláken. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Minimalizace přidělení velkých objektů

[Systém uvolňování paměti .NET Core](/dotnet/standard/garbage-collection/) automaticky spravuje přidělení a uvolnění paměti v aplikacích ASP.NET Core. Automatické uvolňování paměti obecně znamená, že vývojáři nemusí starat o tom, jak nebo kdy je uvolněna paměť. Vyčištění neodkazovaných objektů však trvá čas procesoru, takže vývojáři by měli minimalizovat přidělování objektů v [cestách horkékód](#understand-hot-code-paths). Uvolňování paměti je obzvláště nákladné na velké objekty (> 85 k B). Velké objekty jsou uloženy na [haldě velkého objektu](/dotnet/standard/garbage-collection/large-object-heap) a vyžadují úplné (generace 2) uvolnění paměti k vyčištění. Na rozdíl od generace 0 a generace 1 kolekce generace 2 kolekce vyžaduje dočasné pozastavení spuštění aplikace. Časté přidělování a delokace velkých objektů může způsobit nekonzistentní výkon.

Doporučení:

* **Zvažte** ukládání velkých objektů do mezipaměti, které se často používají. Ukládání velkých objektů do mezipaměti zabraňuje nákladné přidělení.
* **Do** fondu vyrovnávacích pamětí pomocí [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) pro ukládání velkých polí.
* **Nepřidělujte** mnoho, krátkodobé velké objekty na [cesty horké kód](#understand-hot-code-paths).

Problémy s pamětí, jako je například předchozí, mohou být diagnostikovány kontrolou statistik uvolňování paměti (GC) v [PerfView](https://github.com/Microsoft/perfview) a zkoumáním:

* Uvolnění paměti pozastavit čas.
* Jaké procento času procesoru je vynaloženo v uvolňování paměti.
* Kolik uvolnění paměti jsou generace 0, 1 a 2.

Další informace naleznete v [tématu Uvolňování paměti a výkon](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Optimalizace přístupu k datům a vstupně-to

Interakce s úložištěm dat a dalšími vzdálenými službami jsou často nejpomalejší částí aplikace ASP.NET Core. Efektivní čtení a zápis dat je rozhodující pro dobrý výkon.

Doporučení:

* **Volat** všechna data přístupu api asynchronně.
* **Nenačítejte** více dat, než je nutné. Napište dotazy vrátit pouze data, která je nezbytná pro aktuální požadavek HTTP.
* **Zvažte** ukládání často přistupovaných dat z databáze nebo vzdálené služby do mezipaměti, pokud jsou přijatelná mírně zastaralá data. V závislosti na scénáři použijte [MemoryCache](xref:performance/caching/memory) nebo [DistributedCache](xref:performance/caching/distributed). Další informace naleznete v tématu <xref:performance/caching/response>.
* **Minimalizujte** síťové zpáteční lety. Cílem je načíst požadovaná data v jednom volání, nikoli několik volání.
* **Při** [přístupu k datům](/ef/core/querying/tracking#no-tracking-queries) pro účely jen pro čtení používejte dotazy bez sledování v jádru entity frameworku. EF Core můžete vrátit výsledky bez sledování dotazů efektivněji.
* **Filtrujte** a agregujte dotazy `.Where` `.Select`LINQ `.Sum` (například s , nebo příkazy) tak, aby filtrování prováděla databáze.
* **Myslíte** si, že EF Core řeší některé operátory dotazu na straně klienta, což může vést k neefektivní spuštění dotazu. Další informace naleznete v [tématu Problémy s výkonem hodnocení klienta](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Nepoužívejte** projekční dotazy na kolekce, což může mít za následek provádění dotazů SQL "N + 1". Další informace naleznete v [tématu Optimalizace korelovaných poddotazů](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Přístupy, které mohou zlepšit výkon v aplikacích ve velkém měřítku, najdete v tématu [EF High Performance:](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

* [Sdružování kontextu DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explicitně zkompilované dotazy](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Doporučujeme měřit dopad předchozích vysoce výkonných přístupů před potvrzením základu kódu. Další složitost zkompilovaných dotazů nemusí odůvodnit zlepšení výkonu.

Problémy s dotazy lze zjistit kontrolou času stráveného přístupem k datům pomocí [application insights](/azure/application-insights/app-insights-overview) nebo pomocí nástrojů profilování. Většina databází také zpřístupnit statistické údaje týkající se často spouštěné dotazy.

## <a name="pool-http-connections-with-httpclientfactory"></a>Připojení HTTP fondu s aplikací HttpClientFactory

Přestože [HttpClient](/dotnet/api/system.net.http.httpclient) `IDisposable` implementuje rozhraní, je určen pro opakované použití. Uzavřené `HttpClient` instance ponechávají sokety otevřené ve `TIME_WAIT` stavu po krátkou dobu. Pokud cesta kódu, která vytváří `HttpClient` a zprostředkovává objekty se často používá, aplikace může vyčerpat dostupné sokety. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) byl představen v ASP.NET Core 2.1 jako řešení tohoto problému. Zpracovává sdružování připojení HTTP pro optimalizaci výkonu a spolehlivosti.

Doporučení:

* **Nevytvářejte** a `HttpClient` nevyřazujte instance přímo.
* **Použijte** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) k `HttpClient` načtení instancí. Další informace naleznete [v tématu HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Rychlé zachování běžných cest kódu

Chcete, aby byl celý kód rychlý. Často nazývané cesty kódu jsou nejkritičtější pro optimalizaci. Mezi ně patří:

* Součásti Middleware v kanálu zpracování požadavků aplikace, zejména middleware spustit v rané fázi kanálu. Tyto součásti mají velký vliv na výkon.
* Kód, který je proveden pro každý požadavek nebo vícekrát na požadavek. Například vlastní protokolování, obslužné rutiny autorizace nebo inicializace přechodných služeb.

Doporučení:

* **Nepoužívejte** vlastní komponenty middlewaru s dlouhotrvajícími úlohami.
* **K** identifikaci [cest horkého kódu](#understand-hot-code-paths)používejte nástroje pro profilování výkonu, například [nástroje Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) nebo [PerfView](https://github.com/Microsoft/perfview)).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Dokončení dlouhotrvajících úloh mimo požadavky HTTP

Většinu požadavků na aplikaci ASP.NET Core může zpracovat řadič nebo model stránky, který volá nezbytné služby a vrací odpověď HTTP. U některých požadavků, které zahrnují dlouhotrvající úlohy, je lepší, aby celý proces požadavku a odpovědi asynchronní.

Doporučení:

* **Nečekejte** na dlouhotrvající úlohy k dokončení jako součást běžnézpracování požadavku HTTP.
* **Zvažte** zpracování dlouhotrvající požadavky se [službami na pozadí](xref:fundamentals/host/hosted-services) nebo mimo proces s Azure [function](/azure/azure-functions/). Dokončení práce mimo proces je obzvláště výhodné pro úlohy náročné na procesor.
* **Ke** komunikaci s klienty asynchronně používejte možnosti komunikace v reálném čase, například [SignalR](xref:signalr/introduction).

## <a name="minify-client-assets"></a>Minify klientská aktiva

ASP.NET Základní aplikace se složitými front-endy často obsluhují mnoho souborů JavaScriptu, CSS nebo obrázků. Výkon počátečních požadavků na zatížení lze zlepšit pomocí:

* Svazování, které kombinuje více souborů do jednoho.
* Minifying, který zmenšuje velikost souborů odebráním mezery a komentáře.

Doporučení:

* **Používejte** integrovanou [podporu](xref:client-side/bundling-and-minification) ASP.NET Core pro svazování a minifying klientských aktiv.
* **Zvažte** další nástroje třetích stran, jako je [například Webpack](https://webpack.js.org/), pro komplexní správu aktiv klienta.

## <a name="compress-responses"></a>Komprese odpovědí

 Zmenšení velikosti odpovědi obvykle zvyšuje odezvu aplikace, často dramaticky. Jedním ze způsobů, jak snížit velikost datové části, je komprimovat odpovědi aplikace. Další informace naleznete v [tématu Komprese odezvy](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Použití nejnovější verze ASP.NET Core

Každá nová verze ASP.NET Core obsahuje vylepšení výkonu. Optimalizace v .NET Core a ASP.NET Core znamenají, že novější verze obecně předčí starší verze. Například .NET Core 2.1 přidal podporu pro kompilované regulární výrazy a těžil z [Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx). ASP.NET Core 2.2 přidal podporu pro HTTP/2. [ASP.NET Core 3.0 přidává mnoho vylepšení,](xref:aspnetcore-3.0) která snižují využití paměti a zlepšují propustnost. Pokud je prioritou výkon, zvažte upgrade na aktuální verzi ASP.NET core.

## <a name="minimize-exceptions"></a>Minimalizace výjimek

Výjimky by měly být vzácné. Vyvolání a zachycení výjimky je pomalé vzhledem k jiné vzory toku kódu. Z tohoto důvodu by výjimky neměly být použity k řízení normálního toku programu.

Doporučení:

* **Nepoužívejte** vyvolání nebo zachycení výjimky jako prostředek normální tok programu, zejména v [horké cesty kódu](#understand-hot-code-paths).
* **Do** zahrnout logiku v aplikaci ke zjištění a zpracování podmínek, které by způsobily výjimku.
* **Do** vyvolat nebo zachytit výjimky pro neobvyklé nebo neočekávané podmínky.

Diagnostické nástroje aplikací, jako je Application Insights, můžou pomoct identifikovat běžné výjimky v aplikaci, které můžou ovlivnit výkon.

## <a name="performance-and-reliability"></a>Výkon a spolehlivost

Následující části obsahují tipy pro výkon a známé problémy se spolehlivostí a řešení.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Vyhněte se synchronní čtení nebo zápisu na těle HttpRequest/HttpResponse

Všechny vstupně-v. v ASP.NET core je asynchronní. Servery `Stream` implementují rozhraní, které má synchronní i asynchronní přetížení. Asynchronní by měly být upřednostňovány, aby se zabránilo blokování vláken fondu vláken. Blokování podprocesů může vést k hladovění fondu vláken.

**Nedělají toto:** Následující příklad používá <xref:System.IO.StreamReader.ReadToEnd*>. Blokuje aktuální vlákno čekat na výsledek. Toto je příklad [synchronizace přes asynchronní](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

V předchozím kódu `Get` synchronně přečte celé tělo požadavku HTTP do paměti. Pokud se klient pomalu nahrává, aplikace provádí synchronizaci přes asynchronní. Aplikace se synchronizuje přes asynchronní, protože Kestrel **nepodporuje** synchronní čtení.

**Proveďte toto:** Následující příklad <xref:System.IO.StreamReader.ReadToEndAsync*> používá a neblokuje vlákno při čtení.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Předchozí kód asynchronně přečte celé tělo požadavku HTTP do paměti.

> [!WARNING]
> Pokud je požadavek velký, čtení celého těla požadavku HTTP do paměti může vést k nedostatku paměti (OOM). OOM může mít za následek odmítnutí služby.  Další informace naleznete v [tématu Vyhněte se čtení velkých těl požadavků nebo těl odpovědí do paměti](#arlb) v tomto dokumentu.

**Proveďte toto:** Následující příklad je plně asynchronní pomocí těla požadavku bez vyrovnávací paměti:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Předchozí kód asynchronně deserializuje tělo požadavku do c# objektu.

## <a name="prefer-readformasync-over-requestform"></a>Preferovat ReadFormAsync před Request.Form

Místo `HttpContext.Request.ReadFormAsync` použít `HttpContext.Request.Form`.
`HttpContext.Request.Form`lze bezpečně číst pouze za následujících podmínek:

* Formulář byl přečten voláním `ReadFormAsync`na a
* Hodnota formuláře uložená v mezipaměti se čte pomocí`HttpContext.Request.Form`

**Nedělají toto:** Následující příklad `HttpContext.Request.Form`používá .  `HttpContext.Request.Form`používá [synchronizaci přes asynchronizaci](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) a může vést k hladovění fondu vláken.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Proveďte toto:** Následující příklad `HttpContext.Request.ReadFormAsync` používá ke čtení těla formuláře asynchronně.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Vyhněte se čtení velkých těl požadavků nebo těl odpovědí do paměti

V rozhraní .NET každé přidělení objektu větší než 85 KB skončí v haldě velkého objektu ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Velké objekty jsou drahé dvěma způsoby:

* Náklady na přidělení je vysoká, protože paměť pro nově přidělený velký objekt musí být vymazány. CLR zaručuje, že paměť pro všechny nově přidělené objekty je vymazána.
* LOH se shromažďuje se zbytkem haldy. LOH vyžaduje úplné [uvolnění paměti](/dotnet/standard/garbage-collection/fundamentals) nebo [Gen2 kolekce](/dotnet/standard/garbage-collection/fundamentals#generations).

Tento [blog post](https://adamsitnik.com/Array-Pool/#the-problem) popisuje problém stručně:

> Když je přidělen velký objekt, je označen jako objekt Gen 2. Ne Gen 0 jako pro malé objekty. Důsledky jsou, že pokud vám dojdou paměti v LOH, GC vyčistí celou spravovanou haldu, nejen LOH. Takže to uklízí Gen 0, Gen 1 a Gen 2 včetně LOH. To se nazývá úplné uvolnění paměti a je nejvíce časově náročné uvolňování paměti. Pro mnoho aplikací může být přijatelné. Ale rozhodně ne pro vysoce výkonné webové servery, kde je zapotřebí několik velkých vyrovnávacích pamětí pro zpracování průměrného webového požadavku (čtení ze soketu, dekomprese, dekódování JSON & další).

Naivně ukládání velké žádosti nebo odpovědi těla do jednoho `byte[]` nebo `string`:

* Může mít za následek rychlý nedostatek místa v LOH.
* Může způsobit problémy s výkonem aplikace z důvodu úplného spuštění řadičů domény.

## <a name="working-with-a-synchronous-data-processing-api"></a>Práce s rozhraním API pro synchronní zpracování dat

Při použití serializátoru/deserializátoru, který podporuje pouze synchronní čtení a zápisy (například [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Před jejich předáním do serializátoru/deserializátoru asynchronně udeřit data do paměti.

> [!WARNING]
> Pokud je požadavek velký, může vést k nedostatku paměti (OOM) podmínku. OOM může mít za následek odmítnutí služby.  Další informace naleznete v [tématu Vyhněte se čtení velkých těl požadavků nebo těl odpovědí do paměti](#arlb) v tomto dokumentu.

ASP.NET Core 3.0 používá <xref:System.Text.Json> ve výchozím nastavení pro serializaci JSON. <xref:System.Text.Json>:

* Čte a zapisuje JSON asynchronně.
* Je optimalizován pro Text UTF-8.
* Obvykle vyšší výkon `Newtonsoft.Json`než .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Neukládat iHttpContextAccessor.HttpContext do pole

[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) vrátí `HttpContext` aktivní požadavek při přístupu z vlákna požadavku. By `IHttpContextAccessor.HttpContext` **neměly** být uloženy v poli nebo proměnné.

**Nedělají toto:** Následující příklad uloží `HttpContext` pole a pokusí se jej použít později.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Předchozí kód často zachycuje null nebo `HttpContext` nesprávné v konstruktoru.

**Proveďte toto:** Následující příklad:

* Ukládá <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> do pole.
* Použije `HttpContext` pole ve správný čas `null`a zkontroluje .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Nepřistupovat httpcontext z více vláken

`HttpContext`*Není bezpečné* pro přístup z více vláken. Přístup `HttpContext` z více vláken paralelně může mít za následek nedefinované chování, jako je zablokování, selhání a poškození dat.

**Nedělají toto:** Následující příklad provede tři paralelní požadavky a protokoly příchozí požadavek cestu před a po odchozí požadavek HTTP. Cesta požadavku je přístupná z více vláken, potenciálně paralelně.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Proveďte toto:** Následující příklad zkopíruje všechna data z příchozí požadavek před provedením tři paralelní požadavky.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Nepoužívejte httpcontext po dokončení požadavku

`HttpContext`Je platný pouze tak dlouho, dokud je aktivní požadavek HTTP v kanálu ASP.NET Jádra. Celý kanál ASP.NET Core je asynchronní řetězec delegátů, který provede každý požadavek. Po `Task` dokončení vrácené z tohoto `HttpContext` řetězce je recyklován.

**Nedělají toto:** Následující příklad `async void` používá, který umožňuje požadavek `await` HTTP dokončit po dosažení prvního:

* Což je **vždy** špatná praxe v ASP.NET aplikace Core.
* Přistupuje po `HttpResponse` dokončení požadavku HTTP.
* Dojde k chybě procesu.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Proveďte toto:** Následující příklad vrátí `Task` a do rámce, takže požadavek HTTP nedokončí, dokud nebude akce dokončena.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Nezachytávat HttpContext ve vláknech na pozadí

**Nedělají toto:** Následující příklad ukazuje uzavření je `HttpContext` zachycení `Controller` z vlastnosti. To to je špatný postup, protože pracovní položka může:

* Spustit mimo obor požadavku.
* Pokus te se `HttpContext`přečíst špatné .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Proveďte toto:** Následující příklad:

* Zkopíruje data požadovaná v úkolu na pozadí během požadavku.
* Neodkazuje na nic z ovladače.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Úlohy na pozadí by měly být implementovány jako hostované služby. Další informace naleznete [v tématu Úlohy na pozadí s hostovanými službami](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Nezachytávat služby vložené do řadičů na podprocesech na pozadí

**Nedělají toto:** Následující příklad ukazuje uzavření je `DbContext` zachycení `Controller` z parametru akce. Tohle je špatná praxe.  Pracovní položka může být spuštěna mimo obor požadavku. Rozsah `ContosoDbContext` je na požadavek, výsledkem `ObjectDisposedException`.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Proveďte toto:** Následující příklad:

* Vloží <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> vysazení v za účelem vytvoření oboru v pracovní položce na pozadí. `IServiceScopeFactory`je singleton.
* Vytvoří nový obor vkládání závislostí ve vlákně na pozadí.
* Neodkazuje na nic z ovladače.
* Nezachytí `ContosoDbContext` příchozí požadavek.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Následující zvýrazněný kód:

* Vytvoří obor pro životnost operace na pozadí a řeší služby z něj.
* Používá `ContosoDbContext` ze správného oboru.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Po spuštění textu odpovědi neupravujte stavový kód ani záhlaví

ASP.NET Core neuvádí tělo odpovědi HTTP. Při prvním zápisu odpovědi:

* Hlavičky jsou odesílány spolu s tímto blokem těla klientovi.
* Již není možné měnit záhlaví odpovědí.

**Nedělají toto:** Následující kód se pokusí přidat záhlaví odpovědí po již spuštění odpovědi:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

V předchozím kódu `context.Response.Headers["test"] = "test value";` vyvolá výjimku, `next()` pokud byla zapsána do odpovědi.

**Proveďte toto:** Následující příklad zkontroluje, zda byla před úpravou záhlaví spuštěna odpověď HTTP.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Proveďte toto:** Následující příklad `HttpResponse.OnStarting` používá k nastavení záhlaví před hlavičky odpovědi jsou vyprázdněny do klienta.

Kontrola, zda odpověď nebyla spuštěna umožňuje registraci zpětného volání, které bude vyvoláno těsně před zápisem hlaviček odpovědí. Kontrola, zda odpověď nebyla spuštěna:

* Poskytuje možnost připojit nebo přepsat záhlaví právě včas.
* Nevyžaduje znalost dalšího middlewaru v potrubí.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Nevolejte next(), pokud jste již začali psát do těla odpovědi

Součásti očekávat pouze v případě, že je možné pro ně zpracovat a manipulovat s odpovědí.
