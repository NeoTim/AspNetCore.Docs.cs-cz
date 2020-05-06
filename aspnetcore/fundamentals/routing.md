---
title: Směrování v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ASP.NET Core směrování zodpovídá za odpovídající požadavky HTTP a odesílání do spustitelných koncových bodů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/routing
ms.openlocfilehash: e2b1672066a5b3c0bb6bc44e316bda93ae0f21b7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774900"
---
# <a name="routing-in-aspnet-core"></a>Směrování v ASP.NET Core

Služba [Ryan Nowak](https://github.com/rynowak), [Kirka Larkin](https://twitter.com/serpent5)a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Směrování zodpovídá za požadavky na příchozí HTTP a odesílání těchto požadavků do spustitelných koncových bodů aplikace. [Koncové body](#endpoint) jsou jednotky aplikace spustitelného kódu pro zpracování požadavků. Koncové body jsou v aplikaci definované a nakonfigurované při spuštění aplikace. Proces pro porovnání koncových bodů může extrahovat hodnoty z adresy URL požadavku a poskytnout tyto hodnoty pro zpracování požadavků. Směrování pomocí informací o koncových bodech z aplikace taky umožňuje generovat adresy URL, které se mapují na koncové body.

Aplikace můžou konfigurovat směrování pomocí:

- Kontrolery
- Razor Pages
- SignalR
- Služby gRPC
- [Middleware](xref:fundamentals/middleware/index) s povoleným koncovým bodem, například [kontroly stavu](xref:host-and-deploy/health-checks).
- Delegáti a výrazy lambda zaregistrované ve směrování.

Tento dokument popisuje podrobnosti nízké úrovně směrování ASP.NET Core. Informace o konfiguraci směrování:

* Řadiče najdete v tématu <xref:mvc/controllers/routing>.
* Razor Pages konvence naleznete v <xref:razor-pages/razor-pages-conventions>tématu.

Systém směrování koncových bodů popsaný v tomto dokumentu se týká ASP.NET Core 3,0 a novějších. Informace o předchozím systému směrování na základě <xref:Microsoft.AspNetCore.Routing.IRouter>nástroje vyberte verzi ASP.NET Core 2,1 s jedním z následujících přístupů:

* Selektor verzí pro předchozí verzi.
* Vyberte [směrování ASP.NET Core 2,1](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázky stahování pro tento dokument jsou povoleny konkrétní `Startup` třídou. Chcete-li spustit konkrétní ukázku, upravte *program.cs* a zavolejte požadovanou `Startup` třídu.

## <a name="routing-basics"></a>Základy směrování

Všechny šablony ASP.NET Core zahrnují směrování ve vygenerovaném kódu. Směrování je zaregistrované v kanálu [middleware](xref:fundamentals/middleware/index) v `Startup.Configure`.

Následující kód ukazuje základní příklad směrování:

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Směrování používá dvojici middlewaru, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> zaregistrovaných <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>pomocí a:

* `UseRouting`Přidá směrování do kanálu middlewaru. Tento middleware prohlíží sadu koncových bodů definovaných v aplikaci a vybere [nejlepší shodu](#urlm) na základě požadavku.
* `UseEndpoints`Přidá spuštění koncového bodu do kanálu middlewaru. Spustí delegáta spojený s vybraným koncovým bodem.

Předchozí příklad obsahuje jednu *trasu ke* koncovému bodu kódu pomocí metody [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) :

* Při odeslání požadavku `GET` http na kořenovou adresu URL `/`:
  * Spustí se delegát žádosti.
  * `Hello World!`se zapisuje do odpovědi HTTP. Ve výchozím nastavení je `/` `https://localhost:5001/`kořenová adresa URL.
* Pokud metoda Request není `GET` nebo kořenová adresa URL není `/`, neodpovídají žádné trasy a je vrácen protokol HTTP 404.

### <a name="endpoint"></a>Koncový bod

<a name="endpoint"></a>

`MapGet` Metoda se používá k definování **koncového bodu**. Koncový bod je něco, co může být:

* Vybráno tak, že odpovídá adrese URL a metodě HTTP.
* Provedeno spuštěním delegáta.

Koncové body, které se dají spárovat a spustí aplikace, se konfigurují `UseEndpoints`v. Například <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*> <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>,, a [podobné metody](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) spojují delegáty žádostí do směrovacího systému.
Další metody lze použít k připojení funkcí ASP.NET Core Framework k systému směrování:
- [MapRazorPages pro Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [MapControllers pro řadiče](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub\<THub> pro signál](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService\<TService> pro gRPC](xref:grpc/aspnetcore)

Následující příklad ukazuje směrování s propracovanější šablonou směrování:

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

Řetězec `/hello/{name:alpha}` je **šablona trasy**. Slouží ke konfiguraci způsobu párování koncového bodu. V tomto případě šablona odpovídá:

* Adresa URL jako`/hello/Ryan`
* Libovolná cesta URL začínající `/hello/` posloupností abecedních znaků.  `:alpha`použije omezení trasy, které odpovídá pouze abecedním znakům. [Omezení trasy](#route-constraint-reference) jsou vysvětleny dále v tomto dokumentu.

Druhý segment cesty URL `{name:alpha}`:

* Je svázán s `name` parametrem.
* Je zachycen a uložen v [HttpRequest. RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).

Systém směrování koncových bodů, který je popsaný v tomto dokumentu, je nový od ASP.NET Core 3,0. Všechny verze ASP.NET Core ale podporují stejnou sadu funkcí šablon směrování a omezení tras.

Následující příklad ukazuje směrování s [kontrolami stavu](xref:host-and-deploy/health-checks) a autorizací:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Předchozí příklad ukazuje, jak:

* Middleware autorizace se dá použít spolu s směrováním.
* Koncové body lze použít ke konfiguraci chování autorizace.

<xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> Volání přidá koncový bod kontroly stavu. <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> Řetězení k tomuto volání připojí zásadu autorizace ke koncovému bodu.

Volání <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> a <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> přidává middleware pro ověřování a autorizaci. Tyto middleware jsou umístěné <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> mezi `UseEndpoints` a tak, aby mohly:

* Podívejte se, podle `UseRouting`kterého koncového bodu byl vybrán.
* Před <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> odesláním do koncového bodu použijte zásady autorizace.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Metadata koncového bodu

V předchozím příkladu jsou k dispozici dva koncové body, ale pouze koncový bod kontroly stavu má připojené zásady autorizace. Pokud požadavek odpovídá koncovému bodu `/healthz`kontroly stavu, provede se ověření autorizace. To ukazuje, že koncovým bodům můžou být připojená další data. Tato další data se nazývají **metadata**koncového bodu:

* Metadata mohou být zpracována middlewarem s podporou směrování.
* Metadata můžou být libovolného typu .NET.

## <a name="routing-concepts"></a>Koncepty směrování

Systém směrování sestaví nad kanálem middlewaru přidáním výkonného konceptu **koncových bodů** . Koncové body představují jednotky funkcí aplikace, které se od sebe liší v souvislosti s směrováním, autorizací a libovolným počtem systémů ASP.NET Core.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>Definice ASP.NET Coreho koncového bodu

ASP.NET Core koncový bod:

* Spustitelný soubor: má <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.
* Rozšiřitelný: má kolekci [metadat](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) .
* Možnost volby: volitelně má [informace o směrování](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).
* Vyčíslitelné: kolekce koncových bodů může být uvedena načtením <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> z [di](xref:fundamentals/dependency-injection).

Následující kód ukazuje, jak načíst a zkontrolovat koncový bod, který odpovídá aktuálnímu požadavku:

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Koncový bod, je-li vybrán, lze načíst z `HttpContext`. Lze zkontrolovat jeho vlastnosti. Objekty koncového bodu jsou neměnné a po vytvoření je nelze změnit. Nejběžnějším typem koncového bodu je <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>. `RouteEndpoint`obsahuje informace, které umožňují, aby bylo možné je vybrat v systému směrování.

V předchozím kódu [aplikace. Použijte](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) konfiguraci vloženého [middlewaru](xref:fundamentals/middleware/index).

<a name="mt"></a>

Následující kód ukazuje, že v závislosti na tom, `app.Use` kde je volána v kanálu, nemusí být koncový bod:

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

V předchozím příkladu jsou `Console.WriteLine` přidány příkazy, které zobrazují, zda byl vybrán koncový bod. Pro přehlednost ukázka přiřadí zobrazovaný název k poskytnutému `/` koncovému bodu.

Spuštění tohoto kódu s adresou URL pro `/` zobrazení:

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Spuštění tohoto kódu se všemi ostatními adresami URL zobrazuje:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Tento výstup ukazuje, že:

* Koncový bod je před `UseRouting` voláním vždy null.
* Pokud je nalezena shoda, koncový bod nemá hodnotu null mezi `UseRouting` a. <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>
* `UseEndpoints` Middleware je **terminálem** , když je nalezena shoda. [Middleware terminálu terminálu](#tm) je definována dále v tomto dokumentu.
* Middleware po `UseEndpoints` spuštění pouze v případě, že se nenajde žádná shoda.

`UseRouting` Middleware používá metodu [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) pro připojení koncového bodu k aktuálnímu kontextu. Je možné nahradit `UseRouting` middleware vlastní logikou a stále využívat výhody použití koncových bodů. Koncové body jsou primitivní základní, jako middleware, a nejsou spojeny s implementací směrování. Většina aplikací se nemusí nahradit `UseRouting` vlastní logikou.

`UseEndpoints` Middleware je navržená tak, aby se mohla používat `UseRouting` společně s middlewarem. Základní logika pro spuštění koncového bodu není složitá. Použijte <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> k načtení koncového bodu a poté jeho <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> vlastnost vyvolejte.

Následující kód ukazuje, jak middleware může ovlivnit směrování nebo reagovat na něj:

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

Předchozí příklad ukazuje dva důležité koncepty:

* Middleware může běžet `UseRouting` před změnou dat, na kterých funguje směrování.
    * Middleware, které se zobrazují před směrováním, mění určitou vlastnost žádosti, <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>jako <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>je například <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>, nebo.
* Middleware může běžet `UseRouting` mezi <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> a ke zpracování výsledků směrování před provedením koncového bodu.
    * Middleware spouštěné `UseRouting` mezi `UseEndpoints`a:
      * Obvykle kontroluje metadata pro pochopení koncových bodů.
      * Často provádí rozhodnutí o zabezpečení, jak to `UseAuthorization` dělá `UseCors`a.
    * Kombinace middlewaru a metadat umožňuje konfigurovat zásady na koncový bod.

Předchozí kód ukazuje příklad vlastního middlewaru, který podporuje zásady pro jednotlivé koncové body. Middleware zapisuje *protokol auditu* přístupu k citlivým datům do konzoly. Middleware je možné nakonfigurovat pro *audit* koncového bodu s `AuditPolicyAttribute` metadaty. Tato ukázka předvádí vzor *výslovných* přihlášení, kde jsou auditovány pouze koncové body označené jako citlivé. Tuto logiku je možné definovat obráceně a auditovat vše, co není označeno jako bezpečné, například. Systém metadat koncového bodu je flexibilní. Tato logika by mohla být navržena jakýmkoli způsobem, který odpovídá případu použití.

Předchozí vzorový kód je určen k předvedení základních konceptů koncových bodů. **Ukázka není určena pro použití v produkčním**prostředí. Ucelená verze middlewaru *protokolu auditu* by mohla:

* Přihlaste se k souboru nebo databázi.
* Uveďte podrobnosti, jako je uživatel, IP adresa, název citlivého koncového bodu a další.

Metadata `AuditPolicyAttribute` zásad auditu se definují jako `Attribute` pro snazší použití s platformami založenými na třídách, jako jsou řadiče a signály. Při použití *směrování na kód*:

* K rozhraní API tvůrce se připojují metadata.
* Rozhraní založená na třídě zahrnují všechny atributy odpovídající metody a třídy při vytváření koncových bodů.

Osvědčené postupy pro typy metadat jsou jejich definování buď jako rozhraní, nebo jako atributy. Rozhraní a atributy umožňují opakované použití kódu. Systém metadat je flexibilní a nezavádí žádná omezení.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Porovnání middlewaru a směrování terminálu

Následující ukázka kódu kontrastuje pomocí middlewaru s použitím směrování:

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

Styl middleware zobrazený v `Approach 1:` nástroji je **middleware terminálu**. Nazývá middleware terminálu, protože se jedná o shodnou operaci:

* Operace porovnání v předchozím příkladu je určena `Path == "/"` pro middleware a `Path == "/Movie"` pro směrování.
* Po úspěšné shodě se spustí některé funkce a vrátí místo vyvolání `next` middlewaru.

Nazývá middleware terminálu, protože ukončí hledání, spustí některé funkce a pak vrátí.

Porovnání middlewaru a směrování terminálu:
* Oba přístupy umožňují ukončení kanálu zpracování:
    * Middleware ukončí kanál vrácením místo vyvolání `next`.
    * Koncové body jsou vždycky Terminálové.
* Middleware terminálu umožňuje umístění middlewaru na libovolné místo v kanálu:
    * Koncové body jsou spouštěny v <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>pozici.
* Middleware terminálu umožňuje libovolnému kódu určit, kdy se middleware shoduje:
    * Kód pro přizpůsobení vlastní trasy může být podrobný a obtížně zapisovat.
    * Směrování poskytuje jednoduchá řešení pro běžné aplikace. Většina aplikací nevyžaduje kód pro přizpůsobení vlastní trasy.
* Rozhraní koncových bodů s middlewarem, jako je `UseAuthorization` a `UseCors`.
    * Použití middleware terminálu pro `UseAuthorization` nebo `UseCors` vyžaduje ruční propojení s autorizačním systémem.

[Koncový bod](#endpoint) definuje:

* Delegát pro zpracování požadavků.
* Kolekce libovolných metadat Metadata se používají k implementaci průřezových obav na základě zásad a konfigurace připojených ke každému koncovému bodu.

Middleware terminálu může být účinný nástroj, ale může vyžadovat:

* Významné množství kódu a testování.
* Ruční integrace s jinými systémy pro dosažení požadované úrovně flexibility.

Před zápisem middleware terminálu zvažte integraci se směrováním.

Existující middleware terminálu, který se [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) integruje <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> s mapou, nebo se obvykle může přepínat na koncový bod podporující směrování. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) ukazuje vzor pro router:
* Zápis metody rozšíření na <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.
* Vytvořte vnořený kanál middlewaru <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>pomocí.
* Připojte middleware k novému kanálu. V tomto případě <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>kanál middlewaru do <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Zavolejte `Map` a poskytněte nový kanál middlewaru.
* Vrátí objekt tvůrce poskytnutý `Map` z metody rozšíření.

Následující kód ukazuje použití [MapHealthChecks](xref:host-and-deploy/health-checks):

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

Předchozí příklad ukazuje, proč je důležité vracet objekt tvůrce. Vrácení objektu Tvůrce umožňuje vývojářům aplikací nakonfigurovat zásady, jako je například autorizace pro koncový bod. V tomto příkladu middleware pro kontrolu stavu nemá přímou integraci s autorizačním systémem.

Systém metadat byl vytvořen v reakci na problémy zjištěné rozšířením autoři pomocí middlewaru terminálu. U každého middlewaru je problematické implementovat svou vlastní integraci s autorizačním systémem.

<a name="urlm"></a>

### <a name="url-matching"></a>Shoda adresy URL

* Je proces, podle kterého směrování odpovídá příchozímu požadavku na [koncový bod](#endpoint).
* Je založena na datech v cestě a hlavičkách URL.
* Dá se rozšířit tak, aby v žádosti mohla být považovat všechna data.

Když middleware směrování spustí, nastaví v rámci `Endpoint` <xref:Microsoft.AspNetCore.Http.HttpContext> aktuální žádosti hodnoty a směrování na [funkci Request](xref:fundamentals/request-features) .

* Volání [HttpContext. GetEndPoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) získá koncový bod.
* `HttpRequest.RouteValues`Získá kolekci hodnot tras.

[Middleware](xref:fundamentals/middleware/index) spuštěný poté, co middleware směrování může zkontrolovat koncový bod a provést akci. Middleware autorizace může například dotazování kolekci metadat koncového bodu pro zásadu autorizace. Po spuštění všech middlewarů v kanálu zpracování požadavků je vyvolán delegát vybraného koncového bodu.

Systém směrování v rámci směrování koncových bodů zodpovídá za všechna rozhodnutí o odesílání. Vzhledem k tomu, že middleware používá zásady na základě vybraného koncového bodu, je důležité, aby:

* Jakékoli rozhodnutí, které může ovlivnit odesílání nebo použití zásad zabezpečení, se provádí v rámci systému směrování.

> [!WARNING]
> Pro zpětnou kompatibilitu, když se spustí řadič nebo Razor Pages delegát koncového bodu, se vlastnosti [RouteContext. parametr RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) nastaví na vhodné hodnoty na základě dosud provedeného zpracování požadavků.
>
> `RouteContext` Typ bude v budoucí verzi označen jako zastaralý:
>
> * `RouteData.Values` Migrujte `HttpRequest.RouteValues`na.
> * Migrujte `RouteData.DataTokens` pro načtení [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) z metadat koncového bodu.

Shoda adresy URL funguje v konfigurovatelné sadě fází. V každé fázi je výstupem sada shod. Množinu shody lze v další fázi zúžit. Implementace směrování nezaručuje pořadí zpracování pro porovnání koncových bodů. **Všechny** možné shody jsou zpracovávány současně. V následujícím pořadí se shodují tyto fáze adresy URL. ASP.NET Core:

1. Zpracuje cestu URL proti sadě koncových bodů a jejich šablonám směrování a shromažďují **všechny** shody.
1. Převezme předchozí seznam a odstraní shody, které selžou s použitými omezeními směrování.
1. Převezme předchozí seznam a odstraní shody, které selžou sadu instancí [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) .
1. Použije [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) k nastavení konečného rozhodnutí z předchozího seznamu.

Seznam koncových bodů se stanovuje podle priorit:

* [RouteEndpoint. Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* [Priorita šablony trasy](#rtp)

Všechny vyhovující koncové body jsou zpracovávány v každé fázi <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> až do chvíle, kdy je dosaženo. `EndpointSelector` Je finální fází. Zvolí koncový bod nejvyšší priority z odpovídajících shod jako nejlepší shody. Pokud existují jiné shody se stejnou prioritou, jako je nejlepší shoda, je vyvolána výjimka nejednoznačná shoda.

Priorita trasy je vypočítána na základě **konkrétnější** šablony trasy, která má vyšší prioritu. Zvažte například šablony `/hello` a `/{message}`:

* Obě adresy odpovídají cestě `/hello`URL.
* `/hello`je konkrétnější a proto má vyšší prioritu.

Obecně platí, že priorita trasy má dobrou úlohu při výběru nejlepší shody pro typy schémat adres URL používaných v praxi. Použijte <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> pouze v případě potřeby, aby nedocházelo k nejednoznačnosti.

V důsledku druhů rozšiřitelnosti poskytovaných směrováním není možné, aby systém směrování vypočítal předem nejednoznačné trasy. Vezměte v úvahu příklad, jako jsou například `/{message:alpha}` šablony `/{message:int}`směrování a:

* `alpha` Omezení odpovídá pouze abecedním znakům.
* `int` Omezení odpovídá pouze číslům.
* Tyto šablony mají stejnou prioritu trasy, ale žádná jediná adresa URL se shodují.
* Pokud systém směrování ohlásil při spuštění chybu nejednoznačnosti, zablokuje tento platný případ použití.

> [!WARNING]
>
> Pořadí operací uvnitř <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> nemá vliv na chování směrování s jednou výjimkou. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>a <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> automaticky přiřazují hodnotu objednávky svým koncovým bodům podle pořadí, ve kterém jsou vyvolány. To simuluje dlouhodobé chování řadičů bez směrovacího systému, který poskytuje stejné záruky jako u starších implementací směrování.
>
> Ve starší implementaci směrování je možné implementovat rozšiřitelnost směrování, která má závislost v pořadí, ve kterém jsou trasy zpracovávány. Směrování koncových bodů v ASP.NET Core 3,0 a novějším:
> 
> * Nemá koncept tras.
> * Neposkytuje záruky řazení. Všechny koncové body jsou zpracovávány současně.
>
> Pokud to znamená, že jste se zablokovali pomocí staršího směrovacího systému, [otevřete pro pomoc problém GitHubu](https://github.com/dotnet/aspnetcore/issues).

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Priorita šablony směrování a pořadí výběru koncového bodu

[Priorita šablony směrování](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) je systém, který přiřazuje každou šablonu směrování hodnotu podle toho, jak je specifická. Priorita šablony směrování:

* Vyhněte se nutnosti upravovat pořadí koncových bodů v běžných případech.
* Pokusy o shodu se společnými očekáváními chování směrování.

Zvažte například šablony `/Products/List` a `/Products/{id}`. Je vhodné předpokládat, že se jedná `/Products/List` o lepší shodu, `/Products/{id}` než pro cestu `/Products/List`URL. Funguje, protože literální segment `/List` je považován za lepší prioritu než segment `/{id}`parametru.

Podrobnosti o tom, jak priorita funguje, je spojena s tím, jak jsou definovány šablony směrování:

* Šablony s více segmenty jsou považovány za konkrétnější.
* Segment s textovým literálem je považován za konkrétnější než segment parametru.
* Segment parametru s omezením je považován za konkrétnější než bez.
* Složitý segment je považován za specifický jako segment parametru s omezením.
* Catch – všechny parametry jsou nejméně specifické. Důležité informace o catch – všechny trasy najdete v referenčních informacích k [šabloně trasy](#rtr) v tématu **catch-All** .

Odkaz na přesné hodnoty najdete na [zdrojovém kódu na GitHubu](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) .

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Koncepty generování adresy URL

Generování adresy URL:

* Je proces, podle kterého směrování může vytvořit cestu adresy URL na základě sady hodnot tras.
* Umožňuje logické oddělení mezi koncovými body a adresami URL, které k nim mají přístup.

Směrování koncového bodu <xref:Microsoft.AspNetCore.Routing.LinkGenerator> zahrnuje rozhraní API. `LinkGenerator`je služba typu Singleton dostupná z [di](xref:fundamentals/dependency-injection). `LinkGenerator` Rozhraní API lze použít mimo kontext vykonávajícího požadavku. [MVC. IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) a scénáře, které spoléhají <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>na, jako jsou například [pomocníky značek](xref:mvc/views/tag-helpers/intro), HTML helps a [výsledky akcí](xref:mvc/controllers/actions), používají `LinkGenerator` rozhraní API interně k poskytování možností vytváření odkazů.

Generátor propojení se zálohuje konceptem **adres** a **schémat adres**. Schéma adres je způsob, jak určit koncové body, které by měly být považovány za vytváření odkazů. Například název trasy a hodnoty tras vycházejí z řadičů o mnoho uživatelů a Razor Pages jsou implementovány jako schéma adres.

Generátor propojení se může připojit k řadičům a Razor Pages prostřednictvím následujících rozšiřujících metod:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Přetížení těchto metod přijímají argumenty, které obsahují `HttpContext`. Tyto metody jsou funkčně ekvivalentní k [adrese URL. Action](xref:System.Web.Mvc.UrlHelper.Action*) a [URL. Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), ale nabízejí další flexibilitu a možnosti.

`GetPath*` Metody jsou nejvíce podobné `Url.Action` a `Url.Page`, v tom, že generují identifikátor URI obsahující absolutní cestu. `GetUri*` Metody vždy generují absolutní identifikátor URI obsahující schéma a hostitele. Metody, které přijímají `HttpContext` identifikátor URI v kontextu zpracovávaného požadavku. Použijí [se hodnoty tras,](#ambient) základní cesta, schéma a hostitel z zpracovávaného požadavku, pokud nejsou přepsány.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>je volána s adresou. K vygenerování identifikátoru URI dochází ve dvou krocích:

1. Adresa je svázána se seznamem koncových bodů, které odpovídají dané adrese.
1. Každý koncový bod <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> je vyhodnocen, dokud se nenajde vzor směrování, který odpovídá zadaným hodnotám. Výsledný výstup je v kombinaci s ostatními částmi identifikátoru URI dodanými generátorem odkazů a vrácenými.

Metody poskytované funkcí <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard pro vytváření odkazů pro jakýkoli typ adresy. Nejpohodlnější způsob použití generátoru odkazů je prostřednictvím metod rozšíření, které provádějí operace pro konkrétní typ adresy:

| Metoda rozšíření | Popis |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Vygeneruje identifikátor URI s absolutní cestou na základě zadaných hodnot. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Vygeneruje absolutní identifikátor URI na základě zadaných hodnot.             |

> [!WARNING]
> Věnujte pozornost následujícím důsledkům volání <xref:Microsoft.AspNetCore.Routing.LinkGenerator> metod:
>
> * Používejte `GetUri*` rozšiřující metody s opatrností v konfiguraci aplikace, která neověřuje `Host` hlavičku příchozích požadavků. Pokud `Host` záhlaví příchozích požadavků není ověřeno, může být nedůvěryhodný vstup žádosti odeslán zpět klientovi v identifikátorech URI v zobrazení nebo na stránce. Doporučujeme, aby všechny produkční aplikace nakonfigurovali server, aby ověřili `Host` hlavičku se známými platnými hodnotami.
>
> * Používejte <xref:Microsoft.AspNetCore.Routing.LinkGenerator> s opatrností v middleware v `Map` kombinaci `MapWhen`s nebo. `Map*`změní základní cestu spouštěné žádosti, která má vliv na výstup vytváření odkazů. Všechna <xref:Microsoft.AspNetCore.Routing.LinkGenerator> rozhraní API umožňují zadat základní cestu. Zadejte prázdnou základní cestu pro vrácení `Map*` vlivu na generování odkazů.

### <a name="middleware-example"></a>Příklad middlewaru

V následujícím příkladu middleware používá <xref:Microsoft.AspNetCore.Routing.LinkGenerator> rozhraní API k vytvoření odkazu na metodu akce, která obsahuje produkty pro ukládání. Použití generátoru odkazů vložením do třídy a volání `GenerateLink` je k dispozici pro libovolnou třídu v aplikaci:

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Odkaz na šablonu směrování

Tokeny `{}` v rámci definice parametrů trasy, které jsou vázány, pokud je trasa shodná. V segmentu směrování lze definovat více než jeden parametr trasy, ale parametry směrování musí být odděleny hodnotou literálu. Například `{controller=Home}{action=Index}` není platná trasa, protože hodnota literálu není mezi `{controller}` a. `{action}`  Parametry směrování musí mít název a můžou mít zadané další atributy.

Textový literál jiný než parametry směrování (například `{id}`) a oddělovač `/` cesty musí odpovídat textu v adrese URL. Porovnávání textu rozlišuje malá a velká písmena, a to na základě dekódovat reprezentace cesty adresy URL. Chcete-li porovnat oddělovač parametrů trasy literálu `{` nebo `}`, vydejte oddělovač opakováním znaku. Například `{{` nebo `}}`.

Hvězdička `*` nebo dvojitá hvězdička `**`:

* Dá se použít jako předpona parametru Route, aby se navázala na zbytek identifikátoru URI.
* Označují se jako **catch-All** Parameters. Například `blog/{**slug}`:
  * Odpovídá jakémukoli identifikátoru URI, `/blog` který začíná a má za sebou libovolnou hodnotu.
  * Hodnota níže `/blog` je přiřazena k hodnotě trasy [popisu](https://developer.mozilla.org/docs/Glossary/Slug) .

[!INCLUDE[](~/includes/catchall.md)]

Catch – všechny parametry můžou odpovídat také prázdnému řetězci.

Parametr catch-All řídí příslušné znaky, pokud je použita cesta pro vygenerování adresy URL, včetně znaků oddělovače `/` cesty. Například trasa `foo/{*path}` s hodnotami `{ path = "my/path" }` trasy vygeneruje. `foo/my%2Fpath` Všimněte si řídicího znaku lomítka. Do oddělovacích znaků cesty pro přenos cest použijte předponu parametru `**` Route. Trasa `foo/{**path}` s `{ path = "my/path" }` vygeneruje `foo/my/path`.

Vzory adres URL, které se pokoušejí zachytit název souboru s volitelnou příponou souboru, mají další požadavky. Představte si třeba šablonu `files/{filename}.{ext?}`. Pokud hodnoty pro obojí `filename` i `ext` existují, naplní se obě hodnoty. Je-li v adrese `filename` URL pouze hodnota, která odpovídá, bude trasa odpovídat, protože `.` koncový objekt je nepovinný. Tuto trasu odpovídají následujícím adresám URL:

* `/files/myFile.txt`
* `/files/myFile`

Parametry směrování můžou mít **výchozí hodnoty** určené zadáním výchozí hodnoty za názvem parametru odděleným symbolem rovná se (`=`). Například `{controller=Home}` definuje `Home` jako výchozí hodnotu pro `controller`. Výchozí hodnota se použije v případě, že v adrese URL parametru není k dispozici žádná hodnota. Parametry směrování jsou povinny připojením otazníku (`?`) na konec názvu parametru. Například, `id?`. Rozdíl mezi volitelnými hodnotami a výchozími parametry směrování:

* Parametr trasy s výchozí hodnotou vždy vytvoří hodnotu.
* Volitelný parametr má hodnotu pouze v případě, že je hodnota poskytnuta adresou URL požadavku.

Parametry směrování můžou mít omezení, která se musí shodovat s hodnotou trasy svázanou z adresy URL. Přidání `:` a omezení názvu za názvem parametru trasy určuje vložené omezení pro parametr trasy. Pokud omezení vyžaduje argumenty, jsou `(...)` po názvu omezení uzavřeny v závorkách. Pomocí připojení jiného `:` a názvu omezení lze zadat více *vložených omezení* .

Název omezení a argumenty jsou předány <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> službě za účelem vytvoření instance <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> pro použití při zpracování adresy URL. Například šablona `blog/{article:minlength(10)}` trasy Určuje `minlength` omezení s argumentem. `10` Další informace o omezeních tras a seznam omezení poskytovaných rozhraním najdete v části [referenční informace k omezením trasy](#route-constraint-reference) .

Parametry směrování můžou mít také transformátory parametrů. Transformátory parametrů transformují hodnotu parametru při generování odkazů a porovnání akcí a stránek s adresami URL. Podobně jako omezení můžou být transformátory parametrů přidány do parametru trasy, `:` a to tak, že po názvu parametru trasy přidáte název a Transformer. Například šablona `blog/{article:slugify}` trasy Určuje `slugify` transformátor. Další informace o transformačních parametrech naleznete v části [Referenční příručka pro parametry](#parameter-transformer-reference) transformátoru.

Následující tabulka ukazuje příklady šablon směrování a jejich chování:

| Šablona směrování                           | Příklad odpovídajícího identifikátoru URI    | Identifikátor URI žádosti&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Odpovídá pouze jedné cestě `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Odpovídá a nastavuje `Page` na `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Odpovídá a nastavuje `Page` na `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Provede mapování na `Products` kontroler `List` a akci.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Provede mapování na `Products` kontroler `Details` a akci`id` s nastavením na 123. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Provede mapování na `Home` kontroler `Index` a metodu. `id` se ignoruje.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Provede mapování na `Products` kontroler `Index` a metodu. `id` se ignoruje.        |

Použití šablony je obecně nejjednodušší přístup ke směrování. Omezení a výchozí hodnoty je možné zadat i mimo šablonu směrování.

### <a name="complex-segments"></a>Komplexní segmenty

Komplexní segmenty jsou zpracovávány porovnáním oddělovačů literálů zprava doleva [nehladým](#greedy) způsobem. Například `[Route("/a{b}c{d}")]` je složitý segment.
Složité segmenty fungují určitým způsobem, který je nutné chápat pro jejich úspěšné použití. Příklad v této části ukazuje, proč složité segmenty skutečně fungují pouze v případě, že se text oddělovače nezobrazuje v hodnotách parametrů. Použití [regulárního výrazu](/dotnet/standard/base-types/regular-expressions) a následného ruční extrakce hodnot je nutné pro složitější případy.

[!INCLUDE[](~/includes/regex.md)]

Toto je souhrn kroků, které směrování provádí se šablonou `/a{b}c{d}` a cestou `/abcd`k adrese URL. `|` Slouží k vizualizaci, jak algoritmus funguje:

* První literál, zprava doleva, je `c`. Proto `/abcd` je prohledáván zprava a hledá `/ab|c|d`.
* Vše napravo (`d`) je nyní spárováno s parametrem `{d}`Route.
* Další literál zprava doleva, je `a`. Proto `/ab|c|d` se prohledá, kde jsme skončili, `a` a pak `/|a|b|c|d`se najde.
* Hodnota vpravo (`b`) je nyní shodná s parametrem `{b}`směrování.
* Není k dispozici žádný zbývající text a žádná šablona směrování, takže se jedná o shodu.

Tady je příklad negativního případu pomocí stejné šablony `/a{b}c{d}` a cesty `/aabcd`URL. `|` Slouží k vizualizaci, jak algoritmus funguje. Tento případ se neshoduje s tím, který je vysvětlen stejným algoritmem:
* První literál, zprava doleva, je `c`. Proto `/aabcd` je prohledáván zprava a hledá `/aab|c|d`.
* Vše napravo (`d`) je nyní spárováno s parametrem `{d}`Route.
* Další literál zprava doleva, je `a`. Proto `/aab|c|d` se prohledá, kde jsme skončili, `a` a pak `/a|a|b|c|d`se najde.
* Hodnota vpravo (`b`) je nyní shodná s parametrem `{b}`směrování.
* V tomto okamžiku se nachází zbývající text `a`, ale algoritmus vyvolal šablonu směrování, která se má analyzovat, takže se nejedná o shodu.

Vzhledem k tomu, že shodný algoritmus není [hladec](#greedy):

* Odpovídá co nejmenšímu množství textu v každém kroku.
* V případě, že se hodnota oddělovače objeví v hodnotách parametrů, se neshodují.

Regulární výrazy poskytují mnohem větší kontrolu nad svými odpovídajícími chováními.

<a name="greedy"></a>

Hladové párování, také jako [opožděné párování](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), odpovídá největšímu možnému řetězci. Nehladec se shoduje s nejmenším možným řetězcem.

## <a name="route-constraint-reference"></a>Odkaz na omezení trasy

Omezení trasy se spustí, když došlo ke shodě s příchozí adresou URL a cesta URL je zavedená do hodnot tras. Omezení tras obvykle kontrolují hodnotu trasy přidruženou prostřednictvím šablony trasy a vyhodnotí rozhodnutí pravdivé nebo nepravdivé, zda je hodnota přijatelná. Některá omezení tras používají data mimo hodnotu trasy k zvážení toho, zda je možné požadavek směrovat. Například <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> může přijmout nebo odmítnout požadavek na základě jeho příkazu http. Omezení se používají při směrování požadavků a vytváření propojení.

> [!WARNING]
> Nepoužívejte omezení pro ověřování vstupu. Pokud jsou pro ověření vstupu použity omezení, neplatné výsledky vstupu v `404` odpovědi nenalezeny. Neplatný vstup by měl vytvořit `400` chybnou žádost s příslušnou chybovou zprávou. Omezení tras slouží k jednoznačnému rozlišení podobných tras, nikoli k ověření vstupů konkrétní trasy.

Následující tabulka ukazuje příklad omezení trasy a jejich očekávané chování:

| omezení | Příklad | Příklady shody | Poznámky |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Odpovídá jakémukoli celému číslu |
| `bool` | `{active:bool}` | `true`, `FALSE` | Odpovídá `true` nebo `false`. Bez rozlišení velkých a malých písmen |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platné `DateTime` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platné `decimal` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platné `double` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platné `float` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Odpovídá platné `Guid` hodnotě |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Odpovídá platné `long` hodnotě |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Řetězec musí mít minimálně 4 znaky. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Řetězec nesmí být delší než 8 znaků. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Řetězec musí být přesně 12 znaků dlouhý. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Řetězec musí mít aspoň 8 znaků a nesmí být delší než 16 znaků. |
| `min(value)` | `{age:min(18)}` | `19` | Celočíselná hodnota musí být minimálně 18. |
| `max(value)` | `{age:max(120)}` | `91` | Hodnota typu Integer nesmí být větší než 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Celočíselná hodnota musí být minimálně 18, ale ne víc než 120. |
| `alpha` | `{name:alpha}` | `Rick` | Řetězec musí obsahovat jeden nebo více abecedních znaků `a` - `z` a nerozlišuje velká a malá písmena. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Řetězec musí odpovídat regulárnímu výrazu. Přečtěte si tipy k definování regulárního výrazu. |
| `required` | `{name:required}` | `Rick` | Slouží k vykonání, že při generování adresy URL je přítomna hodnota bez parametru. |

[!INCLUDE[](~/includes/regex.md)]

V jednom parametru lze použít více omezení s oddělovači dvojtečky. Například následující omezení omezuje parametr na celočíselnou hodnotu 1 nebo vyšší:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR vždy používají invariantní jazykovou verzi. Například převod na typ `int` CLR nebo. `DateTime` Tato omezení předpokládají, že adresa URL není lokalizovatelné. Omezení tras poskytovaných rozhraním nemění hodnoty uložené v hodnotách tras. Všechny hodnoty tras přeložené z adresy URL se ukládají jako řetězce. Například `float` omezení se pokusí převést hodnotu trasy na typ float, ale převedená hodnota se používá pouze k ověření, že je možné ji převést na typ float.

### <a name="regular-expressions-in-constraints"></a>Regulární výrazy v omezeních

[!INCLUDE[](~/includes/regex.md)]

Regulární výrazy lze zadat jako vložená omezení pomocí omezení `regex(...)` trasy. Metody v <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> rodině také přijímají literál objektu omezení. V případě použití tohoto formuláře jsou řetězcové hodnoty interpretovány jako regulární výrazy.

Následující kód používá vložené omezení regulárního výrazu:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

Následující kód používá literál objektu pro určení omezení regulárního výrazu:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

Rozhraní ASP.NET Core se přidá `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` do konstruktoru regulárního výrazu. Popis <xref:System.Text.RegularExpressions.RegexOptions> těchto členů naleznete v tématu.

Regulární výrazy používají oddělovače a tokeny podobné těm, které používá směrování a jazyk C#. Tokeny regulárního výrazu musí být uvozeny řídicími znaky. Chcete-li použít regulární `^\d{3}-\d{2}-\d{4}$` výraz v rámci vloženého omezení, použijte jednu z následujících možností:

* Nahraďte `\` znaky zadané v řetězci jako `\\` znaky ve zdrojovém souboru C#, aby bylo možné řídicí `\` znak řetězce Escape řídicího znaku.
* [Doslovné řetězce literálů](/dotnet/csharp/language-reference/keywords/string).

`{`Chcete-li řídicí znaky oddělovače parametrů směrování `}`, `[` `]`,,, Zdvojnásobte znaky ve výrazu, `{{` `}}`například,, `[[`,. `]]` V následující tabulce je uveden regulární výraz a jeho řídicí verze:

| Regulární výraz    | Regulární výraz s řídicím znakem     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy používané ve směrování často začínají `^` znakem a odpovídají počáteční pozici řetězce. Výrazy často končí `$` znakem a odpovídají konci řetězce. Znaky `^` a `$` zajišťují, že regulární výraz odpovídá celé hodnotě parametru Route. Bez znaků `^` a `$` regulární výraz odpovídá jakémukoli podřetězci v rámci řetězce, což je často nežádoucí. V následující tabulce jsou uvedeny příklady a vysvětlení, proč se shodují nebo neshodují:

| Expression   | Řetězec    | Shoda | Poznámka               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | MZ        | Ano   | Výraz shody    |
| `[a-z]{2}`   | MZ        | Ano   | Nerozlišuje velká a malá písmena    |
| `^[a-z]{2}$` | hello     | Ne    | Viz `^` a `$` vyšší |
| `^[a-z]{2}$` | 123abc456 | Ne    | Viz `^` a `$` vyšší |

Další informace o syntaxi regulárního výrazu naleznete v tématu [.NET Framework regulární výrazy](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr na známou sadu možných hodnot, použijte regulární výraz. `{action:regex(^(list|get|create)$)}` Například odpovídá pouze hodnotě `action` trasy `list`, `get`nebo. `create` Pokud je předána do slovníku omezení, je `^(list|get|create)$` řetězec ekvivalentní. Omezení, která se předávají ve slovníku omezení, který se neshoduje s jedním ze známých omezení, jsou také považována za regulární výrazy. Omezení, která jsou předána v rámci šablony, která neodpovídá jednomu ze známých omezení, nejsou považována za regulární výrazy.

### <a name="custom-route-constraints"></a>Vlastní omezení trasy

Vlastní omezení směrování lze vytvořit implementací <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozhraní. `IRouteConstraint` Rozhraní <xref:System.Web.Routing.IRouteConstraint.Match*>obsahuje, které vrací `true` , pokud je omezení splněno, a `false` jinak.

Vlastní omezení tras je potřeba jenom zřídka. Před implementací vlastního omezení trasy zvažte alternativy, jako je třeba vazba modelu.

Chcete-li použít `IRouteConstraint`vlastní, musí být typ omezení trasy zaregistrován <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> v rámci aplikace v kontejneru služby. `ConstraintMap` Je slovník, který mapuje klíče omezení tras na `IRouteConstraint` implementace, které ověřují tato omezení. Aplikace se `ConstraintMap` dá v `Startup.ConfigureServices` rámci služeb aktualizovat buď jako součást [služby. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) volání nebo přímou konfigurací <xref:Microsoft.AspNetCore.Routing.RouteOptions> s `services.Configure<RouteOptions>`. Příklad:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

Předchozí omezení je použito v následujícím kódu:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

Implementace `MyCustomConstraint` brání `0` použití parametru trasy:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

Předcházející kód:

* Zabrání `0` v `{id}` segmentu trasy.
* Je zobrazený jako základní příklad implementace vlastního omezení. Neměl by se používat v produkční aplikaci.

Následující kód je lepší přístup, aby nedošlo `id` k tomu, `0` aby bylo možné zabránit zpracování obsahující a.

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

Předchozí kód má oproti `MyCustomConstraint` přístupu následující výhody:

* Nevyžaduje vlastní omezení.
* V případě, že parametr Route obsahuje `0`, vrátí výstižnější chybu.

## <a name="parameter-transformer-reference"></a>Odkaz na transformátor – parametr

Transformátory parametrů:

* Provést při generování propojení pomocí <xref:Microsoft.AspNetCore.Routing.LinkGenerator>.
* Implementujte <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.
* Jsou konfigurovány <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>pomocí.
* Převeďte hodnotu trasy parametru a Transformujte ji na novou řetězcovou hodnotu.
* Výsledkem použití transformované hodnoty ve vygenerovaném odkazu.

Například vlastní `slugify` parametr Transformer ve vzoru `blog\{article:slugify}` směrování s `Url.Action(new { article = "MyTestArticle" })` vygenerováním. `blog\my-test-article`

Vezměte v úvahu `IOutboundParameterTransformer` následující implementaci:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Pokud chcete použít transformující parametr ve schématu směrování, nakonfigurujte ho pomocí <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> v: `Startup.ConfigureServices`

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

Rozhraní ASP.NET Core Framework používá transformaci parametrů k transformaci identifikátoru URI, kde koncový bod řeší. Například transformační parametry transformují hodnoty trasy používané k `area`porovnávání, `controller`, `action`a. `page`

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

S předchozí šablonou směrování je akce `SubscriptionManagementController.GetAll` SHODNÁ s identifikátorem URI. `/subscription-management/get-all` Transformující parametr nemění hodnoty trasy použité k vygenerování odkazu. Například `Url.Action("GetAll", "SubscriptionManagement")` výstupy `/subscription-management/get-all`.

ASP.NET Core poskytuje konvence rozhraní API pro použití transformátorů parametrů s generovanými trasami:

* Konvence <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC aplikuje na všechny trasy atributů v aplikaci zadaný parametr Transformer. Parametr Transformer transformuje tokeny, když jsou nahrazeny. Další informace najdete v tématu [Použití transformátoru parametrů k přizpůsobení náhrady tokenu](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages používá konvenci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> rozhraní API. Tato konvence u všech automaticky zjištěných Razor Pages aplikuje zadaný transformátor parametrů. Parametr Transformer přetransformuje segmenty složky a názvu souboru na trasy Razor Pages. Další informace najdete v tématu [Použití transformátoru parametrů k přizpůsobení cest stránky](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>Odkaz na generování adresy URL

Tato část obsahuje odkaz na algoritmus implementovaný při generování adresy URL. V praxi používá většina složitých příkladů generování adresy URL řadiče nebo Razor Pages. Další informace najdete v tématu věnovaném [Směrování v řadičích](xref:mvc/controllers/routing) .

Proces generování adresy URL začíná voláním [LinkGenerator. GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) nebo podobné metody. Metoda je k dispozici s adresou, sadou hodnot směrování a volitelně informace o aktuálním požadavku z `HttpContext`.

Prvním krokem je použití adresy k vyřešení sady kandidátních koncových bodů pomocí objektu [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) , který odpovídá typu adresy.

Po nalezení sady kandidátů podle schématu adres jsou koncové body seřazené a zpracovávané iterativní, dokud nebude operace generování adresy URL úspěšná. Generování adresy URL **nekontrolují** nejednoznačnosti, první vrácený výsledek je konečný výsledek.

### <a name="troubleshooting-url-generation-with-logging"></a>Řešení potíží s generováním adresy URL pomocí protokolování

Prvním krokem při řešení potíží s generováním adresy URL je nastavení úrovně `Microsoft.AspNetCore.Routing` protokolování `TRACE`na. `LinkGenerator`protokoluje mnoho podrobností o jeho zpracování, které může být užitečné při řešení problémů.

Podrobnosti o generování adresy URL najdete v tématu [odkazy na generování adresy URL](#ugr) .

### <a name="addresses"></a>Adresy

Adresy představují koncept v adrese URL, který se používá pro svázání volání do generátoru odkazů do sady koncových bodů kandidáta.

Adresy představují rozšiřitelný koncept, který se ve výchozím nastavení dodává se dvěma implementacemi:

* Použití *názvu koncového bodu* (`string`) jako adresy:
    * Poskytuje podobné funkce jako název trasy MVC.
    * Používá typ <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> metadat.
    * Vyřeší poskytnutý řetězec proti metadatům všech registrovaných koncových bodů.
    * Vyvolá výjimku při spuštění, pokud více koncových bodů používá stejný název.
    * Doporučuje se pro účely obecného použití mimo řadiče a Razor Pages.
* Jako adresu použijte *hodnoty trasy* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>):
    * Poskytuje podobnou funkci pro řadiče a Razor Pages starší verze generování adresy URL.
    * Velmi složité pro rozšiřování a ladění.
    * Poskytuje implementaci `IUrlHelper`, kterou používá, pomocníkům značek, HTML pomocníkům, výsledky akcí atd.

Role schématu adres je učinit přidružení mezi adresou a shodnými koncovými body podle libovolného kritéria:

* Schéma názvu koncového bodu provede základní vyhledávání slovníku.
* Schéma hodnot tras má složitou nejlepší podmnožinu nastaveného algoritmu.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Okolní hodnoty a explicitní hodnoty

V rámci aktuální žádosti směrování přistupuje k hodnotám tras aktuálního požadavku `HttpContext.Request.RouteValues`. Hodnoty přidružené k aktuální žádosti jsou označovány jako **okolní hodnoty**. Pro účely srozumitelnosti dokumentace odkazuje na hodnoty tras předané do metod jako **explicitních hodnot**.

Následující příklad ukazuje okolní hodnoty a explicitní hodnoty. Poskytuje okolí hodnoty z aktuální žádosti a explicitních hodnot: `{ id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

Předcházející kód:

* Vrátí`/Widget/Index/17`
* Získá <xref:Microsoft.AspNetCore.Routing.LinkGenerator> přes [di](xref:fundamentals/dependency-injection).

Následující kód neposkytuje žádné okolní hodnoty a explicitní hodnoty: `{ controller = "Home", action = "Subscribe", id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

Předchozí metoda vrátí`/Home/Subscribe/17`

Následující kód `WidgetController` vrátí `/Widget/Subscribe/17`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

Následující kód poskytuje kontroler z okolních hodnot v aktuální žádosti a explicitní hodnoty: `{ action = "Edit", id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

V předchozím kódu:

* `/Gadget/Edit/17`je vrácen.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url>Získá <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
vygeneruje adresu URL s absolutní cestou pro metodu Action. Adresa URL obsahuje zadaný `action` název a `route` hodnoty.

Následující kód poskytuje okolí hodnot z aktuální žádosti a explicitních hodnot: `{ page = "./Edit, id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

Předchozí kód je nastaven `url` na `/Edit/17` , pokud stránka upravit Razor obsahuje následující direktivu stránky:

 `@page "{id:int}"`

Pokud stránka pro úpravy neobsahuje šablonu `"{id:int}"` směrování, `url` je. `/Edit?id=17`

Chování MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> přináší kromě pravidel popsaných tady také vrstvu složitosti:

* `IUrlHelper`vždy poskytuje hodnoty tras z aktuálního požadavku jako okolní hodnoty.
* [IUrlHelper. Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) vždycky zkopíruje aktuální `action` a `controller` směrovat hodnoty jako explicitní hodnoty, pokud vývojář nepřepíše.
* [IUrlHelper. Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) vždycky zkopíruje aktuální `page` hodnotu trasy jako explicitní hodnotu, pokud není přepsána. <!--by the user-->
* `IUrlHelper.Page`vždy přepíše hodnotu aktuální `handler` trasy `null` jako explicitní hodnoty, pokud není přepsána.

Uživatelé jsou často překvapeni podrobnostmi o okolních hodnotách, protože MVC nevypadá podle svých vlastních pravidel. V případě historických a kompatibilních důvodů jsou některé hodnoty trasy `action`, `controller`například `page`,, `handler` a mají své vlastní speciální chování.

Ekvivalentní funkce `LinkGenerator.GetPathByAction` , které poskytuje a `LinkGenerator.GetPathByPage` duplikují tyto anomálie `IUrlHelper` kvůli kompatibilitě.

### <a name="url-generation-process"></a>Proces generování adresy URL

Jakmile se najde sada koncových bodů kandidáta, algoritmus generování adresy URL:

* Zpracuje koncové body iterativní.
* Vrátí první úspěšný výsledek.

Prvním krokem v tomto procesu se říká **neplatnost hodnoty směrování**.  Neplatná hodnota trasy je proces, podle kterého směrování rozhoduje o tom, které hodnoty tras z okolních hodnot se mají použít a které by se měly ignorovat. Každá hodnota okolí je považována za a buď kombinovaná s explicitními hodnotami, nebo se ignoruje.

Nejlepším způsobem, jak se zamyslet na roli okolních hodnot, je, že se v některých běžných případech pokusí uložit vývojářům aplikací psaní. Scénáře, ve kterých jsou okolní hodnoty užitečné, jsou tradičně související s MVC:

* Při propojení s jinou akcí ve stejném kontroleru není nutné zadat název kontroleru.
* Při propojení s jiným řadičem ve stejné oblasti není nutné zadávat název oblasti.
* Při propojování se stejnou metodou akce není nutné zadávat hodnoty směrování.
* Při propojování s jinou částí aplikace nechcete přenášet hodnoty tras, které nemají v této části aplikace žádný význam.

Volání `LinkGenerator` nebo `IUrlHelper` tyto návraty `null` jsou obvykle způsobena neporozuměním neplatností hodnoty trasy. Pokud chcete zjistit, jestli se problém vyřeší, vyřešte neplatnost hodnoty trasy explicitním zadáním více hodnot tras.

Neplatnost hodnoty směrování funguje na předpokladu, že schéma adresy URL aplikace je hierarchické, s hierarchií vytvořenou zleva doprava. Vezměte v úvahu šablonu `{controller}/{action}/{id?}` postupu základního kontroleru, abyste získali intuitivní představu o tom, jak to funguje v praxi. **Změna** hodnoty **zruší platnost** všech hodnot tras, které se zobrazí vpravo. To odráží předpoklad hierarchie. Pokud má aplikace okolní hodnotu pro `id`a operace určuje jinou hodnotu pro: `controller`

* `id`se znovu nepoužije, `{controller}` protože je nalevo od `{id?}`.

Některé příklady demonstrují tento princip:

* Pokud explicitní hodnoty obsahují hodnotu pro `id`, hodnota okolí pro `id` je ignorována. Okolní hodnoty pro `controller` a `action` lze použít.
* Pokud explicitní hodnoty obsahují hodnotu pro `action`, všechny okolí hodnoty pro `action` je ignorováno. Okolní hodnoty pro `controller` lze použít. Pokud je explicitní hodnota pro `action` odlišná od okolní hodnoty pro `action`, hodnota se `id` nepoužije.  Pokud je explicitní hodnota pro `action` shodná s hodnotou okolí pro `action`, lze použít `id` hodnotu.
* Pokud explicitní hodnoty obsahují hodnotu pro `controller`, všechny okolí hodnoty pro `controller` je ignorováno. Pokud je explicitní hodnota pro `controller` odlišná od hodnoty okolí pro `controller`, hodnoty `action` a `id` nebudou použity. Pokud je explicitní hodnota pro `controller` shodná s hodnotou okolí pro `controller`, lze použít hodnoty `action` a. `id`

Tento proces je dále komplikovaný existence tras atributů a vyhrazených konvenčních tras. Řadiče konvenčních cest, `{controller}/{action}/{id?}` jako je například určení hierarchie pomocí parametrů směrování. Pro [vyhrazené konvenční trasy](xref:mvc/controllers/routing#dcr) a [Směrování atributů](xref:mvc/controllers/routing#ar) na řadiče a Razor Pages:

* Existuje hierarchie hodnot směrování.
* Nezobrazují se v šabloně.

V těchto případech generování adresy URL definuje koncept **požadovaných hodnot** . Koncové body vytvořené řadiči a Razor Pages mají zadané požadované hodnoty, které umožňují fungování neplatnosti hodnoty směrování.

Podrobnosti o algoritmu neplatnosti hodnoty směrování:

* Požadované názvy hodnot jsou kombinovány s parametry směrování a následně zpracovány z zleva doprava.
* Pro každý parametr se porovná okolní hodnota a explicitní hodnota:
    * Pokud je okolní hodnota a explicitní hodnota stejná, proces pokračuje.
    * Pokud je hodnota okolí přítomná a explicitní hodnota není, použije se při generování adresy URL okolní hodnota.
    * Pokud okolní hodnota není přítomna a explicitní hodnota je, zamítnout okolní hodnotu a všechny následné okolní hodnoty.
    * Pokud je přítomna okolní hodnota a explicitní hodnota a dvě hodnoty se liší, zamítnout okolní hodnotu a všechny následné hodnoty okolí.

V tomto okamžiku je operace generování adresy URL připravena k vyhodnocení omezení trasy. Sada přijatých hodnot je kombinována s výchozími hodnotami parametrů, které jsou k dispozici v omezeních. Pokud jsou omezení splněna, operace pokračuje.

V dalším kroku lze **přijmout hodnoty** , které slouží k rozbalení šablony trasy. Zpracovává se šablona trasy:

* Zleva doprava.
* U každého parametru je nahrazena jeho přijatá hodnota.
* Následující zvláštní případy:
  * Pokud v poli přijatelné hodnoty chybí hodnota a parametr má výchozí hodnotu, použije se výchozí hodnota.
  * Pokud v poli přijatelné hodnoty chybí hodnota a parametr je nepovinný, zpracování pokračuje.
  * Pokud libovolný parametr trasy napravo od chybějícího volitelného parametru má hodnotu, operace se nezdařila.
  * <!-- review default-valued parameters optional parameters --> Sousedící parametry výchozí hodnoty a volitelné parametry jsou sbaleny tam, kde je to možné.

Hodnoty zadané explicitně, které neodpovídají segmentu trasy, se přidají do řetězce dotazu. V následující tabulce je uveden výsledek při použití šablony `{controller}/{action}/{id?}`směrování.

| Okolní hodnoty                     | Explicitní hodnoty                        | Výsledek                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| Controller = "domů"                | Action = "o"                       | `/Home/About`           |
| Controller = "domů"                | Controller = "objednávka"; Action = "o" | `/Order/About`          |
| Controller = "Home"; Color = "Red" | Action = "o"                       | `/Home/About`           |
| Controller = "domů"                | Action = "o", Color = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Problémy s neplatností hodnoty trasy

Od verze ASP.NET Core 3,0 nemusí některá schémata generování adres URL používaná v dřívějších ASP.NET Core verzích dobře spolupracovat s generováním adresy URL. Tým ASP.NET Core plánuje přidat funkce, které tyto potřeby řeší v budoucí verzi. V současné době je nejvhodnějším řešením použití starší verze směrování.

Následující kód ukazuje příklad schématu generování adresy URL, které není podporováno směrováním.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

V předchozím kódu je parametr `culture` Route použit k lokalizaci. Je potřeba, aby `culture` parametr byl vždycky přijatý jako ambientní hodnota. `culture` Parametr ale není přijatý jako ambientní hodnota z důvodu způsobu, jakým požadované hodnoty fungují:

* V šabloně `"default"` trasy je `culture` parametr trasy nalevo `controller`od, takže změny se `controller` nebudou odhodnocovat. `culture`
* V šabloně `"blog"` trasy je parametr `culture` Route považován za napravo od `controller`, což se zobrazuje v požadovaných hodnotách.

## <a name="configuring-endpoint-metadata"></a>Konfigurace metadat koncového bodu

Následující odkazy obsahují informace o konfiguraci metadat koncového bodu:

* [Povolení CORS s směrováním koncových bodů](xref:security/cors#enable-cors-with-endpoint-routing)
* [Ukázka IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) s použitím vlastního `[MinimumAgeAuthorize]` atributu
* [Test ověřování pomocí atributu [autorizovat]](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [Výběr schématu pomocí atributu [autorizovat]](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [Použití zásad pomocí atributu [autorizační]](xref:security/authorization/policies#applying-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>Přiřazení hostitelů v cestách pomocí RequireHost

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*>použije omezení na trasu, která vyžaduje zadaného hostitele. Parametr `RequireHost` nebo [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) může být:

* Hostitel: `www.domain.com`, odpovídá `www.domain.com` jakémukoli portu.
* Hostitel se zástupnými znaky `www.domain.com`: `subdomain.domain.com` `www.subdomain.domain.com` `*.domain.com`, odpovídá, nebo na jakémkoli portu.
* Port: `*:5000`, odpovídá portu 5000 všem hostitelům.
* Hostitel a port: `www.domain.com:5000` nebo `*.domain.com:5000`se shoduje s hostitelem a portem.

Více parametrů lze zadat pomocí `RequireHost` nebo. `[Host]` Omezení odpovídá počtu hostitelů platných pro libovolný parametr. Například `[Host("domain.com", "*.domain.com")]` odpovídá `domain.com` `www.domain.com`, a `subdomain.domain.com`.

Následující kód používá `RequireHost` pro vyžadování zadaného hostitele v trase:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

Následující kód používá `[Host]` atribut na řadiči pro vyžadování některého z určených hostitelů:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

Když je `[Host]` atribut použit pro metodu Controller a Action:

* Atribut akce je použit.
* Atribut kontroleru se ignoruje.

## <a name="performance-guidance-for-routing"></a>Průvodce výkonem pro směrování

Většina směrování se v ASP.NET Core 3,0 aktualizovala, aby se zvýšil výkon.

Pokud dojde k problémům s výkonem aplikace, směrování je často podezřelé jako problém. Podezření na směrování je, že architektury, jako jsou řadiče, a Razor Pages hlásí množství času stráveného v rámci rozhraní ve zprávách protokolování. V případě významného rozdílu mezi časem hlášeným řadiči a celkovou dobou trvání žádosti:

* Vývojáři odstraňují svůj kód aplikace jako zdroj problému.
* Je běžné předpokládat, že směrování je příčinou.

Směrování je Testováno pomocí tisíců koncových bodů. Je pravděpodobné, že Typická aplikace zaznamená problém s výkonem, který je právě velký. Nejběžnější hlavní příčinou pomalého výkonu směrování je obvykle nesprávně se často fungujícím vlastním middlewarem.

Následující příklad kódu ukazuje základní techniku pro zúžení zdroje zpoždění:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Čas do směrování:

* Proložení každého middlewaru pomocí kopie middlewaru časování zobrazeného v předchozím kódu.
* Přidejte jedinečný identifikátor, který bude korelovat data časování s kódem.

Jedná se o základní způsob zúžení zpoždění, pokud je důležité, například více než `10ms`.  Odečte se `Time 2` od `Time 1` sestav čas strávený uvnitř `UseRouting` middlewaru.

Následující kód používá kompaktnější přístup k předchozímu kódu časování:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Potenciálně nákladné funkce směrování

Následující seznam obsahuje přehled funkcí směrování, které jsou v porovnání se základními šablonami směrování poměrně nákladné:

* Regulární výrazy: je možné napsat regulární výrazy, které jsou složité, nebo mají dlouhou dobu běhu s malým množstvím vstupu.

* Komplexní segmenty`{x}-{y}-{z}`(): 
  * Jsou podstatně dražší než analýza běžného segmentu cesty URL.
  * Výsledkem je přidělení mnoha dalších podřetězců.
  * V aktualizaci výkonu směrování ASP.NET Core 3,0 nebyla aktualizována logika komplexního segmentu.

* Synchronní přístup k datům: mnoho složitých aplikací má přístup k databázi jako součást jejich směrování. ASP.NET Core 2,2 a starší směrování nemusí poskytovat správné body rozšiřitelnosti pro podporu směrování přístupu k databázi. Například <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, a <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> jsou synchronní. Body rozšiřitelnosti, <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> jako <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> jsou a, jsou asynchronní.

## <a name="guidance-for-library-authors"></a>Doprovodné materiály pro autory knihovny

Tato část obsahuje pokyny pro autory knihoven, kteří sestavují na směrování. Tyto podrobnosti jsou určené k tomu, aby se zajistilo, že vývojáři aplikací mají dobré zkušenosti s používáním knihoven a architektur, které šíří směrování.

### <a name="define-endpoints"></a>Definování koncových bodů

Chcete-li vytvořit rozhraní, které používá směrování pro odpovídající adresu URL, začněte definováním uživatelského prostředí, které sestaví na začátku <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.

**Sestavte** nahoru <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>. To umožňuje uživatelům vytvářet vaše rozhraní s jinými ASP.NET Core funkcemi bez nejasností. Každá šablona ASP.NET Core zahrnuje směrování. Předpokládat, že směrování je k dispozici a je pro uživatele známé.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**Vraťte z** volání do `MapMyFramework(...)` tohoto implementace <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>zapečetěný konkrétní typ. Většina metod `Map...` rozhraní se řídí tímto modelem. `IEndpointConventionBuilder` Rozhraní:

* Umožňuje vytváření metadat.
* Cílí na celou řadu rozšiřujících metod.

Deklarace vlastního typu umožňuje do Tvůrce přidat vlastní funkce specifické pro rozhraní. Je to v pořádku, pokud chcete zabalit tvůrce deklarovaného rozhraní a přesměrovat do něj volání.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**Zvažte** vytvoření vlastního <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>. `EndpointDataSource`je primitiva nízké úrovně pro deklarování a aktualizaci kolekce koncových bodů. `EndpointDataSource`je výkonné rozhraní API používané řadiči a Razor Pages.

Testy směrování mají [základní příklad](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) zdroje dat bez aktualizace.

**Nepokoušejte se** zaregistrovat `EndpointDataSource` ve výchozím nastavení. Vyžaduje, aby uživatelé zaregistrovali vaše <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>rozhraní v. Filozofie směrování znamená, že ve výchozím nastavení není nic zahrnuto a je `UseEndpoints` to místo pro registraci koncových bodů.

### <a name="creating-routing-integrated-middleware"></a>Vytváření middleware integrovaného s směrováním

**Zvažte** definování typů metadat jako rozhraní.

**Umožňuje použít** typy metadat jako atribut pro třídy a metody.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Architektury, jako jsou řadiče a Razor Pages podporují použití atributů metadat na typy a metody. Pokud deklarujete typy metadat:

* Zpřístupněte je jako [atributy](/dotnet/csharp/programming-guide/concepts/attributes/).
* Většina uživatelů je obeznámena s použitím atributů.

Deklarace typu metadat jako rozhraní přidává další vrstvu flexibility:

* Rozhraní jsou sestavitelná.
* Vývojáři mohou deklarovat své vlastní typy, které kombinují více zásad.

**Proveďte** možnost přepsat metadata, jak je znázorněno v následujícím příkladu:

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

Nejlepším způsobem, jak postupovat podle těchto pokynů, je vyhnout se definování **metadat značek**:

* Nehledejte jenom přítomnost typu metadat.
* Definujte vlastnost pro metadata a ověřte vlastnost.

Kolekce metadat je uspořádaná a podporuje přepsání podle priority. V případě řadičů jsou metadata na metodě Action nejvíc specifická.

**Používejte** middleware pro a bez směrování.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

Jako příklad tohoto pravidla zvažte `UseAuthorization` middleware. Middleware autorizace vám umožní předat záložní zásady. <!-- shown where?  (shown here) --> Záložní zásada, pokud je zadána, platí pro:

* Koncové body bez zadaných zásad.
* Žádosti, které se neshodují s koncovým bodem.

Tímto způsobem je middleware autorizace užitečný mimo kontext směrování. Middleware autorizace se dá použít k tradičnímu programování middlewaru.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Směrování zodpovídá za mapování identifikátorů URI požadavků na koncové body a odesílání příchozích požadavků do těchto koncových bodů. Trasy jsou v aplikaci definované a nakonfigurované při spuštění aplikace. Trasa může volitelně extrahovat hodnoty z adresy URL obsažené v žádosti a tyto hodnoty pak lze použít pro zpracování požadavků. Směrování pomocí informací o trasách z aplikace taky umožňuje generovat adresy URL, které se mapují na koncové body.

Pokud chcete použít nejnovější scénáře směrování v ASP.NET Core 2,2, zadejte [verzi kompatibility](xref:mvc/compatibility-version) pro registraci služby MVC v `Startup.ConfigureServices`těchto umístěních:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> Možnost určuje, jestli má směrování interně používat logiku založenou na koncovém bodu nebo <xref:Microsoft.AspNetCore.Routing.IRouter>logiku na základě ASP.NET Core 2,1 nebo starší verze. Pokud je verze kompatibility nastavená na 2,2 nebo novější, výchozí hodnota je `true`. Nastavte hodnotu `false` na použít předchozí logiku směrování:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Další informace o <xref:Microsoft.AspNetCore.Routing.IRouter>směrování na základě služby najdete v [tomto tématu ve verzi ASP.NET Core 2,1](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

> [!IMPORTANT]
> Tento dokument popisuje směrování ASP.NET Core nízké úrovně. Informace o ASP.NET Core směrování MVC najdete v tématu <xref:mvc/controllers/routing>. Informace o konvencích směrování v Razor Pages najdete v <xref:razor-pages/razor-pages-conventions>tématu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Základy směrování

Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné. Výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}`:

* Podporuje základní a popisné schéma směrování.
* Je užitečným výchozím bodem pro aplikace založené na uživatelském rozhraní.

Vývojáři obvykle přidávají další trasy stručný do oblastí s vysokým provozem v aplikaci ve specializovaných situacích pomocí [Směrování atributů](xref:mvc/controllers/routing#attribute-routing) nebo vyhrazených konvenčních tras. Mezi specializované příklady situací patří, blogové a elektronického obchodování koncové body.

Webové rozhraní API by mělo používat směrování atributů k modelování funkcí aplikace jako sady prostředků, ve kterých jsou operace reprezentované příkazy HTTP. To znamená, že celá řada operací, například GET a POST, na stejném logickém prostředku používá stejnou adresu URL. Směrování atributů poskytuje úroveň řízení, která je nutná k pečlivému návrhu rozložení veřejného koncového bodu rozhraní API.

Aplikace Razor Pages používají výchozí konvenční směrování pro obsluhu pojmenovaných prostředků ve složce *Pages* v aplikaci. K dispozici jsou další konvence, které vám umožní přizpůsobit Razor Pages chování směrování. Další informace naleznete v tématech <xref:razor-pages/index> a <xref:razor-pages/razor-pages-conventions>.

Podpora generování adresy URL umožňuje, aby se aplikace vyvinula bez adres URL s pevným kódováním, aby bylo možné propojit aplikaci dohromady. Tato podpora umožňuje začít se základní konfigurací směrování a upravovat trasy po určení rozložení prostředků aplikace.

Směrování používá pro reprezentaci logických koncových bodů v aplikaci *koncové body* (`Endpoint`).

Koncový bod definuje delegáta pro zpracování požadavků a kolekci libovolných metadat. Metadata se používají k implementaci průřezů na základě zásad a konfigurace připojených ke každému koncovému bodu.

Systém směrování má následující vlastnosti:

* Syntaxe šablony směrování se používá k definování tras s tokeny parametrů trasy.
* Konfigurace koncového bodu stylů a stylu atributu je povolena.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>slouží k určení, zda parametr adresy URL obsahuje platnou hodnotu pro dané omezení koncového bodu.
* Modely aplikací, jako je MVC/Razor Pages, registrují všechny své koncové body, které mají předvídatelné implementaci scénářů směrování.
* Implementace směrování provádí rozhodování o směrování všude, kde je to požadováno v kanálu middlewaru.
* Middleware, který se zobrazí po vytvoření middlewaru směrování, může zkontrolovat výsledek rozhodnutí koncového bodu middleware směrování pro daný identifikátor URI žádosti.
* Je možné vytvořit výčet všech koncových bodů v aplikaci kdekoli v kanálu middlewaru.
* Aplikace může používat směrování k vygenerování adres URL (například pro přesměrování nebo propojení) na základě informací o koncových bodech, takže se vyhnete pevně zakódovaným adresám URL, které pomáhají zachovat.
* Generování adresy URL vychází z adres, které podporují libovolné rozšíření:

  * Rozhraní API generátoru odkazů<xref:Microsoft.AspNetCore.Routing.LinkGenerator>() je možné vyřešit kdekoli pomocí [vkládání závislostí (di)](xref:fundamentals/dependency-injection) pro generování adres URL.
  * Kde rozhraní API generátoru odkazů není k dispozici <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> prostřednictvím di, nabízí metody pro sestavování adres URL.

> [!NOTE]
> S vydáním směrování koncových bodů v ASP.NET Core 2,2 je propojení koncových bodů omezené na akce a stránky MVC/Razor Pages. Pro budoucí verze jsou plánovány rozšíření funkcí pro propojení koncových bodů.

Směrování je k kanálu [middleware](xref:fundamentals/middleware/index) připojeno <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> třídou. [ASP.NET Core MVC](xref:mvc/overview) v rámci své konfigurace přidává směrování do kanálu middlewaru a zpracovává směrování v MVC a Razor Pages aplikacích. Informace o tom, jak používat směrování jako samostatnou součást, najdete v části [použití middlewaru pro směrování](#use-routing-middleware) .

### <a name="url-matching"></a>Shoda adresy URL

Shoda adresy URL je proces, podle kterého směrování odešle příchozí požadavek na *koncový bod*. Tento proces je založený na datech v cestě URL, ale dá se rozšířit, aby v žádosti mohla být považovat všechna data. Schopnost odesílat žádosti na samostatné obslužné rutiny je klíč pro škálování velikosti a složitosti aplikace.

Systém směrování v rámci směrování koncových bodů zodpovídá za všechna rozhodnutí o odesílání. Vzhledem k tomu, že middleware používá zásady na základě vybraného koncového bodu, je důležité, aby jakékoli rozhodnutí, které může ovlivnit odesílání nebo použití zásad zabezpečení, bylo provedeno v rámci systému směrování.

Po spuštění delegáta koncového bodu jsou vlastnosti [RouteContext. parametr RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) nastaveny na odpovídající hodnoty na základě dosud provedeného zpracování požadavků.

[Parametr RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) je slovník *hodnot tras* vytvořených z trasy. Tyto hodnoty se obvykle určují pomocí tokenizací adresy URL a dají se použít k přijetí vstupu uživatele nebo k dalšímu odesílání rozhodnutí v rámci aplikace.

[Parametr RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) je kontejner objektů a dat pro další data související s odpovídající trasou. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>jsou k dispozici pro podporu přidružování dat o stavu k jednotlivým cestám, aby aplikace mohla učinit rozhodnutí na základě toho, na které trase odpovídá. Tyto hodnoty jsou definované vývojářem a **neovlivňují chování** směrování jakýmkoli způsobem. Kromě toho hodnoty dočasně ukládané v [parametr RouteData. Datatokeny](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) můžou být libovolného typu, na rozdíl od [parametr RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), které musí být převoditelné na a z řetězců.

[Parametr RouteData. routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) je seznam tras, které byly součástí úspěšného porovnání požadavku. Trasy mohou být vnořeny do sebe navzájem. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> Vlastnost odráží cestu v logickém stromu tras, jejichž výsledkem byla shoda. Obecně platí, že první položka <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> v nástroji je kolekce tras a měla by se používat pro generování adresy URL. Poslední položka v <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> je obslužná rutina trasy, která se shoduje.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>Generování adresy URL pomocí LinkGenerator

Generování adresy URL je proces, podle kterého směrování může vytvořit cestu adresy URL na základě sady hodnot tras. To umožňuje logické oddělení mezi vašimi koncovými body a adresami URL, které k nim mají přístup.

Směrování koncového bodu zahrnuje rozhraní API generátoru odkazů (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator>je služba typu Singleton, kterou lze načíst z [di](xref:fundamentals/dependency-injection). Rozhraní API lze použít mimo kontext vykonávajícího požadavku. MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> a scénáře, které spoléhají na <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, jako jsou například [pomocníky značek](xref:mvc/views/tag-helpers/intro), HTML helps a [výsledky akcí](xref:mvc/controllers/actions), používají generátor propojení k poskytování možností vytváření odkazů.

Generátor propojení se zálohuje konceptem *adres* a *schémat adres*. Schéma adres je způsob, jak určit koncové body, které by měly být považovány za vytváření odkazů. Například název trasy a hodnoty tras vycházejí z MVC/Razor Pages jsou implementovány jako schéma adres.

Generátor propojení může propojit s akcemi MVC/Razor Pages a stránkami prostřednictvím následujících rozšiřujících metod:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Přetížení těchto metod akceptuje argumenty, které zahrnují `HttpContext`. Tyto metody jsou funkčně ekvivalentní `Url.Action` a `Url.Page` ale nabízejí další flexibilitu a možnosti.

`GetPath*` Metody jsou nejčastěji podobné `Url.Action` a `Url.Page` v tom, že generují identifikátor URI obsahující absolutní cestu. `GetUri*` Metody vždy generují absolutní identifikátor URI obsahující schéma a hostitele. Metody, které přijímají `HttpContext` identifikátor URI v kontextu zpracovávaného požadavku. Použijí se hodnoty tras, základní cesta, schéma a hostitel z zpracovávaného požadavku, pokud nejsou přepsány.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>je volána s adresou. K vygenerování identifikátoru URI dochází ve dvou krocích:

1. Adresa je svázána se seznamem koncových bodů, které odpovídají dané adrese.
1. Každý koncový bod `RoutePattern` je vyhodnocen, dokud se nenajde vzor směrování, který odpovídá zadaným hodnotám. Výsledný výstup je v kombinaci s ostatními částmi identifikátoru URI dodanými generátorem odkazů a vrácenými.

Metody poskytované funkcí <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard pro vytváření odkazů pro jakýkoli typ adresy. Nejpohodlnější způsob použití generátoru odkazů je prostřednictvím metod rozšíření, které provádějí operace pro konkrétní typ adresy.

| Metoda rozšíření   | Popis                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Vygeneruje identifikátor URI s absolutní cestou na základě zadaných hodnot. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Vygeneruje absolutní identifikátor URI na základě zadaných hodnot.             |

> [!WARNING]
> Věnujte pozornost následujícím důsledkům volání <xref:Microsoft.AspNetCore.Routing.LinkGenerator> metod:
>
> * Používejte `GetUri*` rozšiřující metody s opatrností v konfiguraci aplikace, která neověřuje `Host` hlavičku příchozích požadavků. Pokud `Host` záhlaví příchozích požadavků není ověřeno, lze nedůvěryhodný vstup žádosti poslat zpátky klientovi v identifikátorech URI na stránce zobrazení nebo stránky. Doporučujeme, aby všechny produkční aplikace nakonfigurovali server, aby ověřili `Host` hlavičku se známými platnými hodnotami.
>
> * Používejte <xref:Microsoft.AspNetCore.Routing.LinkGenerator> s opatrností v middleware v `Map` kombinaci `MapWhen`s nebo. `Map*`změní základní cestu spouštěné žádosti, která má vliv na výstup vytváření odkazů. Všechna <xref:Microsoft.AspNetCore.Routing.LinkGenerator> rozhraní API umožňují zadat základní cestu. Vždy zadat prázdnou základní cestu, která `Map*`bude mít vliv na generování odkazů.

## <a name="differences-from-earlier-versions-of-routing"></a>Rozdíly oproti starším verzím směrování

Mezi směrováním koncových bodů existuje několik rozdílů v ASP.NET Core 2,2 nebo novějším a starších verzích směrování v ASP.NET Core:

* Systém směrování koncových bodů nepodporuje <xref:Microsoft.AspNetCore.Routing.IRouter>rozšíření na základě podpory, včetně dědění <xref:Microsoft.AspNetCore.Routing.Route>z.

* Směrování koncového bodu nepodporuje [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). K pokračování v používání překrytí kompatibility použijte [verzi](xref:mvc/compatibility-version) `.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`2,1 Compatibility.

* Směrování koncového bodu má pro velká a malá písmena vygenerovaných identifikátorů URI při použití konvenčních tras jiné chování.

  Vezměte v úvahu následující výchozí šablonu trasy:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Předpokládejme, že jste vygenerovali odkaz na akci pomocí následujícího postupu:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  Pomocí <xref:Microsoft.AspNetCore.Routing.IRouter>směrování založeného na tomto kódu generuje identifikátor URI `/blog/ReadPost/17`, který respektuje velikost zadané hodnoty trasy. Směrování koncových bodů v ASP.NET Core 2,2 nebo `/Blog/ReadPost/17` novějším má za následek to, že je "blog" na velká písmena. Směrování koncového bodu `IOutboundParameterTransformer` poskytuje rozhraní, které se dá použít k globálnímu přizpůsobení tohoto chování, nebo k aplikování různých konvencí pro mapování adres URL.

  Další informace najdete v části [referenční informace pro parametry transformátoru](#parameter-transformer-reference) .

* Generace odkazů, kterou používá MVC/Razor Pages, se při pokusu o připojení ke kontroléru nebo akci nebo stránce, která neexistuje, chová jinak.

  Vezměte v úvahu následující výchozí šablonu trasy:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Předpokládejme, že jste vygenerovali odkaz na akci s použitím výchozí šablony s následujícím:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  V `IRouter`rámci směrování založeného na službě je výsledek `/Blog/ReadPost/17`vždy, i když `BlogController` neexistuje nebo nemá metodu `ReadPost` Action. Podle očekávání, směrování koncového bodu v ASP.NET Core 2,2 nebo `/Blog/ReadPost/17` vyšší vytvoří, pokud metoda Action existuje. *Směrování koncových bodů ale vytvoří prázdný řetězec, pokud akce neexistuje.* V koncepčním případě směrování koncového bodu nepředpokládá, že koncový bod existuje, pokud akce neexistuje.

* Při použití s směrováním koncových bodů se *algoritmus neplatných v okolí* generování propojení chová jinak.

  *Neplatnost okolní hodnoty* je algoritmus, který určuje, které hodnoty směrování z aktuálně prováděné žádosti (okolní hodnoty) se dají použít při operacích generování odkazů. Konvenční směrování vždy neověřené hodnoty dodatečné trasy při propojení s jinou akcí. Směrování atributů neobsahovalo toto chování před vydáním ASP.NET Core 2,2. V dřívějších verzích ASP.NET Core odkazy na jinou akci, která používá stejné názvy parametrů trasy, způsobily chyby generování odkazů. V ASP.NET Core 2,2 nebo novějších, obě formy směrování při propojování s jinou akcí zruší hodnoty.

  Vezměte v úvahu následující příklad ASP.NET Core 2,1 nebo starší verze. Při propojení s jinou akcí (nebo jinou stránkou) lze hodnoty směrování znovu použít nežádoucím způsobem.

  V */Pages/Store/Product.cshtml*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  V */Pages/Login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  Pokud je `/Store/Product/18` identifikátor URI v ASP.NET Core 2,1 nebo starším, odkaz vygenerovaný na stránce úložiště/informace `@Url.Page("/Login")` je. `/Login/18` `id` Hodnota 18 se znovu použije, i když je cíl propojení jinou součástí aplikace. Hodnota `id` trasy v kontextu `/Login` stránky je pravděpodobně hodnota ID uživatele, nikoli hodnota ID produktu úložiště.

  V rámci směrování koncového bodu s ASP.NET Core 2,2 nebo novějším `/Login`je výsledkem. Okolní hodnoty se znovu nepoužijí, pokud je cíl propojení jinou akcí nebo stránkou.

* Syntaxe parametru trasy s kulatým Trip: lomítka nejsou zakódována při použití syntaxe parametrů Double`**`-hvězdička () catch-ALL.

  Během generování propojení systém směrování zakóduje hodnotu zachycenou parametrem zachycení dvojité hvězdičky (`**`) (například `{**myparametername}`) s výjimkou lomítka. Směrování s dvojitou hvězdičkou (catch-All `IRouter`) je podporováno v rámci směrování založeného na ASP.NET Core 2,2 nebo novějším.

  V předchozích verzích ASP.NET Core () se podporuje jednoduchá hvězdička All – všechny`{*myparametername}`syntaxe parametrů () a lomítka jsou kódovaná.

  | Trasa              | Odkaz vygeneroval s<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts`(předávané lomítko je zakódováno)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Příklad middlewaru

V následujícím příkladu middleware používá <xref:Microsoft.AspNetCore.Routing.LinkGenerator> rozhraní API k vytvoření odkazu na metodu akce, která obsahuje seznam produktů pro Store. Použití generátoru odkazů vložením do třídy a volání `GenerateLink` je k dispozici pro libovolnou třídu v aplikaci.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>Vytvoření tras

Většina aplikací vytváří trasy voláním <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> nebo jedné z podobných metod rozšíření definovaných v <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Kterákoli z <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> rozšiřujících metod vytvoří instanci <xref:Microsoft.AspNetCore.Routing.Route> a přidá ji do kolekce tras.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nepřijímá parametr obslužné rutiny trasy. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>přidá pouze trasy, které jsou zpracovávány <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Další informace o směrování v MVC najdete v tématu <xref:mvc/controllers/routing>.

Následující příklad kódu je příkladem <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> volání využívaného typickou ASP.NET Core definice trasy MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Tato šablona odpovídá cestě URL a extrahuje hodnoty tras. Například cesta `/Products/Details/17` generuje následující hodnoty trasy: `{ controller = Products, action = Details, id = 17 }`.

Hodnoty tras se určují rozdělením cesty URL na segmenty a porovnáním jednotlivých segmentů s názvem *parametru trasy* v šabloně směrování. Parametry směrování jsou pojmenovány. Parametry definované ohraničujícím název parametru ve složených závorkách `{ ... }`.

Předchozí šablona může také odpovídat cestě `/` URL a vydávat hodnoty. `{ controller = Home, action = Index }` K tomu dochází, `{controller}` protože `{action}` parametry směrování a mají výchozí hodnoty a `id` parametr trasy je nepovinný. Znak rovná se (`=`) následovaný hodnotou po názvu parametru trasy, který definuje výchozí hodnotu parametru. Otazník (`?`) po názvu parametru trasy definuje volitelný parametr.

Parametry směrování s výchozí hodnotou *vždy* vytvoří hodnotu trasy, když odpovídá trasa. Pokud neexistuje žádný odpovídající segment cesty k adrese URL, volitelné parametry nevytvoří hodnotu trasy. Podrobný popis scénářů a syntaxe šablon směrování najdete v části referenční dokumentace k [šabloně směrování](#route-template-reference) .

V následujícím příkladu definuje definice `{id:int}` parametru trasy [omezení trasy](#route-constraint-reference) pro parametr `id` trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Tato šablona odpovídá cestě URL, například `/Products/Details/17` ne `/Products/Details/Apples`. Omezení tras implementují <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a kontrolují hodnoty směrování a ověřují je. V tomto příkladu musí být hodnota `id` trasy převoditelná na celé číslo. Vysvětlení omezení trasy poskytovaných rozhraním naleznete v tématu [Route-Constraint-reference](#route-constraint-reference) .

Další přetížení <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> přijímají hodnoty pro `constraints`, `dataTokens`a. `defaults` Typické použití těchto parametrů je předání anonymního typu objektu, kde názvy vlastností anonymního typu odpovídají názvům parametrů tras.

Následující <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> příklady vytvoří ekvivalentní trasy:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> Vložená syntaxe pro definování omezení a výchozích hodnot může být vhodná pro jednoduché trasy. Existují však scénáře, jako jsou například datové tokeny, které nejsou podporovány vloženou syntaxí.

Následující příklad ukazuje několik dalších scénářů:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Předchozí šablona odpovídá cestě URL jako `/Blog/All-About-Routing/Introduction` a extrahuje hodnoty. `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` Výchozí hodnoty tras pro `controller` a `action` jsou vytvářeny trasou, i když v šabloně nejsou odpovídající parametry směrování. V šabloně směrování lze zadat výchozí hodnoty. Parametr `article` trasy je definován jako *catch-All* pomocí vzhledu dvojité hvězdičky (`**`) před názvem parametru trasy. Catch – všechny parametry tras zaznamenávají zbytek cesty URL a můžou taky odpovídat prázdnému řetězci.

Následující příklad přidá omezení trasy a datové tokeny:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Předchozí šablona odpovídá cestě URL jako `/en-US/Products/5` a extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` a datové tokeny. `{ locale = en-US }`

![Tokeny systému Windows pro národní prostředí](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generování adresy URL třídy směrování

<xref:Microsoft.AspNetCore.Routing.Route> Třída může také provádět generování adresy URL kombinováním sady hodnot směrování se šablonou směrování. Toto je logicky obrácený proces, který odpovídá cestě URL.

> [!TIP]
> Chcete-li lépe pochopit generování adresy URL, Představte si, jakou adresu URL chcete vygenerovat, a pak se zamyslete nad tím, jak šablona trasy odpovídá této adrese Jaké hodnoty by se vytvořily? Toto je hrubý ekvivalent způsobu, jakým generování adresy URL ve <xref:Microsoft.AspNetCore.Routing.Route> třídě funguje.

V následujícím příkladu je použita obecná výchozí trasa ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

S hodnotami `{ controller = Products, action = List }`trasy je vygenerována `/Products/List` adresa URL. Hodnoty tras se nahradí odpovídajícími parametry tras, aby bylo možné vytvořit cestu k adrese URL. Vzhledem `id` k tomu, že se jedná o volitelný parametr trasy, adresa URL se úspěšně `id`vygenerovala bez hodnoty pro.

S hodnotami `{ controller = Home, action = Index }`trasy je vygenerována `/` adresa URL. Zadané hodnoty trasy odpovídají výchozím hodnotám a jsou bezpečně vynechány segmenty odpovídající výchozím hodnotám.

Obě adresy URL vygenerovaly zpáteční cestu pomocí následující definice trasy`/Home/Index` ( `/`a) vytvoří stejné hodnoty trasy, které se použily k vygenerování adresy URL.

> [!NOTE]
> Aplikace, která používá ASP.NET Core MVC, <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> by měla používat k vygenerování adres URL namísto volání přímo do směrování.

Další informace o generování adresy URL najdete v části [Reference pro generování adresy URL](#url-generation-reference) .

## <a name="use-routing-middleware"></a>Použití middlewaru pro směrování

Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v souboru projektu aplikace.

Přidat směrování do kontejneru služby v `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

V `Startup.Configure` metodě musí být nakonfigurovány trasy. Ukázková aplikace používá následující rozhraní API:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Odpovídá pouze požadavkům HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

V následující tabulce jsou uvedeny odpovědi s danými identifikátory URI.

| Identifikátor URI                    | Odpověď                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Dobrý den! Hodnoty směrování: [operace, vytvořit], [ID, 3] |
| `/package/track/-3`    | Dobrý den! Hodnoty směrování: [operace, stopa], [ID,-3] |
| `/package/track/-3/`   | Dobrý den! Hodnoty směrování: [operace, stopa], [ID,-3] |
| `/package/track/`      | Požadavek spadá do, bez shody.              |
| `GET /hello/Joe`       | Dobrý den, Jana!                                          |
| `POST /hello/Joe`      | Požadavek spadá do, odpovídá pouze HTTP GET. |
| `GET /hello/Joe/Smith` | Požadavek spadá do, bez shody.              |

Rozhraní poskytuje sadu metod rozšíření pro vytváření tras (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

`Map[Verb]` Metody používají omezení k omezení trasy na příkaz HTTP v názvu metody. Například viz <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> a <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Odkaz na šablonu směrování

Tokeny ve složených závorkách (`{ ... }`) definují *parametry trasy* , které jsou svázané, pokud je trasa shodná. V segmentu směrování můžete definovat více než jeden parametr trasy, ale musí být oddělený literálovou hodnotou. Například `{controller=Home}{action=Index}` není platná trasa, protože hodnota literálu není mezi `{controller}` a. `{action}` Tyto parametry tras musí mít název a můžou mít zadané další atributy.

Textový literál jiný než parametry směrování (například `{id}`) a oddělovač `/` cesty musí odpovídat textu v adrese URL. U porovnávání textu se nerozlišují malá a velká písmena a na základě dekódovat reprezentace cesty URL. Chcete-li spárovat oddělovač parametrů trasy`{` (nebo `}`) literálu, zařídíte oddělovač opakováním znaku (`{{` nebo `}}`).

Vzory adres URL, které se pokoušejí zachytit název souboru s volitelnou příponou souboru, mají další požadavky. Představte si třeba šablonu `files/{filename}.{ext?}`. Pokud hodnoty pro obojí `filename` i `ext` existují, naplní se obě hodnoty. Je-li v adrese `filename` URL pouze hodnota, která je v adrese URL, odpovídá trasa, protože`.`koncová tečka () je volitelná. Tuto trasu odpovídají následujícím adresám URL:

* `/files/myFile.txt`
* `/files/myFile`

K vytvoření vazby na zbytek identifikátoru URI můžete použít hvězdičku (`*`) nebo dvojitou hvězdičku (`**`) jako předponu parametru trasy. Tyto parametry se nazývají *catch-All* . Například `blog/{**slug}` odpovídá jakémukoli identifikátoru URI, který začíná `/blog` a má libovolnou hodnotu, která je za ní přiřazena hodnota `slug` trasy. Catch – všechny parametry můžou odpovídat také prázdnému řetězci.

Parametr catch-All řídí příslušné znaky, pokud je použita cesta pro vygenerování adresy URL, včetně znaků oddělovače (`/`). Například trasa `foo/{*path}` s hodnotami `{ path = "my/path" }` trasy vygeneruje. `foo/my%2Fpath` Všimněte si řídicího znaku lomítka. Do oddělovacích znaků cesty pro přenos cest použijte předponu parametru `**` Route. Trasa `foo/{**path}` s `{ path = "my/path" }` vygeneruje `foo/my/path`.

Parametry směrování můžou mít *výchozí hodnoty* určené zadáním výchozí hodnoty za názvem parametru odděleným symbolem rovná se (`=`). Například `{controller=Home}` definuje `Home` jako výchozí hodnotu pro `controller`. Výchozí hodnota se použije v případě, že v adrese URL parametru není k dispozici žádná hodnota. Parametry směrování jsou povinny připojením otazníku (`?`) na konec názvu parametru, jako v. `id?` Rozdíl mezi volitelnými hodnotami a výchozími parametry směrování je, že parametr trasy s výchozí hodnotou vždy vytvoří hodnotu&mdash;, která má volitelný parametr hodnotu, pouze pokud je hodnota poskytnuta adresou URL požadavku.

Parametry směrování můžou mít omezení, která se musí shodovat s hodnotou trasy svázanou z adresy URL. Přidání dvojtečky (`:`) a názvu omezení za názvem parametru trasy Určuje *vložené omezení* pro parametr trasy. Pokud omezení vyžaduje argumenty, jsou uzavřeny v závorkách (`(...)`) za názvem omezení. Přidáním dalších dvojtečk (`:`) a názvu omezení lze zadat více vložených omezení.

Název omezení a argumenty jsou předány <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> službě za účelem vytvoření instance <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> pro použití při zpracování adresy URL. Například šablona `blog/{article:minlength(10)}` trasy Určuje `minlength` omezení s argumentem. `10` Další informace o omezeních tras a seznam omezení poskytovaných rozhraním najdete v části [referenční informace k omezením trasy](#route-constraint-reference) .

Parametry směrování můžou mít také transformaci parametrů, které transformují hodnotu parametru při generování odkazů a porovnání akcí a stránek s adresami URL. Podobně jako omezení můžou být transformátory parametrů přidány do parametru trasy, a to přidáním dvojtečky`:`() a názvu transformátoru za názvem parametru trasy. Například šablona `blog/{article:slugify}` trasy Určuje `slugify` transformátor. Další informace o transformačních parametrech naleznete v části [Referenční příručka pro parametry](#parameter-transformer-reference) transformátoru.

Následující tabulka ukazuje příklady šablon směrování a jejich chování.

| Šablona směrování                           | Příklad odpovídajícího identifikátoru URI    | Identifikátor URI žádosti&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Odpovídá pouze jedné cestě `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Odpovídá a nastavuje `Page` na `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Odpovídá a nastavuje `Page` na `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Provede mapování na `Products` kontroler `List` a akci.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Provede mapování na `Products` kontroler `Details` a akci`id` (nastaveno na 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Provede mapování na `Home` kontroler `Index` a metodu`id` (je ignorováno).        |

Použití šablony je obecně nejjednodušší přístup ke směrování. Omezení a výchozí hodnoty je možné zadat i mimo šablonu směrování.

> [!TIP]
> Povolte [protokolování](xref:fundamentals/logging/index) , abyste viděli <xref:Microsoft.AspNetCore.Routing.Route>, jak integrované implementace směrování, například, odpovídají požadavkům.

## <a name="reserved-routing-names"></a>Názvy rezervovaných směrování

Následující klíčová slova jsou vyhrazená jména a nelze je použít jako názvy a parametry směrování:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Odkaz na omezení trasy

Omezení trasy se spustí, když došlo ke shodě s příchozí adresou URL a cesta URL je zavedená do hodnot tras. Omezení tras obvykle kontrolují hodnotu trasy přidruženou prostřednictvím šablony trasy a učiní ano/bez rozhodnutí o tom, zda je tato hodnota přijatelná. Některá omezení tras používají data mimo hodnotu trasy k zvážení toho, zda je možné požadavek směrovat. Například <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> může přijmout nebo odmítnout požadavek na základě jeho příkazu http. Omezení se používají při směrování požadavků a vytváření propojení.

> [!WARNING]
> Nepoužívejte omezení pro **ověřování vstupu**. Pokud se pro **ověřování vstupu**používají omezení, neplatné výsledky vstupu v *404 – nenalezené* odpovědi namísto *400 – Chybný požadavek* s příslušnou chybovou zprávou. Omezení tras slouží k jednoznačnému **rozlišení podobných tras** , nikoli k ověření vstupů konkrétní trasy.

Následující tabulka ukazuje příklad omezení trasy a jejich očekávané chování.

| omezení | Příklad | Příklady shody | Poznámky |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Odpovídá libovolnému celému číslu. |
| `bool` | `{active:bool}` | `true`, `FALSE` | Odpovídá `true` nebo "false". Nerozlišuje malá a velká písmena. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platné `DateTime` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platné `decimal` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platné `double` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platné `float` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Odpovídá platné `Guid` hodnotě. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Odpovídá platné `long` hodnotě. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Řetězec musí mít minimálně 4 znaky. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Řetězec má maximálně 8 znaků. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Řetězec musí být přesně 12 znaků dlouhý. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Řetězec musí obsahovat alespoň 8 znaků a nesmí být delší než 16 znaků. |
| `min(value)` | `{age:min(18)}` | `19` | Celočíselná hodnota musí být minimálně 18. |
| `max(value)` | `{age:max(120)}` | `91` | Celočíselná hodnota je maximálně 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Celočíselná hodnota musí být minimálně 18 a maximálně 120. |
| `alpha` | `{name:alpha}` | `Rick` | Řetězec musí obsahovat jeden nebo více abecedních znaků `a` - `z`.  Nerozlišuje malá a velká písmena. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Řetězec musí odpovídat regulárnímu výrazu. Přečtěte si tipy k definování regulárního výrazu. |
| `required` | `{name:required}` | `Rick` | Slouží k vykonání, že při generování adresy URL je přítomna hodnota bez parametru. |

V jednom parametru lze použít více omezení s oddělovači. Například následující omezení omezuje parametr na celočíselnou hodnotu 1 nebo vyšší:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo `DateTime`), vždy používají invariantní jazykovou verzi. Tato omezení předpokládají, že adresa URL nelze lokalizovat. Omezení tras poskytovaných rozhraním nemění hodnoty uložené v hodnotách tras. Všechny hodnoty tras přeložené z adresy URL se ukládají jako řetězce. Například `float` omezení se pokusí převést hodnotu trasy na typ float, ale převedená hodnota se používá pouze k ověření, že je možné ji převést na typ float.

## <a name="regular-expressions"></a>Regulární výrazy

Rozhraní ASP.NET Core se přidá `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` do konstruktoru regulárního výrazu. Popis <xref:System.Text.RegularExpressions.RegexOptions> těchto členů naleznete v tématu.

Regulární výrazy používají oddělovače a tokeny podobné těm, které používá směrování a jazyk C#. Tokeny regulárního výrazu musí být uvozeny řídicími znaky. Použití regulárního výrazu `^\d{3}-\d{2}-\d{4}$` v směrování:

* Výraz musí mít jedno zpětné lomítko `\` , které je zadáno v řetězci jako Dvojitá zpětná lomítka `\\` ve zdrojovém kódu.
* Regulární výraz musí `\\` být pro řídicí znaky `\` řetězce Escape.
* Regulární výraz nepožaduje `\\` při použití [doslovnéch řetězcových literálů](/dotnet/csharp/language-reference/keywords/string).

Chcete-li `{`řídicí znaky oddělovače parametrů směrování `}`, `[` `]`,,, Zdvojnásobte znaky ve výrazu `{{`, `}` `[[`,, `]]`. V následující tabulce je uveden regulární výraz a verze s řídicím znakem:

| Regulární výraz    | Regulární výraz s řídicím znakem     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy používané ve směrování často začínají znakem stříšky `^` a odpovídají počáteční pozici řetězce. Výrazy často končí `$` znakem dolaru a odpovídají konci řetězce. Znaky `^` a `$` zajišťují, že regulární výraz odpovídá celé hodnotě parametru Route. Bez znaků `^` a `$` regulární výraz odpovídá jakémukoli podřetězci v rámci řetězce, což je často nežádoucí. Následující tabulka obsahuje příklady a vysvětlení, proč se shodují nebo neshodují.

| Expression   | Řetězec    | Shoda | Poznámka               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | MZ        | Ano   | Výraz shody    |
| `[a-z]{2}`   | MZ        | Ano   | Nerozlišuje velká a malá písmena    |
| `^[a-z]{2}$` | hello     | Ne    | Viz `^` a `$` vyšší |
| `^[a-z]{2}$` | 123abc456 | Ne    | Viz `^` a `$` vyšší |

Další informace o syntaxi regulárního výrazu naleznete v tématu [.NET Framework regulární výrazy](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr na známou sadu možných hodnot, použijte regulární výraz. `{action:regex(^(list|get|create)$)}` Například odpovídá pouze hodnotě `action` trasy `list`, `get`nebo. `create` Pokud je předána do slovníku omezení, je `^(list|get|create)$` řetězec ekvivalentní. Omezení, která jsou předána do slovníku omezení (nejsou vložena v rámci šablony), která neodpovídají jednomu ze známých omezení, jsou také považována za regulární výrazy.

## <a name="custom-route-constraints"></a>Vlastní omezení trasy

Kromě předdefinovaných omezení trasy lze vytvořit vlastní omezení trasy implementací <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozhraní. <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> Rozhraní obsahuje jedinou metodu, `Match`která vrací `true` , pokud je omezení splněno, a `false` jinak.

Pokud chcete použít vlastní <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, musí být typ omezení trasy registrovaný <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> v aplikaci v kontejneru služeb aplikace. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> Je slovník, který mapuje klíče omezení tras na <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementace, které ověřují tato omezení. Aplikace se <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> dá v `Startup.ConfigureServices` rámci služeb aktualizovat buď jako součást [služby. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) volání nebo přímou konfigurací <xref:Microsoft.AspNetCore.Routing.RouteOptions> s `services.Configure<RouteOptions>`. Příklad:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Omezení lze následně použít na trasy obvyklým způsobem pomocí názvu zadaného při registraci typu omezení. Příklad:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Odkaz na transformátor – parametr

Transformátory parametrů:

* Provede se při generování odkazu pro <xref:Microsoft.AspNetCore.Routing.Route>.
* Implementujte `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* Jsou konfigurovány <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>pomocí.
* Převeďte hodnotu trasy parametru a Transformujte ji na novou řetězcovou hodnotu.
* Výsledkem použití transformované hodnoty ve vygenerovaném odkazu.

Například vlastní `slugify` parametr Transformer ve vzoru `blog\{article:slugify}` směrování s `Url.Action(new { article = "MyTestArticle" })` vygenerováním. `blog\my-test-article`

Chcete-li použít transformující parametr ve schématu směrování, nakonfigurujte jej nejprve pomocí <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> `Startup.ConfigureServices`:

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Transformátory parametrů používá rozhraní k transformaci identifikátoru URI, kde se Endpoint vyřeší. ASP.NET Core MVC například používá transformaci parametrů k transformaci hodnoty trasy používané k `area`porovnávání, `controller`, `action`a. `page`

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

S předchozí trasou se akce `SubscriptionManagementController.GetAll` shodují s identifikátorem URI. `/subscription-management/get-all` Transformující parametr nemění hodnoty trasy použité k vygenerování odkazu. Například `Url.Action("GetAll", "SubscriptionManagement")` výstupy `/subscription-management/get-all`.

ASP.NET Core poskytuje konvence rozhraní API pro použití parametrů Transformers s vygenerovanými trasami:

* ASP.NET Core MVC má konvenci `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` rozhraní API. Tato konvence aplikuje na všechny trasy atributů v aplikaci zadaného parametru Transformer. Parametr Transformer transformuje tokeny, když jsou nahrazeny. Další informace najdete v tématu [Použití transformátoru parametrů k přizpůsobení náhrady tokenu](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages má konvence `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` rozhraní API. Tato konvence u všech automaticky zjištěných Razor Pages aplikuje zadaný transformátor parametrů. Parametr Transformer přetransformuje segmenty složky a názvu souboru na trasy Razor Pages. Další informace najdete v tématu [Použití transformátoru parametrů k přizpůsobení cest stránky](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Odkaz na generování adresy URL

Následující příklad ukazuje, jak vygenerovat odkaz na trasu s ohledem na slovník hodnot směrování a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> Vygenerovalo se na konci předchozí ukázky `/package/create/123`. Slovník poskytuje hodnoty `operation` a `id` trasu pro šablonu sledování trasy balíčku. `package/{operation}/{id}` Podrobnosti najdete v ukázkovém kódu v části [použití middleware pro směrování](#use-routing-middleware) nebo v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Druhý parametr <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> konstruktoru je kolekce *okolních hodnot*. Okolní hodnoty jsou vhodné k použití, protože omezují počet hodnot, které vývojář musí určit v rámci kontextu požadavku. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. V `About` akci aplikace ASP.NET Core MVC není nutné zadávat hodnotu `HomeController`trasy kontroléru, která bude propojena s `Index` akcí&mdash;, která `Home` je použita okolní hodnotou.

Okolní hodnoty, které se neshodují s parametrem, se ignorují. Okolní hodnoty jsou také ignorovány, pokud explicitně poskytnutá hodnota Přepisuje hodnotu okolí. K shodě dojde zleva doprava v adrese URL.

Hodnoty jsou výslovně poskytnuty, ale neodpovídají segmentu trasy, jsou přidány do řetězce dotazu. V následující tabulce je uveden výsledek při použití šablony `{controller}/{action}/{id?}`směrování.

| Okolní hodnoty                     | Explicitní hodnoty                        | Výsledek                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| Controller = "domů"                | Action = "o"                       | `/Home/About`           |
| Controller = "domů"                | Controller = "objednávka"; Action = "o" | `/Order/About`          |
| Controller = "Home"; Color = "Red" | Action = "o"                       | `/Home/About`           |
| Controller = "domů"                | Action = "o", Color = "Red"        | `/Home/About?color=Red` |

Pokud má trasa výchozí hodnotu, která neodpovídá parametru a tato hodnota je explicitně poskytnutá, musí se shodovat s výchozí hodnotou:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Generace odkazů generuje odkaz pro tuto trasu v případě, že jsou k `controller` dispozici hodnoty pro a `action` , které odpovídají.

## <a name="complex-segments"></a>Komplexní segmenty

Komplexní segmenty (například `[Route("/x{token}y")]`) jsou zpracovávány porovnáním koncových literálů zprava doleva nehladým způsobem. Podrobné vysvětlení, jak se shodují komplexní segmenty, najdete v [tomto kódu](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) . [Ukázka kódu](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) není používána ASP.NET Core, ale poskytuje dobré vysvětlení složitých segmentů.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Směrování zodpovídá za mapování identifikátorů URI požadavků na obslužné rutiny směrování a odesílání příchozích požadavků. Trasy jsou v aplikaci definované a nakonfigurované při spuštění aplikace. Trasa může volitelně extrahovat hodnoty z adresy URL obsažené v žádosti a tyto hodnoty pak lze použít pro zpracování požadavků. Směrování pomocí nakonfigurovaných tras z aplikace dokáže vygenerovat adresy URL, které se mapují na obslužné rutiny tras.

Pokud chcete použít nejnovější scénáře směrování v ASP.NET Core 2,1, zadejte [verzi kompatibility](xref:mvc/compatibility-version) pro registraci služby MVC v `Startup.ConfigureServices`těchto umístěních:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Tento dokument popisuje směrování ASP.NET Core nízké úrovně. Informace o ASP.NET Core směrování MVC najdete v tématu <xref:mvc/controllers/routing>. Informace o konvencích směrování v Razor Pages najdete v <xref:razor-pages/razor-pages-conventions>tématu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Základy směrování

Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné. Výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}`:

* Podporuje základní a popisné schéma směrování.
* Je užitečným výchozím bodem pro aplikace založené na uživatelském rozhraní.

Vývojáři obvykle přidávají další stručný trasy do oblastí s vysokým provozem v aplikaci ve zvláštních situacích (například koncové body blogu a elektronického obchodování) pomocí [Směrování atributů](xref:mvc/controllers/routing#attribute-routing) nebo vyhrazených konvenčních tras.

Webové rozhraní API by mělo používat směrování atributů k modelování funkcí aplikace jako sady prostředků, ve kterých jsou operace reprezentované příkazy HTTP. To znamená, že mnoho operací (například GET, POST) na stejném logickém prostředku bude používat stejnou adresu URL. Směrování atributů poskytuje úroveň řízení, která je nutná k pečlivému návrhu rozložení veřejného koncového bodu rozhraní API.

Aplikace Razor Pages používají výchozí konvenční směrování pro obsluhu pojmenovaných prostředků ve složce *Pages* v aplikaci. K dispozici jsou další konvence, které vám umožní přizpůsobit Razor Pages chování směrování. Další informace naleznete v tématech <xref:razor-pages/index> a <xref:razor-pages/razor-pages-conventions>.

Podpora generování adresy URL umožňuje, aby se aplikace vyvinula bez adres URL s pevným kódováním, aby bylo možné propojit aplikaci dohromady. Tato podpora umožňuje začít se základní konfigurací směrování a upravovat trasy po určení rozložení prostředků aplikace.

Směrování používá implementace směrování <xref:Microsoft.AspNetCore.Routing.IRouter> pro:

* Mapování příchozích požadavků na *obslužné rutiny tras*
* Vygenerujte adresy URL používané v odpovědích.

Ve výchozím nastavení má aplikace jednu kolekci tras. Po doručení žádosti jsou trasy v kolekci zpracovávány v pořadí, v jakém existují v kolekci. Rozhraní se pokusí porovnat adresu URL příchozího požadavku s trasou v kolekci voláním <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> metody v každé trase v kolekci. Odpověď může používat směrování k vygenerování adres URL (například pro přesměrování nebo propojení) na základě informací o trasách, takže se vyhnete pevně zakódovaným adresám URL, které pomáhají zachovat.

Systém směrování má následující vlastnosti:

* Syntaxe šablony směrování se používá k definování tras s tokeny parametrů trasy.
* Konfigurace koncového bodu stylů a stylu atributu je povolena.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>slouží k určení, zda parametr adresy URL obsahuje platnou hodnotu pro dané omezení koncového bodu.
* Modely aplikací, jako je MVC/Razor Pages, registrují všechny své trasy, které mají předvídatelné implementaci scénářů směrování.
* Odpověď může používat směrování k vygenerování adres URL (například pro přesměrování nebo propojení) na základě informací o trasách, takže se vyhnete pevně zakódovaným adresám URL, které pomáhají zachovat.
* Generování adresy URL vychází z tras, které podporují libovolné rozšíření. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>nabízí metody pro sestavování adres URL.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
Směrování je k kanálu [middleware](xref:fundamentals/middleware/index) připojeno <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> třídou. [ASP.NET Core MVC](xref:mvc/overview) v rámci své konfigurace přidává směrování do kanálu middlewaru a zpracovává směrování v MVC a Razor Pages aplikacích. Informace o tom, jak používat směrování jako samostatnou součást, najdete v části [použití middlewaru pro směrování](#use-routing-middleware) .

### <a name="url-matching"></a>Shoda adresy URL

Shoda adresy URL je proces, podle kterého směrování odesílá příchozí požadavek *obslužné rutině*. Tento proces je založený na datech v cestě URL, ale dá se rozšířit, aby v žádosti mohla být považovat všechna data. Schopnost odesílat žádosti na samostatné obslužné rutiny je klíč pro škálování velikosti a složitosti aplikace.

Příchozí požadavky vstupují do <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, který volá <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> metodu v každé trase v sekvenci. Instance zvolí, zda má být *žádost zpracována* nastavením [RouteContext. Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) na jinou hodnotu než null <xref:Microsoft.AspNetCore.Http.RequestDelegate> <xref:Microsoft.AspNetCore.Routing.IRouter> Pokud trasa nastaví obslužnou rutinu pro požadavek, zpracování směrování se zastaví a obslužná rutina se vyvolá pro zpracování žádosti. Pokud se pro zpracování požadavku nenajde žádná obslužná rutina tras, middleware si požadavek doplní k dalšímu middlewaru v kanálu žádosti.

Primární vstup na <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> je [vlastnost RouteContext. HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) přidružená k aktuální žádosti. [RouteContext. Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) a [RouteContext. parametr RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) jsou nastaveny výstupy po porovnání trasy.

Shoda, která volá <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> , také nastaví vlastnosti [RouteContext. parametr RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) na příslušné hodnoty na základě dosud provedeného zpracování požadavků.

[Parametr RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) je slovník *hodnot tras* vytvořených z trasy. Tyto hodnoty se obvykle určují pomocí tokenizací adresy URL a dají se použít k přijetí vstupu uživatele nebo k dalšímu odesílání rozhodnutí v rámci aplikace.

[Parametr RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) je kontejner objektů a dat pro další data související s odpovídající trasou. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>jsou k dispozici pro podporu přidružování dat o stavu k jednotlivým cestám, aby aplikace mohla učinit rozhodnutí na základě toho, na které trase odpovídá. Tyto hodnoty jsou definované vývojářem a **neovlivňují chování** směrování jakýmkoli způsobem. Kromě toho hodnoty dočasně ukládané v [parametr RouteData. Datatokeny](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) můžou být libovolného typu, na rozdíl od [parametr RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), které musí být převoditelné na a z řetězců.

[Parametr RouteData. routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) je seznam tras, které byly součástí úspěšného porovnání požadavku. Trasy mohou být vnořeny do sebe navzájem. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> Vlastnost odráží cestu v logickém stromu tras, jejichž výsledkem byla shoda. Obecně platí, že první položka <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> v nástroji je kolekce tras a měla by se používat pro generování adresy URL. Poslední položka v <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> je obslužná rutina trasy, která se shoduje.

<a name="lg"></a>

### <a name="url-generation"></a>Generování adresy URL

Generování adresy URL je proces, podle kterého směrování může vytvořit cestu adresy URL na základě sady hodnot tras. To umožňuje logické oddělení mezi obslužnými rutinami tras a adresami URL, které k nim mají přístup.

Generování adresy URL následuje po podobném iterativním procesu, ale začíná kódem uživatele nebo rozhraní, který <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> volá metodu kolekce tras. Každá *trasa* má svou <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> metodu volanou v sekvenci, dokud se <xref:Microsoft.AspNetCore.Routing.VirtualPathData> nevrátí hodnota, která není null.

Primární vstupy <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> jsou:

* [VirtualPathContext. HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext. Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Trasy primárně využívají hodnoty tras poskytované nástrojem <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> a <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> k rozhodnutí, zda je možné vygenerovat adresu URL a jaké hodnoty mají být zahrnuty. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> Je sada hodnot tras, které byly vytvořeny z porovnání s aktuálním požadavkem. Naproti tomu <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> jsou hodnoty trasy, které určují, jak se má vygenerovat požadovaná adresa URL pro aktuální operaci. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> Je k dispozici v případě, že trasa má získat služby nebo další data přidružená k aktuálnímu kontextu.

> [!TIP]
> [VirtualPathContext. Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) se považuje za sadu přepsání pro [VirtualPathContext. AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). Generování adresy URL se pokusí znovu použít hodnoty směrování z aktuální žádosti, aby se vygenerovaly adresy URL pro odkazy pomocí stejné trasy nebo hodnoty tras.

Výstupem <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> je <xref:Microsoft.AspNetCore.Routing.VirtualPathData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData>je paralelní z <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData><xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> obsahuje adresu URL výstupu a některé další vlastnosti, které by měly být nastavené trasou.

Vlastnost [VirtualPathData. VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) obsahuje *virtuální cestu* vytvořenou trasou. V závislosti na vašich potřebách možná budete muset zpracovat cestu dále. Pokud chcete vygenerovanou adresu URL vykreslit ve formátu HTML, předřaďte základní cestu aplikace.

[VirtualPathData. router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) je odkaz na trasu, která adresu URL úspěšně vygenerovala.

Vlastnosti [VirtualPathData. DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) je slovník dalších dat souvisejících s trasou, která adresu URL vygenerovala. To je paralelní pro [parametr RouteData. Datatokeny](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Vytvoření tras

Směrování poskytuje <xref:Microsoft.AspNetCore.Routing.Route> třídu jako standardní implementaci <xref:Microsoft.AspNetCore.Routing.IRouter>. <xref:Microsoft.AspNetCore.Routing.Route>používá syntaxi *šablony směrování* k definování vzorů, které se budou shodovat s cestou <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> URL, když je volána. <xref:Microsoft.AspNetCore.Routing.Route>používá stejnou šablonu trasy k vygenerování adresy URL, <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> když se zavolá.

Většina aplikací vytváří trasy voláním <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> nebo jedné z podobných metod rozšíření definovaných v <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Kterákoli z <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> rozšiřujících metod vytvoří instanci <xref:Microsoft.AspNetCore.Routing.Route> a přidá ji do kolekce tras.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nepřijímá parametr obslužné rutiny trasy. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>přidá pouze trasy, které jsou zpracovávány <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Výchozí obslužná rutina je `IRouter`a obslužná rutina nemusí požadavek zpracovat. Například ASP.NET Core MVC je obvykle nakonfigurován jako výchozí obslužná rutina, která zpracovává pouze požadavky, které odpovídají dostupnému kontroleru a akci. Další informace o směrování v MVC najdete v tématu <xref:mvc/controllers/routing>.

Následující příklad kódu je příkladem <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> volání využívaného typickou ASP.NET Core definice trasy MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Tato šablona odpovídá cestě URL a extrahuje hodnoty tras. Například cesta `/Products/Details/17` generuje následující hodnoty trasy: `{ controller = Products, action = Details, id = 17 }`.

Hodnoty tras se určují rozdělením cesty URL na segmenty a porovnáním jednotlivých segmentů s názvem *parametru trasy* v šabloně směrování. Parametry směrování jsou pojmenovány. Parametry definované ohraničujícím název parametru ve složených závorkách `{ ... }`.

Předchozí šablona může také odpovídat cestě `/` URL a vydávat hodnoty. `{ controller = Home, action = Index }` K tomu dochází, `{controller}` protože `{action}` parametry směrování a mají výchozí hodnoty a `id` parametr trasy je nepovinný. Znak rovná se (`=`) následovaný hodnotou po názvu parametru trasy, který definuje výchozí hodnotu parametru. Otazník (`?`) po názvu parametru trasy definuje volitelný parametr.

Parametry směrování s výchozí hodnotou *vždy* vytvoří hodnotu trasy, když odpovídá trasa. Pokud neexistuje žádný odpovídající segment cesty k adrese URL, volitelné parametry nevytvoří hodnotu trasy. Podrobný popis scénářů a syntaxe šablon směrování najdete v části referenční dokumentace k [šabloně směrování](#route-template-reference) .

V následujícím příkladu definuje definice `{id:int}` parametru trasy [omezení trasy](#route-constraint-reference) pro parametr `id` trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Tato šablona odpovídá cestě URL, například `/Products/Details/17` ne `/Products/Details/Apples`. Omezení tras implementují <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a kontrolují hodnoty směrování a ověřují je. V tomto příkladu musí být hodnota `id` trasy převoditelná na celé číslo. Vysvětlení omezení trasy poskytovaných rozhraním naleznete v tématu [Route-Constraint-reference](#route-constraint-reference) .

Další přetížení <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> přijímají hodnoty pro `constraints`, `dataTokens`a. `defaults` Typické použití těchto parametrů je předání anonymního typu objektu, kde názvy vlastností anonymního typu odpovídají názvům parametrů tras.

Následující <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> příklady vytvoří ekvivalentní trasy:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> Vložená syntaxe pro definování omezení a výchozích hodnot může být vhodná pro jednoduché trasy. Existují však scénáře, jako jsou například datové tokeny, které nejsou podporovány vloženou syntaxí.

Následující příklad ukazuje několik dalších scénářů:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Předchozí šablona odpovídá cestě URL jako `/Blog/All-About-Routing/Introduction` a extrahuje hodnoty. `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` Výchozí hodnoty tras pro `controller` a `action` jsou vytvářeny trasou, i když v šabloně nejsou odpovídající parametry směrování. V šabloně směrování lze zadat výchozí hodnoty. Parametr `article` Route je definován jako *catch-All* pomocí vzhledu hvězdičky (`*`) před názvem parametru trasy. Catch – všechny parametry tras zaznamenávají zbytek cesty URL a můžou taky odpovídat prázdnému řetězci.

Následující příklad přidá omezení trasy a datové tokeny:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Předchozí šablona odpovídá cestě URL jako `/en-US/Products/5` a extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` a datové tokeny. `{ locale = en-US }`

![Tokeny systému Windows pro národní prostředí](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generování adresy URL třídy směrování

<xref:Microsoft.AspNetCore.Routing.Route> Třída může také provádět generování adresy URL kombinováním sady hodnot směrování se šablonou směrování. Toto je logicky obrácený proces, který odpovídá cestě URL.

> [!TIP]
> Chcete-li lépe pochopit generování adresy URL, Představte si, jakou adresu URL chcete vygenerovat, a pak se zamyslete nad tím, jak šablona trasy odpovídá této adrese Jaké hodnoty by se vytvořily? Toto je hrubý ekvivalent způsobu, jakým generování adresy URL ve <xref:Microsoft.AspNetCore.Routing.Route> třídě funguje.

V následujícím příkladu je použita obecná výchozí trasa ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

S hodnotami `{ controller = Products, action = List }`trasy je vygenerována `/Products/List` adresa URL. Hodnoty tras se nahradí odpovídajícími parametry tras, aby bylo možné vytvořit cestu k adrese URL. Vzhledem `id` k tomu, že se jedná o volitelný parametr trasy, adresa URL se úspěšně `id`vygenerovala bez hodnoty pro.

S hodnotami `{ controller = Home, action = Index }`trasy je vygenerována `/` adresa URL. Zadané hodnoty trasy odpovídají výchozím hodnotám a jsou bezpečně vynechány segmenty odpovídající výchozím hodnotám.

Obě adresy URL vygenerovaly zpáteční cestu pomocí následující definice trasy`/Home/Index` ( `/`a) vytvoří stejné hodnoty trasy, které se použily k vygenerování adresy URL.

> [!NOTE]
> Aplikace, která používá ASP.NET Core MVC, <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> by měla používat k vygenerování adres URL namísto volání přímo do směrování.

Další informace o generování adresy URL najdete v části [Reference pro generování adresy URL](#url-generation-reference) .

## <a name="use-routing-middleware"></a>Použití middlewaru pro směrování

Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v souboru projektu aplikace.

Přidat směrování do kontejneru služby v `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

V `Startup.Configure` metodě musí být nakonfigurovány trasy. Ukázková aplikace používá následující rozhraní API:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Odpovídá pouze požadavkům HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

V následující tabulce jsou uvedeny odpovědi s danými identifikátory URI.

| Identifikátor URI                    | Odpověď                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Dobrý den! Hodnoty směrování: [operace, vytvořit], [ID, 3] |
| `/package/track/-3`    | Dobrý den! Hodnoty směrování: [operace, stopa], [ID,-3] |
| `/package/track/-3/`   | Dobrý den! Hodnoty směrování: [operace, stopa], [ID,-3] |
| `/package/track/`      | Požadavek spadá do, bez shody.              |
| `GET /hello/Joe`       | Dobrý den, Jana!                                          |
| `POST /hello/Joe`      | Požadavek spadá do, odpovídá pouze HTTP GET. |
| `GET /hello/Joe/Smith` | Požadavek spadá do, bez shody.              |

Pokud konfigurujete jednu trasu, zavolejte <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> předávání do `IRouter` instance. Nebudete muset používat <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

Rozhraní poskytuje sadu metod rozšíření pro vytváření tras (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Některé z uvedených metod, například <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, vyžadují. <xref:Microsoft.AspNetCore.Http.RequestDelegate> <xref:Microsoft.AspNetCore.Http.RequestDelegate> Je použit jako *obslužná rutina trasy* při porovnání trasy. Jiné metody v této rodině umožňují konfigurovat kanál middlewaru pro použití jako obslužná rutina trasy. Pokud `Map*` metoda nepřijímá obslužnou rutinu, například <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, používá. <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>

`Map[Verb]` Metody používají omezení k omezení trasy na příkaz HTTP v názvu metody. Například viz <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> a <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Odkaz na šablonu směrování

Tokeny ve složených závorkách (`{ ... }`) definují *parametry trasy* , které jsou svázané, pokud je trasa shodná. V segmentu směrování můžete definovat více než jeden parametr trasy, ale musí být oddělený literálovou hodnotou. Například `{controller=Home}{action=Index}` není platná trasa, protože hodnota literálu není mezi `{controller}` a. `{action}` Tyto parametry tras musí mít název a můžou mít zadané další atributy.

Textový literál jiný než parametry směrování (například `{id}`) a oddělovač `/` cesty musí odpovídat textu v adrese URL. U porovnávání textu se nerozlišují malá a velká písmena a na základě dekódovat reprezentace cesty URL. Chcete-li spárovat oddělovač parametrů trasy`{` (nebo `}`) literálu, zařídíte oddělovač opakováním znaku (`{{` nebo `}}`).

Vzory adres URL, které se pokoušejí zachytit název souboru s volitelnou příponou souboru, mají další požadavky. Představte si třeba šablonu `files/{filename}.{ext?}`. Pokud hodnoty pro obojí `filename` i `ext` existují, naplní se obě hodnoty. Je-li v adrese `filename` URL pouze hodnota, která je v adrese URL, odpovídá trasa, protože`.`koncová tečka () je volitelná. Tuto trasu odpovídají následujícím adresám URL:

* `/files/myFile.txt`
* `/files/myFile`

K vytvoření vazby na zbytek identifikátoru URI můžete použít hvězdičku (`*`) jako předponu parametru trasy. Tento parametr se nazývá *catch-All* . Například `blog/{*slug}` odpovídá jakémukoli identifikátoru URI, který začíná `/blog` a má libovolnou hodnotu, která je za ní přiřazena hodnota `slug` trasy. Catch – všechny parametry můžou odpovídat také prázdnému řetězci.

Parametr catch-All řídí příslušné znaky, pokud je použita cesta pro vygenerování adresy URL, včetně znaků oddělovače (`/`). Například trasa `foo/{*path}` s hodnotami `{ path = "my/path" }` trasy vygeneruje. `foo/my%2Fpath` Všimněte si řídicího znaku lomítka.

Parametry směrování můžou mít *výchozí hodnoty* určené zadáním výchozí hodnoty za názvem parametru odděleným symbolem rovná se (`=`). Například `{controller=Home}` definuje `Home` jako výchozí hodnotu pro `controller`. Výchozí hodnota se použije v případě, že v adrese URL parametru není k dispozici žádná hodnota. Parametry směrování jsou povinny připojením otazníku (`?`) na konec názvu parametru, jako v. `id?` Rozdíl mezi volitelnými hodnotami a výchozími parametry směrování je, že parametr trasy s výchozí hodnotou vždy vytvoří hodnotu&mdash;, která má volitelný parametr hodnotu, pouze pokud je hodnota poskytnuta adresou URL požadavku.

Parametry směrování můžou mít omezení, která se musí shodovat s hodnotou trasy svázanou z adresy URL. Přidání dvojtečky (`:`) a názvu omezení za názvem parametru trasy Určuje *vložené omezení* pro parametr trasy. Pokud omezení vyžaduje argumenty, jsou uzavřeny v závorkách (`(...)`) za názvem omezení. Přidáním dalších dvojtečk (`:`) a názvu omezení lze zadat více vložených omezení.

Název omezení a argumenty jsou předány <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> službě za účelem vytvoření instance <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> pro použití při zpracování adresy URL. Například šablona `blog/{article:minlength(10)}` trasy Určuje `minlength` omezení s argumentem. `10` Další informace o omezeních tras a seznam omezení poskytovaných rozhraním najdete v části [referenční informace k omezením trasy](#route-constraint-reference) .

Následující tabulka ukazuje příklady šablon směrování a jejich chování.

| Šablona směrování                           | Příklad odpovídajícího identifikátoru URI    | Identifikátor URI žádosti&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Odpovídá pouze jedné cestě `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Odpovídá a nastavuje `Page` na `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Odpovídá a nastavuje `Page` na `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Provede mapování na `Products` kontroler `List` a akci.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Provede mapování na `Products` kontroler `Details` a akci`id` (nastaveno na 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Provede mapování na `Home` kontroler `Index` a metodu`id` (je ignorováno).        |

Použití šablony je obecně nejjednodušší přístup ke směrování. Omezení a výchozí hodnoty je možné zadat i mimo šablonu směrování.

> [!TIP]
> Povolte [protokolování](xref:fundamentals/logging/index) , abyste viděli <xref:Microsoft.AspNetCore.Routing.Route>, jak integrované implementace směrování, například, odpovídají požadavkům.

## <a name="route-constraint-reference"></a>Odkaz na omezení trasy

Omezení trasy se spustí, když došlo ke shodě s příchozí adresou URL a cesta URL je zavedená do hodnot tras. Omezení tras obvykle kontrolují hodnotu trasy přidruženou prostřednictvím šablony trasy a učiní ano/bez rozhodnutí o tom, zda je tato hodnota přijatelná. Některá omezení tras používají data mimo hodnotu trasy k zvážení toho, zda je možné požadavek směrovat. Například <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> může přijmout nebo odmítnout požadavek na základě jeho příkazu http. Omezení se používají při směrování požadavků a vytváření propojení.

> [!WARNING]
> Nepoužívejte omezení pro **ověřování vstupu**. Pokud se pro **ověřování vstupu**používají omezení, neplatné výsledky vstupu v *404 – nenalezené* odpovědi namísto *400 – Chybný požadavek* s příslušnou chybovou zprávou. Omezení tras slouží k jednoznačnému **rozlišení podobných tras** , nikoli k ověření vstupů konkrétní trasy.

Následující tabulka ukazuje příklad omezení trasy a jejich očekávané chování.

| omezení | Příklad | Příklady shody | Poznámky |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Odpovídá jakémukoli celému číslu |
| `bool` | `{active:bool}` | `true`, `FALSE` | Odpovídá `true` nebo `false` (nerozlišuje velká a malá písmena) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platné `DateTime` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platné `decimal` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platné `double` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platné `float` hodnotě v neutrální jazykové verzi. Viz předchozí upozornění.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Odpovídá platné `Guid` hodnotě |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Odpovídá platné `long` hodnotě |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Řetězec musí mít minimálně 4 znaky. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Řetězec nesmí být delší než 8 znaků. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Řetězec musí být přesně 12 znaků dlouhý. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Řetězec musí mít aspoň 8 znaků a nesmí být delší než 16 znaků. |
| `min(value)` | `{age:min(18)}` | `19` | Celočíselná hodnota musí být minimálně 18. |
| `max(value)` | `{age:max(120)}` | `91` | Hodnota typu Integer nesmí být větší než 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Celočíselná hodnota musí být minimálně 18, ale ne víc než 120. |
| `alpha` | `{name:alpha}` | `Rick` | Řetězec musí obsahovat jeden nebo více abecedních znaků (`a`-`z`bez rozlišování velkých a malých písmen). |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Řetězec musí odpovídat regulárnímu výrazu (viz Tipy k definování regulárního výrazu). |
| `required` | `{name:required}` | `Rick` | Slouží k vykonání, že při generování adresy URL je přítomna hodnota bez parametru. |

V jednom parametru lze použít více omezení s oddělovači. Například následující omezení omezuje parametr na celočíselnou hodnotu 1 nebo vyšší:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo `DateTime`), vždy používají invariantní jazykovou verzi. Tato omezení předpokládají, že adresa URL nelze lokalizovat. Omezení tras poskytovaných rozhraním nemění hodnoty uložené v hodnotách tras. Všechny hodnoty tras přeložené z adresy URL se ukládají jako řetězce. Například `float` omezení se pokusí převést hodnotu trasy na typ float, ale převedená hodnota se používá pouze k ověření, že je možné ji převést na typ float.

## <a name="regular-expressions"></a>Regulární výrazy

Rozhraní ASP.NET Core se přidá `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` do konstruktoru regulárního výrazu. Popis <xref:System.Text.RegularExpressions.RegexOptions> těchto členů naleznete v tématu.

Regulární výrazy používají oddělovače a tokeny podobné těm, které používá směrování a jazyk C#. Tokeny regulárního výrazu musí být uvozeny řídicími znaky. Chcete-li použít regulární `^\d{3}-\d{2}-\d{4}$` výraz ve směrování, musí mít `\` výraz (jedno zpětné lomítko), které je zadáno v řetězci `\\` jako (dvojité zpětné lomítko) ve zdrojovém souboru C#, aby bylo možné `\` řídicí znak řetězce Escape řídicího znaku (Pokud se nepoužívají [doslovné řetězce literálů](/dotnet/csharp/language-reference/keywords/string)). Chcete-li řídicí znaky oddělovače parametrů směrování`{`( `}`, `[`, `]`,), poklikejte na znaky ve výrazu (`{{`, `}`, `[[`, `]]`). V následující tabulce je uveden regulární výraz a verze s řídicím znakem.

| Regulární výraz    | Regulární výraz s řídicím znakem     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy používané v směrování často začínají znakem stříšky`^`() a odpovídají počáteční pozici řetězce. Výrazy se často končí znakem dolaru (`$`) a koncem řetězce. Znaky `^` a `$` zajišťují, že regulární výraz odpovídá celé hodnotě parametru Route. Bez znaků `^` a `$` regulární výraz odpovídá jakémukoli podřetězci v rámci řetězce, což je často nežádoucí. Následující tabulka obsahuje příklady a vysvětlení, proč se shodují nebo neshodují.

| Expression   | Řetězec    | Shoda | Poznámka               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | MZ        | Ano   | Výraz shody    |
| `[a-z]{2}`   | MZ        | Ano   | Nerozlišuje velká a malá písmena    |
| `^[a-z]{2}$` | hello     | Ne    | Viz `^` a `$` vyšší |
| `^[a-z]{2}$` | 123abc456 | Ne    | Viz `^` a `$` vyšší |

Další informace o syntaxi regulárního výrazu naleznete v tématu [.NET Framework regulární výrazy](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr na známou sadu možných hodnot, použijte regulární výraz. `{action:regex(^(list|get|create)$)}` Například odpovídá pouze hodnotě `action` trasy `list`, `get`nebo. `create` Pokud je předána do slovníku omezení, je `^(list|get|create)$` řetězec ekvivalentní. Omezení, která jsou předána do slovníku omezení (nejsou vložena v rámci šablony), která neodpovídají jednomu ze známých omezení, jsou také považována za regulární výrazy.

## <a name="custom-route-constraints"></a>Vlastní omezení trasy

Kromě předdefinovaných omezení trasy lze vytvořit vlastní omezení trasy implementací <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozhraní. <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> Rozhraní obsahuje jedinou metodu, `Match`která vrací `true` , pokud je omezení splněno, a `false` jinak.

Pokud chcete použít vlastní <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, musí být typ omezení trasy registrovaný <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> v aplikaci v kontejneru služeb aplikace. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> Je slovník, který mapuje klíče omezení tras na <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementace, které ověřují tato omezení. Aplikace se <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> dá v `Startup.ConfigureServices` rámci služeb aktualizovat buď jako součást [služby. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) volání nebo přímou konfigurací <xref:Microsoft.AspNetCore.Routing.RouteOptions> s `services.Configure<RouteOptions>`. Příklad:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Omezení lze následně použít na trasy obvyklým způsobem pomocí názvu zadaného při registraci typu omezení. Příklad:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>Odkaz na generování adresy URL

Následující příklad ukazuje, jak vygenerovat odkaz na trasu s ohledem na slovník hodnot směrování a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> Vygenerovalo se na konci předchozí ukázky `/package/create/123`. Slovník poskytuje hodnoty `operation` a `id` trasu pro šablonu sledování trasy balíčku. `package/{operation}/{id}` Podrobnosti najdete v ukázkovém kódu v části [použití middleware pro směrování](#use-routing-middleware) nebo v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Druhý parametr <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> konstruktoru je kolekce *okolních hodnot*. Okolní hodnoty jsou vhodné k použití, protože omezují počet hodnot, které vývojář musí určit v rámci kontextu požadavku. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. V `About` akci aplikace ASP.NET Core MVC není nutné zadávat hodnotu `HomeController`trasy kontroléru, která bude propojena s `Index` akcí&mdash;, která `Home` je použita okolní hodnotou.

Okolní hodnoty, které se neshodují s parametrem, se ignorují. Okolní hodnoty jsou také ignorovány, pokud explicitně poskytnutá hodnota Přepisuje hodnotu okolí. K shodě dojde zleva doprava v adrese URL.

Hodnoty jsou výslovně poskytnuty, ale neodpovídají segmentu trasy, jsou přidány do řetězce dotazu. V následující tabulce je uveden výsledek při použití šablony `{controller}/{action}/{id?}`směrování.

| Okolní hodnoty                     | Explicitní hodnoty                        | Výsledek                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| Controller = "domů"                | Action = "o"                       | `/Home/About`           |
| Controller = "domů"                | Controller = "objednávka"; Action = "o" | `/Order/About`          |
| Controller = "Home"; Color = "Red" | Action = "o"                       | `/Home/About`           |
| Controller = "domů"                | Action = "o", Color = "Red"        | `/Home/About?color=Red` |

Pokud má trasa výchozí hodnotu, která neodpovídá parametru a tato hodnota je explicitně poskytnutá, musí se shodovat s výchozí hodnotou:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Generace odkazů generuje odkaz pro tuto trasu v případě, že jsou k `controller` dispozici hodnoty pro a `action` , které odpovídají.

## <a name="complex-segments"></a>Komplexní segmenty

Komplexní segmenty (například `[Route("/x{token}y")]`) jsou zpracovávány porovnáním koncových literálů zprava doleva nehladým způsobem. Podrobné vysvětlení, jak se shodují komplexní segmenty, najdete v [tomto kódu](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) . [Ukázka kódu](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) není používána ASP.NET Core, ale poskytuje dobré vysvětlení složitých segmentů.

::: moniker-end
