---
title: Osvědčené postupy týkající se ASP.NET Core výkonu
author: mjrousos
description: Tipy pro zvýšení výkonu v aplikacích ASP.NET Core a předcházení běžným problémům s výkonem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: 587872b269d897d7c86eb77c110a4b6432218ed3
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746556"
---
# <a name="aspnet-core-performance-best-practices"></a>Osvědčené postupy týkající se ASP.NET Core výkonu

O [Jan Rousos](https://github.com/mjrousos)

Tento článek poskytuje pokyny pro osvědčené postupy výkonu ASP.NET Core.

## <a name="cache-aggressively"></a>Ukládat do mezipaměti agresivní

Mezipaměť je popsána v několika částech tohoto dokumentu. Další informace naleznete v tématu <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Pochopení cest k horkému kódu

V tomto dokumentu je *horká cesta kódu* definována jako cesta kódu, která je často volána a kde velká doba spuštění nastane. Cesty horkého kódu obvykle omezují škálování a výkon aplikace a jsou popsány v několika částech tohoto dokumentu.

## <a name="avoid-blocking-calls"></a>Vyhnout se blokování volání

ASP.NET Core aplikace by měly být navržené tak, aby souběžně zpracovaly hodně požadavků. Asynchronní rozhraní API umožňují malému fondu vláken zpracovávat tisíce souběžných požadavků a nečekají na blokující volání. Místo čekání na dokončení dlouhotrvající synchronní úlohy může vlákno pracovat na jiné žádosti.

Běžný problém s výkonem v ASP.NET Core aplikace blokuje volání, která by mohla být asynchronní. Mnoho synchronních blokujících volání vede k [vyčerpání fondu vláken](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) a snížení doby odezvy.

**Nepoužívejte**:

* Zablokuje asynchronní spuštění voláním [Task. Wait](/dotnet/api/system.threading.tasks.task.wait) nebo [Task. Result](/dotnet/api/system.threading.tasks.task-1.result).
* Získejte zámky v běžných cestách kódu. ASP.NET Core aplikace jsou nejvíc výkonné, když je navrženo paralelní spouštění kódu.
* Zavolejte [úlohu. Spusťte](/dotnet/api/system.threading.tasks.task.run) ji a ihned ji proveďte. ASP.NET Core v normálních vláknech fondu vláken již spouští kód aplikace, takže volání úlohy. Výsledkem je nadbytečné plánování nepotřebného fondu vláken. I v případě, že je v plánovaném kódu zablokované vlákno, úloha. Run nebrání.

**Do**:

* Proveďte asynchronní vytváření [cest kódu](#understand-hot-code-paths) .
* Pokud je k dispozici asynchronní rozhraní API, volejte asynchronní rozhraní API pro přístup k datům, vstupně-výstupní operace a dlouhotrvající provozní rozhraní. Nepoužívejte rutinu [Task. Run](/dotnet/api/system.threading.tasks.task.run) , aby se asynchronní rozhraní API **nepoužívalo** synchronně.
* Provede Razor asynchronní akce kontroleru nebo stránky. Celý zásobník volání je asynchronní, aby bylo možné využívat vzory [Async/await](/dotnet/csharp/programming-guide/concepts/async/) .

Profiler, například [PerfView](https://github.com/Microsoft/perfview), lze použít k nalezení často přidaných vláken do [fondu vláken](/windows/desktop/procthread/thread-pools). `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`Událost indikuje vlákno přidané do fondu vláken. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a>Vrátit IEnumerable \<T> nebo IAsyncEnumerable\<T>

`IEnumerable<T>`Výsledkem vrácení z akce je synchronní iterace kolekce serializátorem. Výsledkem je blokování volání a potenciál pro fond vláken vyčerpání. Chcete-li se vyhnout synchronnímu výčtu, použijte `ToListAsync` před vrácením vyčíslitelné.

Počínaje ASP.NET Core 3,0 `IAsyncEnumerable<T>` lze použít jako alternativu k `IEnumerable<T>` asynchronnímu vytváření výčtu. Další informace najdete v tématu [návratový typ akce kontroleru](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet).

## <a name="minimize-large-object-allocations"></a>Minimalizace přidělení velkých objektů

[Systém uvolňování paměti .NET Core](/dotnet/standard/garbage-collection/) spravuje přidělování a uvolňování paměti automaticky v aplikacích ASP.NET Core. Automatické uvolňování paměti obvykle znamená, že se vývojáři nemuseli starat o způsob uvolnění paměti. Vyčištění neodkazovaných objektů však zabere čas procesoru, takže vývojáři by měli minimalizovat přidělování objektů v [Hot recestách kódu](#understand-hot-code-paths). Uvolňování paměti je mimořádně nákladné u velkých objektů (> 85 KB). Velké objekty jsou uloženy v [haldě velkých objektů](/dotnet/standard/garbage-collection/large-object-heap) a vyžadují úplné uvolnění paměti (2. generace) pro vyčištění. Na rozdíl od generace 0 a 1. generace kolekce vyžaduje shromažďování 2 dočasné pozastavení provádění aplikace. Časté přidělení a zrušení přidělení velkých objektů může způsobit nekonzistentní výkon.

Doporučit

* **Zvažte ukládání** velkých často používaných objektů do mezipaměti. Ukládání velkých objektů do mezipaměti brání nákladným přidělením.
* **Do vyrovnávací paměti ve fondu můžete** ukládat velká pole pomocí [ArrayPool \<T> ](/dotnet/api/system.buffers.arraypool-1) .
* **Nepřiřazujte** mnoho nenáročnéch velkých objektů na [cesty horkého kódu](#understand-hot-code-paths).

Problémy s pamětí, jako je například předchozí, lze diagnostikovat podle statistik uvolňování paměti (GC) v [PerfView](https://github.com/Microsoft/perfview) a prověřování:

* Doba pozastavení uvolňování paměti
* Jaké procento času procesoru stráví uvolňováním paměti.
* Kolik uvolňování paměti je generace 0, 1 a 2.

Další informace najdete v tématu [shromažďování a výkon uvolňování paměti](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Optimalizujte přístup k datům a vstupně-výstupní operace

Interakce s úložištěm dat a dalšími vzdálenými službami jsou často nejpomalejšími částmi ASP.NET Core aplikace. Efektivní čtení a zápis dat je zásadní pro dobrý výkon.

Doporučit

* **Volejte všechna** rozhraní API pro přístup k datům asynchronně.
* **Nečítat více** dat, než je nutné. Zápis dotazů, které vrátí pouze data potřebná pro aktuální požadavek HTTP.
* **Zvažte ukládání** často používaných dat načtených z databáze nebo vzdálené služby, pokud jsou přijatelné mírně zastaralá data. V závislosti na scénáři použijte [MemoryCache](xref:performance/caching/memory) nebo [DistributedCache](xref:performance/caching/distributed). Další informace naleznete v tématu <xref:performance/caching/response>.
* **Minimalizujte** síťové zpáteční cykly. Cílem je načíst požadovaná data v jednom volání namísto několika volání.
* Při přístupu k datům pro účely jen pro **čtení používejte v** Entity Framework Core [dotazy bez sledování](/ef/core/querying/tracking#no-tracking-queries) . EF Core může vracet výsledky nesledovaných dotazů efektivněji.
* **Filtrujte a** AGREGOVAT dotazy LINQ (například pomocí `.Where` `.Select` příkazů,, nebo `.Sum` ), aby bylo filtrování provedeno v databázi.
* **Vezměte v** úvahu, že EF Core řeší některé operátory pro dotazování v klientovi, což může vést k neefektivnímu provádění dotazů. Další informace najdete v tématu [problémy s výkonem Hodnocení klientů](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Nepoužívejte dotazy** projekce na kolekcích, což může vést k provádění dotazů SQL N + 1. Další informace najdete v tématu [optimalizace korelačních poddotazů](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Podívejte se na [vysoký výkon](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) pro přístupy, které můžou zlepšit výkon v vysoce škálovatelných aplikacích:

* [Sdružování DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explicitně kompilované dotazy](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Před potvrzením základu kódu doporučujeme měřit dopad předchozích přístupů. Další složitost kompilovaných dotazů nemusí zlepšit zvýšení výkonu.

Problémy s dotazy se dají zjistit tak, že si prohlédnete čas strávený přístupem k datům pomocí [Application Insights](/azure/application-insights/app-insights-overview) nebo pomocí nástrojů pro profilaci. Většina databází také zpřístupňuje statistické údaje týkající se často spuštěných dotazů.

## <a name="pool-http-connections-with-httpclientfactory"></a>Připojení fondů HTTP k HttpClientFactory

I když [HttpClient](/dotnet/api/system.net.http.httpclient) implementuje `IDisposable` rozhraní, je navrženo pro opakované použití. Uzavřené `HttpClient` instance nechají v krátké době otevřené sokety ve `TIME_WAIT` stavu. Pokud se často používá cesta kódu, která vytváří a uvolňuje `HttpClient` objekty, může aplikace vyčerpat dostupné sokety. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) byl představen v ASP.NET Core 2,1 jako řešení tohoto problému. Zpracovává připojení HTTP ve fondu za účelem optimalizace výkonu a spolehlivosti.

Doporučit

* **Nevytvářejte a neodstraňujte** `HttpClient` instance přímo.
* **Do** K načtení instancí použijte [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) `HttpClient` . Další informace najdete v tématu [použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Rychlé udržování běžných cest kódu

Chcete, aby byl veškerý kód rychlý. Často volané cesty kódu jsou nejdůležitější pro optimalizaci. Zde jsou některé z nich:

* Komponenty middlewaru v kanálu zpracování požadavků aplikace, zejména middleware spouštěné včas v kanálu. Tyto součásti mají velký dopad na výkon.
* Kód, který se spustí pro každý požadavek nebo vícekrát na požadavek. Například vlastní protokolování, obslužné rutiny autorizace nebo inicializace přechodných služeb.

Doporučit

* **Nepoužívejte vlastní** součásti middlewaru s dlouhodobě běžícími úkoly.
* **Pomocí nástrojů** pro profilaci výkonu, jako je [Visual Studio diagnostické nástroje](/visualstudio/profiling/profiling-feature-tour) nebo [PerfView](https://github.com/Microsoft/perfview)), identifikujte [cesty k horkému kódu](#understand-hot-code-paths).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Dokončení dlouhotrvajících úloh mimo požadavky HTTP

Většinu požadavků na aplikaci ASP.NET Core může zpracovat řadič nebo model stránky, který volá potřebné služby a vrací odpověď HTTP. U některých požadavků, které zahrnují dlouhotrvající úlohy, je lepší provést asynchronní zpracování celého procesu požadavků a odpovědí.

Doporučit

* **Nečekejte** na dokončení dlouhotrvajících úloh jako součást běžného zpracování požadavků protokolu HTTP.
* **Vezměte v** úvahu zpracování dlouho běžících požadavků se [službami na pozadí](xref:fundamentals/host/hosted-services) nebo mimo proces s [funkcí Azure Functions](/azure/azure-functions/). Dokončení práce mimo proces je zvláště užitečné pro úlohy náročné na procesor.
* **Do** [SignalR](xref:signalr/introduction) K asynchronní komunikaci s klienty použijte možnosti komunikace v reálném čase, například.

## <a name="minify-client-assets"></a>Prostředky klienta minimalizuje

ASP.NET Core aplikace s komplexní front-endy často obsluhují mnoho souborů JavaScript, CSS nebo obrázků. Výkon požadavků na počáteční zatížení může zlepšit:

* Sdružování, které kombinuje více souborů do jednoho.
* Minifikace, která zmenšuje velikost souborů odebráním prázdných znaků a komentářů.

Doporučit

* **Využijte** [předdefinovanou podporu](xref:client-side/bundling-and-minification) ASP.NET Core pro sdružování a minifikaceí prostředků klientů.
* Pro komplexní správu prostředků **klienta zvažte další** nástroje třetích stran, jako je například [Webpack](https://webpack.js.org/).

## <a name="compress-responses"></a>Komprimovat odpovědi

 Zmenšení velikosti odpovědi obvykle zvyšuje rychlost odezvy aplikace, často výrazně. Jedním ze způsobů, jak omezit velikost datových částí, je komprimace reakcí aplikace. Další informace najdete v tématu [odezva komprese](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Použít nejnovější verzi ASP.NET Core

Každé nové vydání ASP.NET Core zahrnuje vylepšení výkonu. Optimalizace v .NET Core a ASP.NET Core znamenají, že novější verze obecně překoná starší verze. Například rozhraní .NET Core 2,1 přidalo podporu kompilovaných regulárních výrazů a benefitted [z \<T> rozsahu](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay). ASP.NET Core 2,2 přidali podporu pro HTTP/2. [ASP.NET Core 3,0 přidává mnoho vylepšení](xref:aspnetcore-3.0) , která omezují využití paměti a zvyšují propustnost. Pokud je výkon prioritou, zvažte upgrade na aktuální verzi ASP.NET Core.

## <a name="minimize-exceptions"></a>Minimalizace výjimek

Výjimky by měly být vzácné. Vyvolávání a zachycování výjimek je pomalé vzhledem k jiným vzorům toku kódu. Z tohoto důvodu by se výjimky neměly používat k řízení normálního toku programu.

Doporučit

* **Nepoužívejte vyvolání** nebo zachycení výjimek jako způsob normálního toku programu, zejména v případě [aktivních cest kódu](#understand-hot-code-paths).
* **Do aplikace zahrňte logiku** , která zjišťuje a zpracovává podmínky, které by způsobily výjimku.
* **Vyvolejte nebo** Zachyťte výjimky pro neobvyklé nebo neočekávané podmínky.

Nástroje pro diagnostiku aplikací, jako je například Application Insights, můžou pomáhat identifikovat běžné výjimky v aplikaci, která může mít vliv na výkon.

## <a name="performance-and-reliability"></a>Výkon a spolehlivost

V následujících částech najdete tipy ke zvýšení výkonu a známé problémy s spolehlivostí a řešení.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Vyhněte se synchronnímu čtení nebo zápisu na HttpRequest/HttpResponse tělo

Všechny vstupně-výstupní operace v ASP.NET Core jsou asynchronní. Servery implementují `Stream` rozhraní, které má synchronní i asynchronní přetížení. Asynchronní objekty by měly být upřednostňovány, aby se zabránilo blokování vláken fondu vláken. Blokování vláken může vést k vyčerpání fondu vláken.

**Neprovádět tyto akce:** Následující příklad používá <xref:System.IO.StreamReader.ReadToEnd*> . Zablokuje aktuální vlákno, aby čekal na výsledek. Toto je příklad [synchronizace přes Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

V předchozím kódu `Get` synchronně přečte celý text požadavku HTTP do paměti. Pokud se klient pomalu odesílá, aplikace provádí synchronizaci přes Async. Aplikace provádí synchronizaci přes Async, protože **Kestrel nepodporuje synchronní** čtení.

**Postupujte takto:** Následující příklad používá <xref:System.IO.StreamReader.ReadToEndAsync*> a neblokuje vlákno při čtení.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Předchozí kód asynchronně načte celý text požadavku HTTP do paměti.

> [!WARNING]
> Pokud je žádost velká, čtení celého textu požadavku HTTP do paměti by mohlo vést k nedostatku paměti (OOM). OOM může mít za následek odepření služby.  Další informace najdete v tématu [zamezení čtení velkých těla žádostí nebo těla reakcí do paměti](#arlb) v tomto dokumentu.

**Postupujte takto:** Následující příklad je plně asynchronní pomocí textu žádosti bez vyrovnávací paměti:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Předchozí kód asynchronně deserializace tělo požadavku do objektu jazyka C#.

## <a name="prefer-readformasync-over-requestform"></a>Preferovat ReadFormAsync přes Request. Form

`HttpContext.Request.ReadFormAsync`Místo použijte `HttpContext.Request.Form` .
`HttpContext.Request.Form` lze bezpečně číst pouze s následujícími podmínkami:

* Formulář byl přečten voláním `ReadFormAsync` a
* Hodnota formuláře v mezipaměti je čtena pomocí `HttpContext.Request.Form`

**Neprovádět tyto akce:** Následující příklad používá `HttpContext.Request.Form` .  `HttpContext.Request.Form` používá [synchronizaci přes Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) a může vést k vyčerpání fondu vláken.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Postupujte takto:** Následující příklad používá `HttpContext.Request.ReadFormAsync` pro asynchronní čtení těla formuláře.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Vyhněte se čtení velkých těla žádostí nebo těla reakcí do paměti

V rozhraní .NET končí každá alokace objektu větší než 85 KB v haldě velkých objektů ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Velké objekty jsou nákladné dvěma způsoby:

* Náklady na přidělení jsou vysoké, protože paměť pro nově přidělený velký objekt musí být smazána. Modul CLR garantuje, že paměť pro všechny nově přidělené objekty jsou vymazány.
* LOH se shromáždí se zbytkem haldy. LOH vyžaduje úplné [uvolňování paměti](/dotnet/standard/garbage-collection/fundamentals) nebo [kolekci Gen2](/dotnet/standard/garbage-collection/fundamentals#generations).

Tento [Blogový příspěvek](https://adamsitnik.com/Array-Pool/#the-problem) popisuje stručně problém:

> Když je přidělen velký objekt, je označen jako objekt Gen 2. Nejedná se o gen 0 jako u malých objektů. Důsledkem je, že pokud vyčerpáte paměť v LOH, UVOLŇOVÁNí paměti vyčistí celou spravovanou haldu, nejen LOH. Proto vyčistí obecné 0, obecné 1 a obecné 2 včetně LOH. Nazývá se to úplné uvolňování paměti a je to nejvíce časově náročné uvolňování paměti. U mnoha aplikací může být přijatelné. Ale pro vysoce výkonné webové servery, u kterých je potřeba pár paměťových vyrovnávacích pamětí potřebných ke zpracování průměrného webového požadavku (čtení z soketu, dekomprese, dekódování JSON & více), ale opravdu ne.

Naively ukládání velkého textu žádosti nebo odpovědi do jednoho nebo více `byte[]` `string` :

* Může způsobit, že dojde k rychlému vyzkoušení volného místa v LOH.
* Může způsobit problémy s výkonem aplikace z důvodu úplného GC běhu.

## <a name="working-with-a-synchronous-data-processing-api"></a>Práce s rozhraním API pro synchronní zpracování dat

Při použití serializátoru/zrušení serializátoru, který podporuje pouze synchronní čtení a zápisy (například  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Ukládat data do vyrovnávací paměti asynchronně předtím, než je předáte do serializátoru/zrušení serializátoru.

> [!WARNING]
> Pokud je žádost velká, může to vést k nedostatku paměti (OOM). OOM může mít za následek odepření služby.  Další informace najdete v tématu [zamezení čtení velkých těla žádostí nebo těla reakcí do paměti](#arlb) v tomto dokumentu.

ASP.NET Core 3,0 používá <xref:System.Text.Json> ve výchozím nastavení pro serializaci JSON. <xref:System.Text.Json>:

* Čte a zapisuje JSON asynchronně.
* Je optimalizován pro text v kódování UTF-8.
* Obvykle vyšší výkon než `Newtonsoft.Json` .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Do pole neukládejte IHttpContextAccessor. HttpContext.

[IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) vrací `HttpContext` aktivní požadavek při přístupu z vlákna požadavku. `IHttpContextAccessor.HttpContext`Neměl by **not** být uložen v poli nebo proměnné.

**Neprovádět tyto akce:** V následujícím příkladu je uloženo `HttpContext` v poli a pak se pokusí o pozdější použití.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Předchozí kód často zachycuje v konstruktoru hodnotu null nebo není správný `HttpContext` .

**Postupujte takto:** Následující příklad:

* Uloží pole <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> do pole.
* Používá `HttpContext` pole ve správné době a kontroluje pro `null` .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Nepřistupovat k HttpContext z více vláken

`HttpContext` není bezpečná pro přístup *z více vláken* . Přístup `HttpContext` z více vláken paralelně může způsobit nedefinované chování, jako je například zablokování, zhroucení a poškození dat.

**Neprovádět tyto akce:** Následující příklad vytvoří tři paralelní požadavky a zaznamená příchozí cestu požadavku před a za odchozí požadavek HTTP. Cesta k požadavku je k dispozici z více vláken, potenciálně paralelně.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Postupujte takto:** Následující příklad kopíruje všechna data z příchozího požadavku a teprve potom provádí tři paralelní požadavky.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Nepoužívat HttpContext po dokončení žádosti

`HttpContext` je platná pouze tak dlouho, dokud je v kanálu ASP.NET Core aktivní požadavek HTTP. Celý kanál ASP.NET Core je asynchronní řetěz delegátů, který provádí všechny požadavky. Po `Task` dokončení operace vrácené z tohoto řetězu `HttpContext` se recykluje.

**Neprovádět tyto akce:** Následující příklad používá, aby `async void` byl požadavek HTTP dokončen při prvním dosažení prvního `await` :

* Což je **vždy** špatný postup v aplikacích ASP.NET Core.
* Přístup k `HttpResponse` po dokončení požadavku HTTP.
* Dojde k chybě procesu.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Postupujte takto:** Následující příklad vrátí `Task` rozhraní, takže požadavek HTTP nebude dokončen, dokud se akce nedokončí.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Nezachytit vlastnost HttpContext v vláknech na pozadí

**Neprovádět tyto akce:** Následující příklad ukazuje, že uzavření zachytí `HttpContext` z `Controller` Vlastnosti. Toto je špatný postup, protože pracovní položka by mohla:

* Spustit mimo rozsah požadavku.
* Došlo k pokusu o čtení špatného `HttpContext` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Postupujte takto:** Následující příklad:

* Zkopíruje data potřebná v úloze na pozadí během žádosti.
* Neodkazuje na cokoli z kontroleru.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Úlohy na pozadí by se měly implementovat jako hostované služby. Další informace najdete v tématu [úlohy na pozadí s hostovanými službami](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Nezachytávat služby vložené do řadičů v vláknech na pozadí

**Neprovádět tyto akce:** Následující příklad ukazuje, že uzavření zachytí `DbContext` z `Controller` parametru action. Toto je špatný postup.  Pracovní položka by mohla běžet mimo rozsah požadavku. `ContosoDbContext`Je vymezen na žádost, výsledkem je `ObjectDisposedException` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Postupujte takto:** Následující příklad:

* Vloží objekt <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> , aby bylo možné vytvořit obor v pracovní položce na pozadí. `IServiceScopeFactory` je typu singleton.
* Vytvoří nový rozsah vkládání závislostí ve vlákně na pozadí.
* Neodkazuje na cokoli z kontroleru.
* Nezachycuje `ContosoDbContext` z příchozího požadavku.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Následující zvýrazněný kód:

* Vytvoří obor pro dobu života operace na pozadí a vyřeší z něj služby.
* Používá `ContosoDbContext` se správným oborem.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Neměňte stavový kód ani hlavičky po zahájení textu odpovědi

ASP.NET Core neukládá obsah odpovědi HTTP do vyrovnávací paměti. Při prvním zápisu odpovědi:

* Hlavičky jsou odesílány spolu s tímto blokem těla do klienta.
* Již není možné měnit hlavičky odpovědí.

**Neprovádět tyto akce:** Následující kód se pokusí přidat hlavičky odpovědi poté, co byla odpověď již spuštěna:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

V předchozím kódu `context.Response.Headers["test"] = "test value";` vyvolá výjimku, pokud `next()` zapisuje do odpovědi.

**Postupujte takto:** Následující příklad zkontroluje, zda byla před úpravou hlaviček spuštěna odpověď protokolu HTTP.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Postupujte takto:** Následující příklad používá `HttpResponse.OnStarting` k nastavení hlaviček před vyprázdněním hlaviček odpovědí na klienta.

Kontrola, zda odpověď nezačala, umožňuje registraci zpětného volání, které bude vyvoláno těsně před zápisem hlaviček odpovědí. Kontroluje se, jestli odpověď nezačala:

* Poskytuje možnost připojit nebo přepsat hlavičky v čase.
* Nevyžaduje znalosti dalšího middlewaru v kanálu.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Nevolejte Next (), pokud jste již začali zapisovat do těla odpovědi

Součásti, které mají být volány, jsou očekávány pouze v případě, že je možné je zpracovat a manipulovat s ní.

## <a name="use-in-process-hosting-with-iis"></a>Použití vnitroprocesového hostování se službou IIS

Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS. Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky. Adaptér zpětné smyčky je síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače. Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Projekty jsou ve výchozím nastavení pro model hostování v rámci procesu v ASP.NET Core 3,0 a novějším.

Další informace najdete v tématu [hostitelská ASP.NET Core ve Windows se službou IIS](xref:host-and-deploy/iis/index) .
