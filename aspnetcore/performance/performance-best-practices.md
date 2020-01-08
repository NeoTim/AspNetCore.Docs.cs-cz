---
title: Osvědčené postupy týkající se ASP.NET Core výkonu
author: mjrousos
description: Tipy pro zvýšení výkonu aplikací ASP.NET Core a předcházení běžným problémům s výkonem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 12/05/2019
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: c74adf7479d176c41dc26c7e77acfc3dc9cdcb88
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693957"
---
# <a name="aspnet-core-performance-best-practices"></a>Osvědčené postupy týkající se ASP.NET Core výkonu

O [Jan Rousos](https://github.com/mjrousos)

Tento článek poskytuje pokyny pro osvědčené postupy výkonu ASP.NET Core.

## <a name="cache-aggressively"></a>Agresivní ukládání do mezipaměti

Ukládání do mezipaměti je podrobněji popsáno v několika částech tohoto dokumentu. Další informace najdete v tématu <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Pochopení cest k horkému kódu

V tomto dokumentu je *horká cesta kódu* definována jako cesta kódu, která je často volána a kde velká doba spuštění nastane. Cesty horkého kódu obvykle omezují škálování a výkon aplikace a jsou popsány v několika částech tohoto dokumentu.

## <a name="avoid-blocking-calls"></a>Předcházejte blokujícím voláním

Aplikace ASP.NET Core by měly být navrhovány tak, aby dokázaly zpracovat velký počet požadavků současně. Asynchronní rozhraní API umožňují malému fondu vláken zpracovat tisíce souběžných požadavků tím, že neblokují volání, takže namísto čekání na dokončení dlouhotrvající synchronní úlohy může vlákno zpracovávat další požadavek.

Častý problém výkonu v aplikacích ASP.NET Core je blokování volání, které mohlo být asynchronní. Mnoho synchronních blokujících volání vede k [vyčerpání fondu vláken](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) a snížení doby odezvy.

**Nedělejte**:

* Neblokujte asynchronní běh zavoláním [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) nebo [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Nepoužívejte zámky v kritických cestách kódu. Aplikace ASP.NET Core dosahují nejlepšího výkonu, když jsou navrženy tak, aby spouštěly kód paralelně.
* Zavolejte [úlohu. Spusťte](/dotnet/api/system.threading.tasks.task.run) ji a ihned ji proveďte. ASP.NET Core v normálních vláknech fondu vláken již spouští kód aplikace, takže volání úlohy. Výsledkem je nadbytečné plánování nepotřebného fondu vláken. I v případě, že je v plánovaném kódu zablokované vlákno, úloha. Run nebrání.

**Dělejte**:

* Ujistěte se, že [kritické cesty](#understand-hot-code-paths) jsou asynchronní.
* Pokud je k dispozici asynchronní rozhraní API, volejte asynchronní rozhraní API pro přístup k datům, vstupně-výstupní operace a dlouhotrvající provozní rozhraní. Nepoužívejte rutinu [Task. Run](/dotnet/api/system.threading.tasks.task.run) , aby rozhraní API synchronus bylo asynchronní.
* Ujistěte se, že jsou controllery a Razor akce stránek asynchronní. Celý zásobník volání je asynchronní, aby bylo možné využívat vzory [Async/await](/dotnet/csharp/programming-guide/concepts/async/) .

Profiler, například [PerfView](https://github.com/Microsoft/perfview), lze použít k nalezení často přidaných vláken do [fondu vláken](/windows/desktop/procthread/thread-pools). Událost `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` označuje vlákno přidané do fondu vláken. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Minimalizujte velké alokace objektů

[Systém uvolňování paměti .NET Core](/dotnet/standard/garbage-collection/) spravuje přidělování a uvolňování paměti automaticky v aplikacích ASP.NET Core. Automatické uvolňování paměti obvykle znamená, že se vývojáři nemuseli starat o způsob uvolnění paměti. Vyčištění neodkazovaných objektů však zabere čas procesoru, takže vývojáři by měli minimalizovat přidělování objektů v [Hot recestách kódu](#understand-hot-code-paths). Uvolňování paměti je mimořádně nákladné u velkých objektů (> 85 KB). Velké objekty jsou uloženy v [haldě velkých objektů](/dotnet/standard/garbage-collection/large-object-heap) a vyžadují úplné uvolnění paměti (2. generace) pro vyčištění. Na rozdíl od generace 0 a 1. generace kolekce vyžaduje shromažďování 2 dočasné pozastavení provádění aplikace. Časté přidělení a zrušení přidělení velkých objektů může způsobit nekonzistentní výkon.

Doporučit

* **Dělejte:** Zvažte ukládání často používaných velkých objektů do mezipaměti. Zabráníte tím náročným alokacím.
* Využijte vyrovnávací **paměti fondu pomocí** [ArrayPool\<t >](/dotnet/api/system.buffers.arraypool-1) k ukládání velkých polí.
* **Nedělejte:** Nealokujte mnoho krátkodobých velkých objektů na [kritické cestě](#understand-hot-code-paths).

Problémy s pamětí, jako je například předchozí, lze diagnostikovat podle statistik uvolňování paměti (GC) v [PerfView](https://github.com/Microsoft/perfview) a prověřování:

* Dobu pozastavení uvolnění paměti.
* Jaké procento času procesoru zabírá uvolňování paměti.
* Kolik uvolnění paměti jsou 0, 1 a 2. generace.

Další informace najdete v tématu [uvolňování paměti a výkon](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Optimalizujte přístup k datům a vstupně-výstupní operace

Interakce s úložištěm dat a dalšími vzdálenými službami jsou často nejpomalejšími částmi ASP.NET Core aplikace. Efektivní čtení a zápis dat jsou klíčové pro dobrý výkon.

Doporučit

* **Dělejte:** Všechna rozhraní API pro přístup k datům volejte asynchronně.
* **Nedělejte:** Nenačítejte více dat, než je nezbytné. Zápis dotazů, které vrátí pouze data potřebná pro aktuální požadavek HTTP.
* **Zvažte ukládání** často používaných dat načtených z databáze nebo vzdálené služby, pokud jsou přijatelné mírně zastaralá data. V závislosti na scénáři použijte [MemoryCache](xref:performance/caching/memory) nebo [DistributedCache](xref:performance/caching/distributed). Další informace najdete v tématu <xref:performance/caching/response>.
* **Minimalizujte** síťové zpáteční cykly. Cílem je načíst požadovaná data v jednom volání namísto několika volání.
* **Dělejte:** Použijte [dotazy bez sledování](/ef/core/querying/tracking#no-tracking-queries) v Entity Framework Core při přístupu k datům pouze pro čtení. EF Core může vrátit výsledky dotazů bez sledování efektivněji.
* **Filtrujte a** AGREGUJE dotazy LINQ (například pomocí `.Where`, `.Select`nebo `.Sum` příkazy) tak, aby se filtrování provádělo v databázi.
* **Dělejte:** Vezměte v úvahu, že EF Core řeší některé operátory dotazu na klientovi, což může vést k neefektivnímu provádění dotazů. Další informace najdete v tématu [problémy s výkonem Hodnocení klientů](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Nedělejte:** Nepoužívejte dotazy projekce na kolekce, pokud to může vést k provádění "N + 1" SQL dotazů. Další informace najdete v tématu [optimalizace korelovaných poddotazů](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Podívejte se na článek [vysoký výkon EF](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries), kde najdete metody, jak zlepšit výkon ve vysoce škálovatelných aplikacích:

* [Sdružování DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explicitně kompilované dotazy](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Před potvrzením základu kódu doporučujeme měřit dopad předchozích přístupů. Může se stát, že přidaná složitost kompilovaných dotazů bude větší než přínosy zlepšení výkonu.

Problémy s dotazy se dají zjistit tak, že si prohlédnete čas strávený přístupem k datům pomocí [Application Insights](/azure/application-insights/app-insights-overview) nebo pomocí nástrojů pro profilaci. Většina databází také poskytuje statistiky týkající se často spouštěných dotazů.

## <a name="pool-http-connections-with-httpclientfactory"></a>Sdružování HTTP spojení pomocí HttpClientFactory

I když [HttpClient](/dotnet/api/system.net.http.httpclient) implementuje rozhraní `IDisposable`, je navrženo pro opakované použití. Zavřené instance třídy `HttpClient` zanechávají na krátkou dobu otevřené sockety ve stavu `TIME_WAIT`. Pokud se často používá cesta kódu, která vytvoří a uvolní `HttpClient` objekty, může aplikace vyčerpat dostupné sokety. Jako řešení tohoto problému byla v ASP.NET Core 2.1 zavedena třída [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests), která se stará o sdružování HTTP spojení za účelem optimalizace výkonu a spolehlivosti.

Doporučit

* **Nedělejte:** Nevytvářejte a nerušte instance `HttpClient` napřímo.
* **Dělejte:** Načítejte instance `HttpClient` pomocí [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests). Další informace najdete v tématu [Použití HttpClientFactory k implementaci odolných HTTP požadavků](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Udržujte často volaný kód rychlý

Chcete, aby veškerý kód byl rychlý, často označované jako cesty kódu jsou nejdůležitější pro optimalizaci:

* Komponenty middleware v průběhu zpracování požadavků aplikace - zejména ty, které se spouští v raných fázích. Tyto součásti mají velký dopad na výkon.
* Kód, který se spustí pro každý požadavek nebo vícekrát na požadavek. Například vlastní protokolování, obslužné rutiny autorizace nebo inicializace přechodných služeb.

Doporučit

* **Nedělejte:** Nepoužívejte vlastní komponenty middleware obsahující dlouho běžící úlohy.
* **Pomocí nástrojů** pro profilaci výkonu, jako je [Visual Studio diagnostické nástroje](/visualstudio/profiling/profiling-feature-tour) nebo [PerfView](https://github.com/Microsoft/perfview)), identifikujte [cesty k horkému kódu](#understand-hot-code-paths).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Dokončení dlouho běžící úlohy mimo požadavky HTTP

Většina požadavků na aplikace ASP.NET Core může být zpracována controllerem nebo modelem stránky pomocí volání nezbytných služeb a vrácení HTTP odpovědi. U některých požadavků, které se týkají dlouho běžících úloh, je ale lepší udělat celý proces požadavek-odpověď asynchronní.

Doporučit

* **Nedělejte:** Nečekejte na dokončení dlouho běžících úloh jako běžnou součást zpracování HTTP požadavku.
* **Dělejte:** Vezměte v úvahu zpracování dlouhodobých požadavků pomocí [služeb na pozadí](xref:fundamentals/host/hosted-services) nebo úplně mimo proces za použití [Azure Functions](/azure/azure-functions/). Dokončení práce mimo proces je obzvláště užitečné pro úlohy náročné na CPU.
* **Používejte možnosti** komunikace v reálném čase, například [SignalR](xref:signalr/introduction), ke komunikaci s klienty asynchronně.

## <a name="minify-client-assets"></a>Minifikace prostředků klienta

Aplikace ASP.NET Core s komplexními front-endy často posílají klientům mnoho souborů JavaScript, CSS nebo obrázků. Výkon počátečních požadavků je možné zvýšit pomocí:

* Sdružování, které kombinuje několik souborů do jednoho.
* Minifikace, která zmenšuje velikost souborů odebráním prázdných znaků a komentářů.

Doporučit

* **Dělejte:** Použijte [integrovanou podporu](xref:client-side/bundling-and-minification) ASP.NET Core pro sdružování a minifikaci prostředků klienta.
* Pro komplexní správu prostředků **klienta zvažte další** nástroje třetích stran, jako je například [Webpack](https://webpack.js.org/).

## <a name="compress-responses"></a>Komprese odpovědí

 Odezvu aplikace obvykle zlepšuje zmenšení velikosti odpovědi, a to často výrazně. Jedním ze způsobů zmenšení velikosti datové části je komprese odpovědí vaší aplikace. Další informace najdete v tématu [komprese odpovědí](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Použít nejnovější verzi ASP.NET Core

Každé nové vydání ASP.NET Core zahrnuje vylepšení výkonu. Optimalizace v .NET Core a ASP.NET Core znamenají, že novější verze obecně překoná starší verze. Například rozhraní .NET Core 2,1 přidalo podporu kompilovaných regulárních výrazů a benefitted z [rozsahu >\<t](https://msdn.microsoft.com/magazine/mt814808.aspx). Verze ASP.NET Core 2.2 přidala podporu HTTP/2. [ASP.NET Core 3,0 přidává mnoho vylepšení](xref:aspnetcore-3.0) , která omezují využití paměti a zvyšují propustnost. Pokud je výkon prioritou, zvažte upgrade na aktuální verzi ASP.NET Core.

## <a name="minimize-exceptions"></a>Omezení počtu výjimek

Výjimky by měly být vzácné. V porovnání s ostatními typy kódu je vyvolávání a zachycování výjimek pomalé, Z tohoto důvodu by se výjimky neměly používat k řízení normálního toku programu.

Doporučit

* **Nepoužívejte vyvolání** nebo zachycení výjimek jako způsob normálního toku programu, zejména v případě [aktivních cest kódu](#understand-hot-code-paths).
* **Dělejte:** Zahrťne v aplikaci logiku, která detekuje a řeší podmínky, které by jinak způsobily výjimku.
* **Dělejte:** Používejte operaci throw a catch výjimky pro neobvyklé a neočekávané situace.

Nástroje pro diagnostiku aplikací, jako je například Application Insights, můžou pomáhat identifikovat běžné výjimky v aplikaci, která může mít vliv na výkon.

## <a name="performance-and-reliability"></a>Výkon a spolehlivost

V následujících částech najdete tipy ke zvýšení výkonu a známé problémy s spolehlivostí a řešení.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Vyhněte se synchronnímu čtení nebo zápisu na HttpRequest/HttpResponse tělo

Všechny vstupně-výstupní operace ASP.NET Core jsou asynchronní. Servery implementují rozhraní `Stream`, které má synchronní i asynchronní přetížení. Asynchronní objekty by měly být upřednostňovány, aby se zabránilo blokování vláken fondu vláken. Blokování vláken může vést k vyčerpání fondu vláken.

**Neprovádět tyto akce:** Následující příklad používá <xref:System.IO.StreamReader.ReadToEnd*>. Zablokuje aktuální vlákno, aby čekal na výsledek. Toto je příklad [synchronizace přes Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
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

Předchozí kód asynchronně deserializace tělo požadavku do C# objektu.

## <a name="prefer-readformasync-over-requestform"></a>Preferovat ReadFormAsync přes Request. Form

Použití `HttpContext.Request.ReadFormAsync` místo `HttpContext.Request.Form`.
`HttpContext.Request.Form` lze bezpečně číst pouze s následujícími podmínkami:

* Formulář byl přečten voláním `ReadFormAsync`a
* Hodnota formuláře v mezipaměti je načítána pomocí `HttpContext.Request.Form`

**Neprovádět tyto akce:** Následující příklad používá `HttpContext.Request.Form`.  `HttpContext.Request.Form` používá [synchronizaci přes Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) a může vést k vyčerpání fondu vláken.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Postupujte takto:** Následující příklad používá `HttpContext.Request.ReadFormAsync` k asynchronnímu čtení těla formuláře.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Vyhněte se čtení velkých těla žádostí nebo těla reakcí do paměti

V rozhraní .NET končí každá alokace objektu větší než 85 KB v haldě velkých objektů ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Velké objekty jsou nákladné dvěma způsoby:

* Náklady na přidělení jsou vysoké, protože paměť pro nově přidělený velký objekt musí být smazána. Modul CLR garantuje, že paměť pro všechny nově přidělené objekty jsou vymazány.
* LOH se shromáždí se zbytkem haldy. LOH vyžaduje úplné [uvolňování paměti](/dotnet/standard/garbage-collection/fundamentals) nebo [kolekci Gen2](/dotnet/standard/garbage-collection/fundamentals#generations).

Tento [Blogový příspěvek](https://adamsitnik.com/Array-Pool/#the-problem) popisuje stručně problém:

> Když je přidělen velký objekt, je označen jako objekt Gen 2. Nejedná se o gen 0 jako u malých objektů. Důsledkem je, že pokud vyčerpáte paměť v LOH, UVOLŇOVÁNí paměti vyčistí celou spravovanou haldu, nejen LOH. Proto vyčistí obecné 0, obecné 1 a obecné 2 včetně LOH. Nazývá se to úplné uvolňování paměti a je to nejvíce časově náročné uvolňování paměti. U mnoha aplikací může být přijatelné. Ale pro vysoce výkonné webové servery, u kterých je potřeba pár paměťových vyrovnávacích pamětí potřebných ke zpracování průměrného webového požadavku (čtení z soketu, dekomprese, dekódování JSON & více), ale opravdu ne.

Naively uložení velkého textu žádosti nebo odpovědi do jednoho `byte[]` nebo `string`:

* Může způsobit, že dojde k rychlému vyzkoušení volného místa v LOH.
* Může způsobit problémy s výkonem aplikace z důvodu úplného GC běhu.

## <a name="working-with-a-synchronous-data-processing-api"></a>Práce s rozhraním API pro synchronní zpracování dat

Při použití serializátoru/zrušení serializátoru, který podporuje pouze synchronní čtení a zápisy (například [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Ukládat data do vyrovnávací paměti asynchronně předtím, než je předáte do serializátoru/zrušení serializátoru.

> [!WARNING]
> Pokud je žádost velká, může to vést k nedostatku paměti (OOM). OOM může mít za následek odepření služby.  Další informace najdete v tématu [zamezení čtení velkých těla žádostí nebo těla reakcí do paměti](#arlb) v tomto dokumentu.

ASP.NET Core 3,0 používá ve výchozím nastavení <xref:System.Text.Json> pro serializaci JSON. <xref:System.Text.Json>:

* Čte a zapisuje JSON asynchronně.
* Je optimalizován pro text v kódování UTF-8.
* Obvykle je vyšší výkon než `Newtonsoft.Json`.

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Do pole neukládejte IHttpContextAccessor. HttpContext.

[IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) vrací `HttpContext` aktivní žádosti při přístupu z vlákna požadavku. `IHttpContextAccessor.HttpContext` **by neměl** být uložen v poli nebo proměnné.

**Neprovádět tyto akce:** Následující příklad uloží `HttpContext` do pole a pak se pokusí o pozdější použití.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Předchozí kód často zachycuje hodnotu null nebo nesprávnou `HttpContext` v konstruktoru.

**Postupujte takto:** Následující příklad:

* Uloží <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> do pole.
* Používá pole `HttpContext` ve správné době a kontroluje `null`.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Nepřistupovat k HttpContext z více vláken

`HttpContext` není bezpečný pro přístup *z více vláken* . Přístup k `HttpContext` z více vláken paralelně může způsobit nedefinované chování, jako je například zablokování, zhroucení a poškození dat.

**Neprovádět tyto akce:** Následující příklad vytvoří tři paralelní požadavky a zaznamená příchozí cestu požadavku před a za odchozí požadavek HTTP. Cesta k požadavku je k dispozici z více vláken, potenciálně paralelně.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Postupujte takto:** Následující příklad kopíruje všechna data z příchozího požadavku a teprve potom provádí tři paralelní požadavky.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Nepoužívat HttpContext po dokončení žádosti

`HttpContext` je platná, pouze pokud je v kanálu ASP.NET Core aktivní požadavek HTTP. Celý kanál ASP.NET Core je asynchronní řetěz delegátů, který provádí všechny požadavky. Až se `Task` vrátí z tohoto řetězce, `HttpContext` se recykluje.

**Neprovádět tyto akce:** V následujícím příkladu se používá `async void`, který po dosažení prvního `await` dokončí požadavek HTTP:

* Což je **vždy** špatný postup v aplikacích ASP.NET Core.
* Po dokončení požadavku HTTP přistupuje k `HttpResponse`.
* Dojde k chybě procesu.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Postupujte takto:** Následující příklad vrátí `Task` do rozhraní, takže požadavek HTTP nebude dokončen, dokud se akce nedokončí.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Nezachytit vlastnost HttpContext v vláknech na pozadí

**Neprovádět tyto akce:** Následující příklad ukazuje, že uzavření zachytí `HttpContext` z vlastnosti `Controller`. Toto je špatný postup, protože pracovní položka by mohla:

* Spustit mimo rozsah požadavku.
* Došlo k pokusu o čtení chybného `HttpContext`.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Postupujte takto:** Následující příklad:

* Zkopíruje data potřebná v úloze na pozadí během žádosti.
* Neodkazuje na cokoli z kontroleru.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Úlohy na pozadí by se měly implementovat jako hostované služby. Další informace najdete v tématu [úlohy na pozadí s hostovanými službami](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Nezachytávat služby vložené do řadičů v vláknech na pozadí

**Neprovádět tyto akce:** Následující příklad ukazuje, že uzavření zachytí `DbContext` z parametru `Controller` akce. Toto je špatný postup.  Pracovní položka by mohla běžet mimo rozsah požadavku. `ContosoDbContext` je vymezen na žádost, což vede k `ObjectDisposedException`.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Postupujte takto:** Následující příklad:

* Vloží <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>, aby bylo možné vytvořit obor v pracovní položce na pozadí. `IServiceScopeFactory` je typu singleton.
* Vytvoří nový rozsah vkládání závislostí ve vlákně na pozadí.
* Neodkazuje na cokoli z kontroleru.
* Nezachycuje `ContosoDbContext` z příchozího požadavku.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Následující zvýrazněný kód:

* Vytvoří obor pro dobu života operace na pozadí a vyřeší z něj služby.
* Používá `ContosoDbContext` ze správného oboru.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Neměňte stavový kód ani hlavičky po zahájení textu odpovědi

ASP.NET Core neukládá obsah odpovědi HTTP do vyrovnávací paměti. Při prvním zápisu odpovědi:

* Hlavičky jsou odesílány spolu s tímto blokem těla do klienta.
* Již není možné měnit hlavičky odpovědí.

**Neprovádět tyto akce:** Následující kód se pokusí přidat hlavičky odpovědi poté, co byla odpověď již spuštěna:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

V předchozím kódu `context.Response.Headers["test"] = "test value";` vyvolá výjimku, pokud `next()` zapsána do odpovědi.

**Postupujte takto:** Následující příklad zkontroluje, zda byla před úpravou hlaviček spuštěna odpověď protokolu HTTP.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Postupujte takto:** Následující příklad používá `HttpResponse.OnStarting` k nastavení hlaviček před vyprázdněním hlaviček odpovědí na klienta.

Kontrola, zda odpověď nezačala, umožňuje registraci zpětného volání, které bude vyvoláno těsně před zápisem hlaviček odpovědí. Kontroluje se, jestli odpověď nezačala:

* Poskytuje možnost připojit nebo přepsat hlavičky v čase.
* Nevyžaduje znalosti dalšího middlewaru v kanálu.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Nevolejte Next (), pokud jste již začali zapisovat do těla odpovědi

Součásti, které mají být volány, jsou očekávány pouze v případě, že je možné je zpracovat a manipulovat s ní.
