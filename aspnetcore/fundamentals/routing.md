---
title: Směrování v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak ASP.NET základní směrování je zodpovědný za odpovídající požadavky HTTP a odeslání spustitelných koncových bodů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
uid: fundamentals/routing
ms.openlocfilehash: 5742ac6879ce46e01247ddd2f8bfe3e3b8a2a02a
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751157"
---
# <a name="routing-in-aspnet-core"></a>Směrování v ASP.NET jádru

[Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Směrování je zodpovědný za odpovídající příchozí požadavky HTTP a odeslání těchto požadavků do koncových bodů spustitelné aplikace. [Koncové body](#endpoint) jsou jednotky kódu zpracování spustitelných požadavků. Koncové body jsou definovány v aplikaci a nakonfigurovány při spuštění aplikace. Proces porovnávání koncových bodů můžete extrahovat hodnoty z adresy URL požadavku a poskytnout tyto hodnoty pro zpracování požadavku. Pomocí informací o koncovém bodu z aplikace směrování je také možné generovat adresy URL, které mapují koncové body.

Aplikace mohou konfigurovat směrování pomocí:

- Kontrolery
- Razor Pages
- SignalR
- gRPC Služby
- [Middleware](xref:fundamentals/middleware/index) s povoleným koncovým bodem, například [kontroly stavu](xref:host-and-deploy/health-checks).
- Delegáti a lambdas registrováni s směrováním.

Tento dokument popisuje podrobnosti nízké úrovně ASP.NET směrování Core. Informace o konfiguraci směrování:

* Informace o řadičích naleznete v tématu <xref:mvc/controllers/routing>.
* Konvence Razor Pages najdete v tématu <xref:razor-pages/razor-pages-conventions>.

Systém směrování koncových bodů popsaný v tomto dokumentu se vztahuje na ASP.NET jádrem 3.0 a novějším. Informace o předchozím systému <xref:Microsoft.AspNetCore.Routing.IRouter>směrování založeném na písmenu a) vyberte ASP.NET verzi Core 2.1 pomocí jednoho z následujících postupů:

* Volič verze pro předchozí verzi.
* Vyberte [ASP.NET směrování Jádra 2.1](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázky stahování pro tento dokument `Startup` jsou povoleny určitou třídou. Chcete-li spustit konkrétní *Program.cs* ukázku, `Startup` upravte Program.cs volat požadovanou třídu.

## <a name="routing-basics"></a>Základy směrování

Všechny ASP.NET základní šablony zahrnují směrování v generovaném kódu. Směrování je registrováno v `Startup.Configure` [kanálu middleware](xref:fundamentals/middleware/index) v .

Následující kód ukazuje základní příklad směrování:

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Směrování používá dvojici middleware, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>registrovaných a:

* `UseRouting`přidá odpovídající trasu do kanálu middlewaru. Tento middleware se dívá na sadu koncových bodů definovaných v aplikaci a vybere [nejlepší shodu](#urlm) na základě požadavku.
* `UseEndpoints`přidá spuštění koncového bodu do kanálu middlewaru. Spustí delegáta přidruženého k vybranému koncovému bodu.

Předchozí příklad obsahuje jednu *trasu ke koncovému* bodu kódu pomocí metody [MapGet:](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*)

* Při odeslání `GET` požadavku HTTP na `/`kořenovou adresu URL :
  * Delegát požadavku zobrazí provede.
  * `Hello World!`je zapsána do odpovědi HTTP. Ve výchozím nastavení `/` je `https://localhost:5001/`kořenová adresa URL .
* Pokud metoda požadavku `GET` není nebo kořenová `/`adresa URL není , není vrácena žádná trasa a je vrácena metoda HTTP 404.

### <a name="endpoint"></a>Koncový bod

<a name="endpoint"></a>

Metoda `MapGet` se používá k definování **koncového bodu**. Koncový bod je něco, co může být:

* Vybráno porovnáním adresy URL a metody HTTP.
* Spuštěno spuštěním delegáta.

Koncové body, které mohou být spárovány a `UseEndpoints`provedeny aplikací, jsou nakonfigurovány v aplikaci . Například <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, a [podobné metody](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) připojit delegáty požadavků do systému směrování.
Další metody lze použít k připojení ASP.NET funkce core framework u systému směrování:
- [MapRazorPages pro Razor Stránky](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [MapControllers pro řadiče](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub\<THub> pro SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService\<TService> pro gRPC](xref:grpc/aspnetcore)

Následující příklad ukazuje směrování s propracovanější šablonou trasy:

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

Řetězec `/hello/{name:alpha}` je **šablona postupu**. Slouží ke konfiguraci, jak je koncový bod spárován. V tomto případě šablona odpovídá:

* Adresa URL jako`/hello/Ryan`
* Jakákoli cesta URL, `/hello/` která začíná následovaným posloupností abecedních znaků.  `:alpha`použije omezení trasy, které odpovídá pouze abecedním znakům. Omezení postupu jsou [vysvětlena](#route-constraint-reference) dále v tomto dokumentu.

Druhý segment cesty URL: `{name:alpha}`

* Je vázán `name` na parametr.
* Je zachycena a uložena v [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).

Systém směrování koncových bodů popsaný v tomto dokumentu je od ASP.NET jádru 3.0 nový. Všechny verze ASP.NET Core však podporují stejnou sadu funkcí šablony trasy a omezení trasy.

Následující příklad ukazuje směrování s [kontrolami stavu](xref:host-and-deploy/health-checks) a autorizací:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Předchozí příklad ukazuje, jak:

* Autorizační middleware lze použít s směrováním.
* Koncové body lze použít ke konfiguraci chování autorizace.

Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> přidá koncový bod kontroly stavu. Řetězení <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> na toto volání připojí zásady autorizace ke koncovému bodu.

Volání <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> a přidá ověřování a autorizace middleware. Tyto middleware jsou <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> `UseEndpoints` umístěny mezi a tak, že mohou:

* Podívejte se, který `UseRouting`koncový bod byl vybrán .
* Před <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> odesláním do koncového bodu použijte zásadu autorizace.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Metadata koncového bodu

V předchozím příkladu existují dva koncové body, ale pouze koncový bod kontroly stavu má připojené zásady autorizace. Pokud požadavek odpovídá koncovému `/healthz`bodu kontroly stavu , provede se kontrola autorizace. To ukazuje, že koncové body mohou mít další data k nim připojena. Tato další data se nazývají **metadata**koncového bodu :

* Metadata mohou být zpracovány pomocí middleware s vědomím směrování.
* Metadata mohou být libovolného typu .NET.

## <a name="routing-concepts"></a>Koncepty směrování

Systém směrování se staví na hlavním kanálu middlewaru přidáním výkonného konceptu **koncového bodu.** Koncové body představují jednotky funkcí aplikace, které se od sebe liší, pokud jde o směrování, autorizaci a libovolný počet ASP.NET systémů Core.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>definice ASP.NET základního koncového bodu

ASP.NET základní koncový bod je:

* Spustitelný soubor: <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>Má .
* Rozšiřitelné: Má [metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) kolekce.
* Volitelně: Volitelně má [informace o směrování](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).
* Výčet: Kolekce koncových bodů mohou být uvedeny načtením <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> z [DI](xref:fundamentals/dependency-injection).

Následující kód ukazuje, jak načíst a zkontrolovat koncový bod odpovídající aktuálnímu požadavku:

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Koncový bod, pokud je vybrán, lze `HttpContext`načíst z . Jeho vlastnosti mohou být kontrolovány. Objekty koncového bodu jsou neměnné a po vytvoření je nelze změnit. Nejběžnějším typem <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>koncového bodu je . `RouteEndpoint`obsahuje informace, které umožňují, aby byl vybrán systémem směrování.

V předchozím kódu [aplikace. Použití](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) konfiguruje in-line [middleware](xref:fundamentals/middleware/index).

<a name="mt"></a>

Následující kód ukazuje, že `app.Use` v závislosti na tom, kde je volána v kanálu, nemusí být koncový bod:

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

Tato předchozí ukázka přidá `Console.WriteLine` příkazy, které zobrazují, zda byl vybrán koncový bod. Pro přehlednost vzorku přiřadí zobrazovaný název `/` k poskytnutému koncovému bodu.

Spuštění tohoto kódu s `/` adresou URL zobrazení:

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Spuštění tohoto kódu s jinými projevy adres URL:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Tento výstup ukazuje, že:

* Koncový bod je vždy `UseRouting` null před je volána.
* Pokud je nalezena shoda, koncový bod `UseRouting` není <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>null mezi a .
* Middleware `UseEndpoints` je **terminál,** když je nalezena shoda. [Terminálový middleware](#tm) je definován dále v tomto dokumentu.
* Middleware po `UseEndpoints` spuštění pouze v případě, že není nalezena žádná shoda.

Middleware `UseRouting` používá [Metodu SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) k připojení koncového bodu k aktuálnímu kontextu. Je možné nahradit `UseRouting` middleware s vlastní logikou a stále získat výhody použití koncových bodů. Koncové body jsou primitivní jako middleware nižší úrovně a nejsou spojeny s implementací směrování. Většina aplikací nemusí být `UseRouting` nahrazena vlastní logikou.

Middleware `UseEndpoints` je určen pro použití v tandemu `UseRouting` s middleware. Základní logika pro spuštění koncového bodu není složitá. Slouží <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> k načtení koncového bodu <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> a potom vyvolat jeho vlastnost.

Následující kód ukazuje, jak middleware může ovlivnit nebo reagovat na směrování:

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

Předchozí příklad ukazuje dva důležité pojmy:

* Middleware lze `UseRouting` spustit před upravit data, která směrování pracuje na.
    * Middleware, který se zobrazí před směrováním, obvykle upraví <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>některé <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>vlastnosti požadavku, například <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, nebo .
* Middleware může `UseRouting` běžet <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> mezi a zpracovat výsledky směrování před spuštěním koncového bodu.
    * Middleware, který `UseRouting` `UseEndpoints`běží mezi a :
      * Obvykle kontroluje metadata pochopit koncové body.
      * Často dělá rozhodnutí o zabezpečení, jak je tomu v rámci `UseAuthorization` a `UseCors`.
    * Kombinace middlewaru a metadat umožňuje konfiguraci zásad pro koncový bod.

Předchozí kód ukazuje příklad vlastního middlewaru, který podporuje zásady pro koncový bod. Middleware zapíše *protokol auditu* přístupu k citlivým datům do konzoly. Middleware lze nakonfigurovat tak, aby `AuditPolicyAttribute` *auditovat* koncový bod s metadaty. Tato ukázka ukazuje *vzor opt-in,* kde jsou auditovány pouze koncové body, které jsou označeny jako citlivé. Je možné definovat tuto logiku v opačném pořadí, auditování vše, co není označeno jako bezpečné, například. Systém metadat koncových bodů je flexibilní. Tato logika by mohla být navržena jakýmkoli způsobem vyhovuje případu použití.

Předchozí ukázkový kód je určen k prokázání základní pojmy koncových bodů. **Vzorek není určen pro výrobní účely**. Úplnější verze middlewaru *protokolu auditu* by:

* Protokolovat do souboru nebo databáze.
* Uveďte podrobnosti, jako je uživatel, IP adresa, název citlivého koncového bodu a další.

Metadata zásad `AuditPolicyAttribute` auditu jsou `Attribute` definována jako pro snadnější použití s architekturami založenými na třídách, jako jsou řadiče a SignalR. Při použití *trasy ke kódu*:

* Metadata jsou připojena k rozhraní API tvůrce.
* Architektury založené na třídách zahrnují všechny atributy na odpovídající metodu a třídu při vytváření koncových bodů.

Doporučené postupy pro typy metadat jsou definovat je buď jako rozhraní nebo atributy. Rozhraní a atributy umožňují opakované použití kódu. Systém metadat je flexibilní a neukládá žádná omezení.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Porovnání koncového middlewaru a směrování

Následující ukázka kódu kontrastuje pomocí middleware s pomocí směrování:

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

Styl middleware je `Approach 1:` zobrazen s je **terminál middleware**. Říká se tomu terminálmiddleware, protože provádí odpovídající operaci:

* Odpovídající operace v předchozí ukázce `Path == "/"` je `Path == "/Movie"` pro middleware a pro směrování.
* Pokud je shoda úspěšná, provede některé funkce a vrátí, spíše `next` než vyvolání middleware.

Nazývá se terminálmiddleware, protože ukončí vyhledávání, provede některé funkce a pak se vrátí.

Porovnání koncového middlewaru a směrování:
* Oba přístupy umožňují ukončení kanálu zpracování:
    * Middleware ukončí potrubí tím, že vrátí spíše `next`než vyvolání .
    * Koncové body jsou vždy terminální.
* Terminálmiddleware umožňuje umístění middleware na libovolné místo v potrubí:
    * Koncové body se provádějí <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>na pozici .
* Terminálmiddleware umožňuje libovolný kód určit, kdy middleware odpovídá:
    * Vlastní kód pro porovnávání tras může být podrobný a obtížně správně zapisovat.
    * Směrování poskytuje jednoduchá řešení pro typické aplikace. Většina aplikací nevyžaduje vlastní kód pro porovnávání tras.
* Koncové body rozhraní s `UseAuthorization` middleware, jako je například a `UseCors`.
    * Použití koncového middlewaru s `UseAuthorization` autorizačním systémem nebo `UseCors` vyžaduje ruční propojení s autorizačním systémem.

[Koncový bod](#endpoint) definuje obojí:

* Delegát pro zpracování požadavků.
* Kolekce libovolných metadat. Metadata se používá k implementaci průřezové obavy založené na zásady a konfigurace připojené ke každému koncovému bodu.

Terminál middleware může být účinným nástrojem, ale může vyžadovat:

* Značné množství kódování a testování.
* Ruční integrace s jinými systémy pro dosažení požadované úrovně flexibility.

Zvažte integraci s směrováním před napsáním terminálového middlewaru.

Existující terminálový middleware, který <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> se integruje s [mapou](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) nebo může být obvykle přeměněn na koncový bod vědomý směrování. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) ukazuje vzor pro router-ware:
* Napište metodu <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>rozšíření na aplikaci .
* Vytvořte vnořený <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>kanál middlewaru pomocí aplikace .
* Připojte middleware k novému potrubí. V tomto <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>případě.
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>middleware potrubí do <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Zavolejte `Map` a poskytněte nový middleware potrubí.
* Vrátí objekt tvůrce poskytované `Map` z metody rozšíření.

Následující kód ukazuje použití [MapHealthChecks](xref:host-and-deploy/health-checks):

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

Předchozí ukázka ukazuje, proč je důležité vrácení objektu tvůrce. Vrácení objektu tvůrce umožňuje vývojáři aplikace konfigurovat zásady, jako je autorizace pro koncový bod. V tomto příkladu middleware kontroly stavu nemá žádnou přímou integraci se systémem autorizace.

Systém metadat byl vytvořen v reakci na problémy, s nimiž se setkávají autoři rozšiřitelnosti pomocí terminálového middlewaru. Pro každý middleware je problematické implementovat vlastní integraci s autorizačním systémem.

<a name="urlm"></a>

### <a name="url-matching"></a>Párování adres URL

* Je proces, kterým směrování odpovídá příchozí požadavek na [koncový bod](#endpoint).
* Je založen na datech v cestě URL a záhlavích.
* Lze rozšířit, aby zvážila všechna data v žádosti.

Když směrování middleware spustí, nastaví `Endpoint` a směrování hodnoty na [požadavek funkce](xref:fundamentals/request-features) <xref:Microsoft.AspNetCore.Http.HttpContext> na od aktuálního požadavku:

* Volání [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) získá koncový bod.
* `HttpRequest.RouteValues`získá kolekci hodnot trasy.

[Middleware](xref:fundamentals/middleware/index) běží po směrování middleware můžete zkontrolovat koncový bod a přijmout opatření. Middleware autorizace může například vyslýchat kolekci metadat koncového bodu pro zásady autorizace. Po spuštění všech middleware v kanálu zpracování požadavku je vyvolána delegát vybraného koncového bodu.

Systém směrování v směrování koncových bodů je zodpovědný za všechna rozhodnutí o odeslání. Vzhledem k tomu, že middleware používá zásady založené na vybraném koncovém bodu, je důležité, aby:

* Jakékoli rozhodnutí, které může ovlivnit odeslání nebo použití zásad zabezpečení, je provedeno uvnitř systému směrování.

> [!WARNING]
> Pro zpětnou kompatibilitu při controller nebo Razor Pages koncový bod delegáta je proveden, vlastnosti [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) jsou nastaveny na příslušné hodnoty na základě zpracování požadavku provádí tak daleko.
>
> Typ `RouteContext` bude v budoucí verzi označen jako zastaralý:
>
> * Migrace `RouteData.Values` `HttpRequest.RouteValues`do aplikace .
> * Migrovat `RouteData.DataTokens` k načtení [metadat IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) z metadat koncového bodu.

Párování adres URL funguje v konfigurovatelné sadě fází. V každé fázi je výstupem sada shod. Sadu zápasů lze v další fázi dále zúžit. Implementace směrování nezaručuje pořadí zpracování pro odpovídající koncové body. **Všechny** možné shody jsou zpracovány najednou. Fáze párování adres URL se vyskytují v následujícím pořadí. ASP.NET Core:

1. Zpracuje cestu URL proti sadě koncových bodů a jejich šablony tras, shromažďování **všech** shod.
1. Odebere předchozí seznam a odebere shody, které se nezdaří s použitými omezeními trasy.
1. Odebere předchozí seznam a odebere shody, které neporuší sadu instancí [MatcherPolicy.](xref:Microsoft.AspNetCore.Routing.MatcherPolicy)
1. Používá [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) k konečnému rozhodnutí z předchozího seznamu.

Seznam koncových bodů je upřednostněn podle:

* [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* [Priorita šablony trasy](#rtp)

Všechny odpovídající koncové body jsou zpracovány <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> v každé fázi, dokud není dosaženo. Je `EndpointSelector` to poslední fáze. Vybere koncový bod s nejvyšší prioritou ze zápasů jako nejlepší shoda. Pokud existují jiné shody se stejnou prioritou jako nejlepší shoda, je vyvolána nejednoznačná výjimka shody.

Priorita trasy se vypočítá na základě **konkrétnější** šablony trasy, která má vyšší prioritu. Zvažte například šablony `/hello` `/{message}`a :

* Obě odpovídají cestě `/hello`URL .
* `/hello`konkrétnější, a tudíž vyšší prioritu.

Obecně platí, že priorita trasy dělá dobrou práci při výběru nejlepší shody pro typy schémat adres URL používaných v praxi. Používejte <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> pouze v případě potřeby, aby se zabránilo nejednoznačnosti.

Vzhledem k druhům rozšiřitelnosti poskytované směrování, není možné pro systém směrování vypočítat předem nejednoznačné trasy. Vezměme si příklad, `/{message:alpha}` jako `/{message:int}`jsou šablony tras a :

* Omezení `alpha` odpovídá pouze abecedním znakům.
* Omezení `int` odpovídá pouze číslům.
* Tyto šablony mají stejnou prioritu trasy, ale neexistuje žádná jediná adresa URL, které se obě shodují.
* Pokud systém směrování ohlásil chybu nejednoznačnosti při spuštění, zablokoval by tento platný případ použití.

> [!WARNING]
>
> Pořadí operací uvnitř <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> nemá vliv na chování směrování, s jednou výjimkou. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>a <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> automaticky přiřadit hodnotu objednávky jejich koncovým bodům na základě pořadí, ve které jsou vyvolány. To simuluje dlouhodobé chování řadičů bez systému směrování poskytující stejné záruky jako starší implementace směrování.
>
> Ve starší implementaci směrování je možné implementovat rozšiřitelnost směrování, která má závislost na pořadí, ve kterém jsou zpracovávány trasy. Směrování koncového bodu v ASP.NET jádrem 3.0 a novějším:
> 
> * Nemá pojem trasy.
> * Neposkytuje záruky objednávání. Všechny koncové body jsou zpracovány najednou.
>
> Pokud to znamená, že jste přilepená pomocí starší ho systému směrování, [otevřete github problém pro pomoc](https://github.com/dotnet/aspnetcore/issues).

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Priorita šablony trasy a pořadí výběru koncového bodu

[Priorita šablony postupu](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) je systém, který každé šabloně trasy přiřazuje hodnotu na základě toho, jak je specifická. Priorita šablony trasy:

* Vyhýbá se nutnosti upravit pořadí koncových bodů v běžných případech.
* Pokusy o sladění selský rozum očekávání směrování chování.

Zvažte například `/Products/List` šablony `/Products/{id}`a . Bylo by rozumné předpokládat, že `/Products/List` `/Products/{id}` je lepší `/Products/List`shoda než pro cestu URL . Funguje, protože segment `/List` literálu je považován za lepší `/{id}`prioritu než segment parametru .

Podrobnosti o tom, jak funguje priorita, jsou spojeny s tím, jak jsou definovány šablony tras:

* Šablony s více segmenty jsou považovány za konkrétnější.
* Segment s literálovým textem je považován za specifičtější než segment parametrů.
* Segment parametru s vazbou je považován za specifičtější než segment bez.
* Složitý segment je považován za specifický jako segment parametrů s vazbou.
* Catch všechny parametry jsou nejméně specifické.

Podívejte se na [zdrojový kód na GitHubu](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) pro odkaz na přesné hodnoty.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Koncepty generování adres URL

Generování adresy URL:

* Je proces, kterým směrování může vytvořit cestu URL na základě sady hodnot trasy.
* Umožňuje logické oddělení mezi koncovými body a adresy URL, které k nim přistupují.

Směrování koncových <xref:Microsoft.AspNetCore.Routing.LinkGenerator> bodů zahrnuje rozhraní API. `LinkGenerator`je singleton služba k dispozici od [DI](xref:fundamentals/dependency-injection). Rozhraní `LinkGenerator` API lze použít mimo kontext vykonávajícího požadavku. [Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) a scénáře, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>které jsou závislé na , jako jsou [pomocné spoje značek](xref:mvc/views/tag-helpers/intro), html pomocníci a [výsledky akcí](xref:mvc/controllers/actions), používají `LinkGenerator` rozhraní API interně k poskytování možností generování odkazů.

Generátor propojení je podpořen konceptem schémat **adres** a **adres**. Schéma adresy je způsob, jak určit koncové body, které by měly být považovány za generování propojení. Například scénáře název trasy a hodnoty trasy mnoho uživatelů jsou obeznámeni s z řadičů a Razor Pages jsou implementovány jako schéma adres.

Generátor odkazů lze propojit s řadiči a Razor Pages pomocí následujících metod rozšíření:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Přetížení těchto metod přijmout argumenty, `HttpContext`které zahrnují . Tyto metody jsou funkčně ekvivalentní [url.action](xref:System.Web.Mvc.UrlHelper.Action*) a [url.page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), ale nabízejí další flexibilitu a možnosti.

Metody `GetPath*` jsou nejvíce `Url.Action` podobné `Url.Page`a , v tom, že generují identifikátor URI obsahující absolutní cestu. Metody `GetUri*` vždy generovat absolutní URI obsahující schéma a hostitele. Metody, které `HttpContext` přijímají generovat identifikátor URI v kontextu vykonávajícího požadavku. Hodnoty [trasy okolí,](#ambient) základní cesta URL, schéma a hostitel z vykonávajícího požadavku se používají, pokud nejsou přepsány.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>je volána s adresou. Generování identifikátoru URI probíhá ve dvou krocích:

1. Adresa je vázána na seznam koncových bodů, které odpovídají adrese.
1. Každý koncový bod <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> je vyhodnocen, dokud není nalezen vzorek postupu, který odpovídá zadaným hodnotám. Výsledný výstup je kombinován s ostatními částmi URI dodanými do generátoru propojení a vráceny.

Metody poskytované <xref:Microsoft.AspNetCore.Routing.LinkGenerator> podporou standardních možností generování propojení pro jakýkoli typ adresy. Nejpohodlnější způsob, jak používat generátor propojení je prostřednictvím rozšíření metody, které provádějí operace pro určitý typ adresy:

| Metoda rozšíření | Popis |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generuje identifikátor URI s absolutní cestou na základě zadaných hodnot. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generuje absolutní identifikátor URI na základě zadaných hodnot.             |

> [!WARNING]
> Věnujte pozornost následujícím <xref:Microsoft.AspNetCore.Routing.LinkGenerator> důsledkům volání metod:
>
> * V `GetUri*` konfiguraci aplikace, která neověřuje `Host` záhlaví příchozích požadavků, používejte metody rozšíření opatrně. Pokud `Host` záhlaví příchozích požadavků není ověřeno, nelze klientovi v zobrazení nebo na stránce odeslat zpět nedůvěryhodný vstup požadavku. Doporučujeme, aby všechny produkční aplikace `Host` nakonfigurovaly svůj server tak, aby ověřoval záhlaví proti známým platným hodnotám.
>
> * Používejte <xref:Microsoft.AspNetCore.Routing.LinkGenerator> s opatrností v `Map` middleware v kombinaci s nebo `MapWhen`. `Map*`změní základní cestu vykonávajícího požadavku, což má vliv na výstup generování propojení. Všechna <xref:Microsoft.AspNetCore.Routing.LinkGenerator> api umožňují určení základní cesty. Zadejte prázdnou základní cestu, která má vrátit `Map*` vliv na generování propojení.

### <a name="middleware-example"></a>Middleware příklad

V následujícím příkladu middleware <xref:Microsoft.AspNetCore.Routing.LinkGenerator> používá rozhraní API k vytvoření odkazu na metodu akce, která uvádí seznam produktů úložiště. Použití generátoru propojení vstřikováním `GenerateLink` do třídy a volání je k dispozici pro všechny třídy v aplikaci:

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Odkaz na šablonu trasy

Tokeny `{}` v rámci definovat parametry trasy, které jsou vázány, pokud je trasa uzavřeno. V segmentu trasy lze definovat více než jeden parametr trasy, ale parametry trasy musí být odděleny hodnotou literálu. Například `{controller=Home}{action=Index}` není platná trasa, protože neexistuje žádná hodnota `{controller}` literálu mezi a `{action}`.  Parametry trasy musí mít název a mohou mít zadané další atributy.

Doslovný text jiný než parametry `{id}`trasy (například) a oddělovač `/` cesty se musí shodovat s textem v adrese URL. Porovnávání textu nerozlišuje malá a velká písmena a je založena na dekódované reprezentaci cesty url. Chcete-li porovnat oddělovač `{` parametrů `}`literálu trasy nebo , unikne oddělovači opakováním znaku. Například `{{` `}}`nebo .

Hvězdička `*` nebo dvojitá `**`hvězdička :

* Lze použít jako předponu parametru trasy k vazbě na zbytek identifikátoru URI.
* Nazývají **catch-all** parametry. Například: `blog/{**slug}`
  * Odpovídá všem identifikátorům `/blog` URI, který začíná a má za ním libovolnou hodnotu.
  * Následující `/blog` hodnota je přiřazena hodnotě trasy [popisu.](https://developer.mozilla.org/docs/Glossary/Slug)

Catch-all parametry mohou také odpovídat prázdný řetězec.

Catch-all Parametr unikne příslušné znaky při směrování slouží ke generování `/` adresy URL, včetně oddělovací znaky cesty. Například trasa `foo/{*path}` s `{ path = "my/path" }` hodnotami `foo/my%2Fpath`trasy generuje . Všimněte si uvozené lomítko. K oddělovacím znakům cesty `**` okolení použijte předponu parametru trasy. Trasa `foo/{**path}` `{ path = "my/path" }` s `foo/my/path`generuje .

Další důležité informace mají vzory adres URL, které se pokoušejí zachytit název souboru s volitelnou příponou souboru. Zvažte například `files/{filename}.{ext?}`šablonu . Pokud jsou `filename` hodnoty `ext` pro oba a existují, jsou vyplněny obě hodnoty. Pokud v adrese `filename` URL existuje pouze hodnota pro, trasa se shoduje, protože koncové je `.` volitelné. Následující adresy URL odpovídají této trase:

* `/files/myFile.txt`
* `/files/myFile`

Parametry postupu mohou mít **výchozí hodnoty** určené zadáním výchozí hodnoty za názvem`=`parametru odděleným znaménkem rovná se ( ). Definuje například `{controller=Home}` `Home` jako výchozí hodnotu pro `controller`. Výchozí hodnota se používá, pokud v adrese URL parametru není žádná hodnota. Parametry trasy jsou volitelné připojením otazníku (`?`) na konec názvu parametru. Například, `id?`. Rozdíl mezi volitelnými hodnotami a výchozími parametry trasy je následující:

* Parametr trasy s výchozí hodnotou vždy vytvoří hodnotu.
* Volitelný parametr má hodnotu pouze v případě, že hodnota je poskytnuta adresou URL požadavku.

Parametry trasy mohou mít omezení, která musí odpovídat hodnotě trasy vázané z adresy URL. Přidání `:` a název omezení za název parametru trasy určuje vázací omezení parametru trasy. Pokud omezení vyžaduje argumenty, jsou uzavřeny v `(...)` závorcích za názvem omezení. Více *vřádkové omezení* lze zadat `:` připojením jiného a názvu omezení.

Název omezení a argumenty jsou <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> předány službě <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> k vytvoření instance pro použití při zpracování adres URL. Například šablona `blog/{article:minlength(10)}` trasy určuje `minlength` omezení s `10`argumentem . Další informace o omezeních postupu a seznam omezení poskytovaných rámcem naleznete v referenční části [omezení trasy.](#route-constraint-reference)

Parametry trasy mohou mít také parametrtransformátory. Parametrové transformátory transformují hodnotu parametru při generování odkazů a odpovídajících akcí a stránek na adresy URL. Podobně jako omezení mohou být transformátory parametrů přidány do `:` parametru trasy přidáním názvu a transformátoru za název parametru trasy. Například šablona `blog/{article:slugify}` trasy určuje `slugify` transformátor. Další informace o transformátorech parametrů naleznete v [části Reference parametrtransformátoru.](#parameter-transformer-reference)

Následující tabulka ukazuje ukázkové šablony tras a jejich chování:

| Šablona postupu                           | Příklad odpovídajícího identifikátoru URI    | Identifikátor URI požadavku&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Odpovídá pouze jedné `/hello`cestě .                                     |
| `{Page=Home}`                            | `/`                     | Odpovídá a `Page` `Home`nastavuje na .                                         |
| `{Page=Home}`                            | `/Contact`              | Odpovídá a `Page` `Contact`nastavuje na .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mapuje `Products` na `List` ovladač a akci.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapuje `Products` na `Details` ovladač`id` a akce s nastavenou na 123. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapuje `Home` na `Index` řadič a metodu. `id` se ignoruje.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Mapuje `Products` na `Index` řadič a metodu. `id` se ignoruje.        |

Použití šablony je obecně nejjednodušší přístup k směrování. Omezení a výchozí hodnoty lze zadat také mimo šablonu trasy.

### <a name="complex-segments"></a>Složité segmenty

Složité segmenty jsou zpracovány porovnáním literálových oddělovačů zprava [doleva nechamtivým](#greedy) způsobem. Například `[Route("/a{b}c{d}")]` je složitý segment.
Složité segmenty fungují určitým způsobem, který je třeba chápat, aby byly úspěšně používány. Příklad v této části ukazuje, proč složité segmenty skutečně fungují dobře pouze v případě, že se text oddělovače nezobrazuje uvnitř hodnot parametrů. Použití [regulárního výrazu](/dotnet/standard/base-types/regular-expressions) a následné ruční extrahování hodnot je potřeba pro složitější případy.

Toto je souhrn kroků, které směrování `/a{b}c{d}` provádí se `/abcd`šablonou a cestou url . Slouží `|` k vizualizaci, jak algoritmus funguje:

* První literál, zprava doleva, je `c`. Tak `/abcd` je prohledán `/ab|c|d`zprava a najde .
* Vše vpravo`d`( ) je nyní spárováno s parametrem `{d}`trasy .
* Další literál, zprava doleva, je `a`. Takže `/ab|c|d` je prohledána začíná `a` tam, `/|a|b|c|d`kde jsme skončili, pak se nachází .
* Hodnota vpravo (`b`) je nyní spárována `{b}`s parametrem trasy .
* Neexistuje žádný zbývající text a žádná zbývající šablona trasy, takže se jedná o shodu.

Tady je příklad negativního případu, který `/a{b}c{d}` používá stejnou `/aabcd`šablonu a cestu url . Slouží `|` k vizualizaci, jak algoritmus funguje. Tento případ se neshoduje, což je vysvětleno stejným algoritmem:
* První literál, zprava doleva, je `c`. Tak `/aabcd` je prohledán `/aab|c|d`zprava a najde .
* Vše vpravo`d`( ) je nyní spárováno s parametrem `{d}`trasy .
* Další literál, zprava doleva, je `a`. Takže `/aab|c|d` je prohledána začíná `a` tam, `/a|a|b|c|d`kde jsme skončili, pak se nachází .
* Hodnota vpravo (`b`) je nyní spárována `{b}`s parametrem trasy .
* V tomto okamžiku je `a`zbývající text , ale algoritmus má nedostatek šablony trasy analyzovat, takže to není shoda.

Vzhledem k tomu, odpovídající algoritmus je [non-chamtivý](#greedy):

* Odpovídá nejmenšímu možnému množství textu v každém kroku.
* V každém případě, kdy se hodnota oddělovače objeví uvnitř hodnot parametrů, dojde k neshodu.

Regulární výrazy poskytují mnohem větší kontrolu nad jejich chování odpovídající.

<a name="greedy"></a>

Greedy odpovídající, také vědět, jak [opožděné odpovídající](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), odpovídá největší možný řetězec. Nechamtivý odpovídá nejmenší možný řetězec.

## <a name="route-constraint-reference"></a>Odkaz na omezení postupu

Omezení trasy se spustí, když došlo ke shodě s příchozí adresou URL a cesta url je tokenizována na hodnoty trasy. Omezení postupu obvykle kontrolují hodnotu postupu přidruženou prostřednictvím šablony postupu a činí pravdivé nebo nepravdivé rozhodnutí o tom, zda je hodnota přijatelná. Některá omezení postupu používají data mimo hodnotu postupu k posouzení, zda lze požadavek směrovat. Například <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> může přijmout nebo odmítnout požadavek na základě jeho slovesa HTTP. Omezení se používají v požadavcích na směrování a generování propojení.

> [!WARNING]
> Nepoužívejte omezení pro ověření vstupu. Pokud omezení se používají pro ověření vstupu, `404` neplatný vstup výsledky v nenalezené odpovědi. Neplatný vstup `400` by měl vytvořit chybný požadavek s příslušnou chybovou zprávou. Omezení trasy se používají k rozdvojení podobných tras, nikoli k ověření vstupů pro určitou trasu.

Následující tabulka ukazuje ukázková omezení trasy a jejich očekávané chování:

| omezení | Příklad | Příklady shod | Poznámky |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Shoduje se s libovolným celéčíslo |
| `bool` | `{active:bool}` | `true`, `FALSE` | `true` Zápasy `false`nebo . Case-insensitive |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platné `DateTime` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platné `decimal` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platné `double` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platné `float` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Odpovídá platné `Guid` hodnotě. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Odpovídá platné `long` hodnotě. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Řetězec musí mít alespoň 4 znaky. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Řetězec nesmí být větší než 8 znaků. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Řetězec musí mít přesně 12 znaků. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Řetězec musí mít alespoň 8 a nesmí mít přes 16 znaků. |
| `min(value)` | `{age:min(18)}` | `19` | Hodnota celého čísla musí být alespoň 18 |
| `max(value)` | `{age:max(120)}` | `91` | Celá hodnota nesmí být větší než 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Hodnota celého čísla musí být alespoň 18, ale ne větší než 120 |
| `alpha` | `{name:alpha}` | `Rick` | Řetězec se musí skládat z `a` - `z` jednoho nebo více abecedních znaků a bez rozlišování velkých a malých písmen. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Řetězec musí odpovídat regulárnímu výrazu. Podívejte se na tipy pro definici regulárního výrazu. |
| `required` | `{name:required}` | `Rick` | Slouží k vynucení, že hodnota neparametrje přítomna během generování adresy URL. |

[!INCLUDE[](~/includes/regex.md)]

Na jeden parametr lze použít více omezení vymezených dvojtečkou. Například následující omezení omezuje parametr na celou hodnotu 1 nebo vyšší:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Omezení trasy, která ověřují adresu URL a jsou převedena na typ CLR, vždy používají invariantní jazykovou verzi. Například převod na typ `int` CLR nebo `DateTime`. Tato omezení předpokládají, že adresu URL nelze lokalizovat. Omezení trasy poskytovaná rámcem nemění hodnoty uložené v hodnotách trasy. Všechny hodnoty trasy analyzované z adresy URL jsou uloženy jako řetězce. Například `float` omezení se pokusí převést hodnotu trasy na float, ale převedená hodnota se používá pouze k ověření, že může být převedena na float.

### <a name="regular-expressions-in-constraints"></a>Regulární výrazy v omezeních

[!INCLUDE[](~/includes/regex.md)]

Regulární výrazy lze zadat jako `regex(...)` válčná omezení pomocí omezení postupu. Metody v <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> rodině také přijmout literál objektu omezení. Pokud je tento formulář použit, řetězcové hodnoty jsou interpretovány jako regulární výrazy.

Následující kód používá omezení vposlední regulární výraz:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

Následující kód používá literál objektu k určení omezení regulárního výrazu:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

Rozhraní ASP.NET Core `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` přidává k konstruktoru regulárního výrazu. Viz <xref:System.Text.RegularExpressions.RegexOptions> popis těchto členů.

Regulární výrazy používají oddělovače a tokeny podobné těm, které používají směrování a jazyk C#. Tokeny regulárního výrazu musí být uvozeny. Chcete-li použít `^\d{3}-\d{2}-\d{4}$` regulární výraz v inline omezení, použijte jednu z následujících možností:

* Nahraďte `\` znaky uvedené `\\` v řetězci jako znaky ve zdrojovém souboru Jazyka C#, aby se vyhnuly řídicímu znaku `\` řetězce.
* [Doslovné řetězcové literály](/dotnet/csharp/language-reference/keywords/string).

Chcete-li `{`uniknout znaky oddělovače parametrů směrování , `}`, `[`zdvojnásobte `[[` `]]` `]`znaky ve výrazu, `{{`například , `}}`, , . V následující tabulce je uveden regulární výraz a jeho uvozená verze:

| Regulární výraz    | Uvozen regulární výraz     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy používané při `^` směrování často začínají znakem a odpovídají počáteční pozici řetězce. Výrazy často končí `$` znakem a odpovídají konci řetězce. Znaky `^` `$` a zajišťují, že regulární výraz odpovídá celé hodnotě parametru trasy. Bez `^` znaků `$` a regulární výraz odpovídá libovolnému podřetězci v řetězci, což je často nežádoucí. V následující tabulce jsou uvedeny příklady a vysvětleny, proč se shodují nebo neshodují:

| Expression   | Řetězec    | Shoda | Poznámka               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | Mz        | Ano   | Odpovídá výrazu    |
| `[a-z]{2}`   | MZ        | Ano   | Neuznačují malá a velká písmen    |
| `^[a-z]{2}$` | hello     | Ne    | Viz `^` `$` a výše |
| `^[a-z]{2}$` | 123abc456 | Ne    | Viz `^` `$` a výše |

Další informace o syntaxi regulárních výrazů naleznete [v tématu .NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr na známou sadu možných hodnot, použijte regulární výraz. Například `{action:regex(^(list|get|create)$)}` pouze odpovídá `action` hodnotě `list` `get`trasy `create`do , , nebo . Pokud je předán do slovníku `^(list|get|create)$` omezení, řetězec je ekvivalentní. Omezení, která jsou předána ve slovníku omezení, které neodpovídají jednomu ze známých omezení, jsou také považována za regulární výrazy. Omezení, která jsou předána v rámci šablony, která neodpovídají jednomu ze známých omezení, nejsou považována za regulární výrazy.

### <a name="custom-route-constraints"></a>Vlastní omezení trasy

Vlastní omezení trasy lze vytvořit implementací <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozhraní. Rozhraní `IRouteConstraint` obsahuje <xref:System.Web.Routing.IRouteConstraint.Match*>, `true` který vrátí, pokud `false` je splněna omezení a jinak.

Omezení vlastní trasy jsou zřídka potřeba. Před implementací omezení vlastní trasy zvažte alternativy, jako je například vazba modelu.

Chcete-li `IRouteConstraint`použít vlastní , musí být typ omezení <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> postupu registrován u aplikace v kontejneru servisu. A `ConstraintMap` je slovník, který mapuje `IRouteConstraint` klíče omezení trasy na implementace, které tato omezení ověřují. Aplikace `ConstraintMap` lze aktualizovat buď `Startup.ConfigureServices` jako součást [služby. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) volání nebo <xref:Microsoft.AspNetCore.Routing.RouteOptions> konfigurací `services.Configure<RouteOptions>`přímo s . Příklad:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

Předchozí omezení se použije v následujícím kódu:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

Implementace zabránění `MyCustomConstraint` `0` použití parametru trasy:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

Předcházející kód:

* Zabrání `0` v `{id}` segmentu trasy.
* Je zobrazen a poskytuje základní příklad implementace vlastní omezení. Neměl by být používán v produkční aplikaci.

Následující kód je lepší přístup `id` k zabránění obsahující `0` obsahující z zpracování:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

Předchozí kód má následující výhody `MyCustomConstraint` oproti přístupu:

* Nevyžaduje vlastní omezení.
* Vrátí popisnější chybu, pokud parametr trasy `0`zahrne .

## <a name="parameter-transformer-reference"></a>Odkaz na parametr transformátor

Parametrické transformátory:

* Spusťte při generování <xref:Microsoft.AspNetCore.Routing.LinkGenerator>propojení pomocí .
* Implementovat <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.
* Jsou konfigurovány pomocí <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Vezměte hodnotu trasy parametru a transformujte ji na novou hodnotu řetězce.
* Výsledkem je použití transformované hodnoty ve generovaném propojení.

`slugify` Například vlastní parametr transformátor `blog\{article:slugify}` ve `Url.Action(new { article = "MyTestArticle" })` vzoru `blog\my-test-article`trasy s generuje .

Zvažte `IOutboundParameterTransformer` následující implementaci:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Chcete-li použít parametrový transformátor ve <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> `Startup.ConfigureServices`vzoru trasy, nakonfigurujte jej pomocí aplikace :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

Rozhraní ASP.NET Core framework používá parametr transformátory k transformaci identifikátoru URI, kde se řeší koncový bod. Například parametrové transformátory transformují `area`hodnoty `controller` `action`trasy `page`použité tak, aby odpovídaly , , a .

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

S předchozí šablonou trasy `SubscriptionManagementController.GetAll` je akce spárována `/subscription-management/get-all`s identifikátorem URI . Transformátor parametrů nezmění hodnoty trasy použité ke generování propojení. Například `Url.Action("GetAll", "SubscriptionManagement")` výstupy `/subscription-management/get-all`.

ASP.NET Core poskytuje konvencí rozhraní API pro použití parametrických transformátorů s generovanými trasami:

* Konvence <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC použije zadaný parametr transformátor u všech tras atributů v aplikaci. Parametr transformer transformuje atribut route tokeny jako jsou nahrazeny. Další informace naleznete [v tématu Použití transformátoru parametrů k přizpůsobení nahrazení tokenu](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> používá konvenci rozhraní API. Tato konvence aplikuje zadaný parametr transformátoru na všechny automaticky zjištěné Razor Pages. Parametr transformer transformuje segmenty názvů složek a souborů tras Razor Pages. Další informace naleznete [v tématu Použití transformátoru parametrů k přizpůsobení tras stránek](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>Odkaz na generování adresy URL

Tato část obsahuje odkaz na algoritmus implementovaný generováním adresy URL. V praxi nejsložitější příklady generování adres URL používají řadiče nebo Razor Pages. Další informace naleznete [v tématu směrování v řadičích.](xref:mvc/controllers/routing)

Proces generování adresy URL začíná [volánílinkem LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) nebo podobnou metodou. Metoda je k dispozici s adresou, sadou hodnot postupu a volitelně informacemi o aktuálním požadavku od `HttpContext`.

Prvním krokem je použití adresy k vyřešení sady koncových [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) bodů kandidáta pomocí typu, který odpovídá typu adresy.

Jakmile sada kandidátů je nalezen a schéma adresy, koncové body jsou seřazeny a zpracovány iterativně, dokud operace generování adresy URL úspěšné. Generování adresy URL **nekontroluje** nejednoznačnosti, první vrácený výsledek je konečný výsledek.

### <a name="troubleshooting-url-generation-with-logging"></a>Poradce při potížích s generováním adresy URL s protokolováním

Prvním krokem při odstraňování potíží s generováním `Microsoft.AspNetCore.Routing` `TRACE`adresy URL je nastavení úrovně protokolování na . `LinkGenerator`zaznamenává mnoho podrobností o jeho zpracování, které mohou být užitečné pro řešení problémů.

Podrobnosti o generování adresy URL najdete v [tématu odkaz](#ugr) na generování adres URL.

### <a name="addresses"></a>Adresy

Adresy jsou koncept v generování adres URL slouží k vytvoření svázání volání do generátoru propojení na sadu koncových bodů kandidáta.

Adresy jsou rozšiřitelný koncept, které přicházejí s dvě implementace ve výchozím nastavení:

* Použití *názvu koncového bodu* (`string`) jako adresy:
    * Poskytuje podobné funkce jako název trasy MVC.
    * Používá <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> typ metadat.
    * Řeší zadaný řetězec proti metadatům všech registrovaných koncových bodů.
    * Vyvolá výjimku při spuštění, pokud více koncových bodů používá stejný název.
    * Doporučeno pro všeobecné použití mimo ovladače a Razor Pages.
* Použití *hodnot* <xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>trasy ( ) jako adresy:
    * Poskytuje podobné funkce pro řadiče a generování starších adres URL razor pages.
    * Velmi složité rozšířit a ladit.
    * Poskytuje implementaci používanou `IUrlHelper`pomocí , Tag Helpers, HTML Helpers, Výsledky akcí atd.

Úlohou schématu adres je přidružení mezi adresou a odpovídajícími koncovými body podle libovolných kritérií:

* Schéma názvu koncového bodu provádí základní slovníkvyhledávání.
* Schéma hodnot trasy má komplexní nejlepší podmnožinu algoritmu sady.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Okolní hodnoty a explicitní hodnoty

Z aktuálního požadavku přistupuje směrování `HttpContext.Request.RouteValues`k hodnotám trasy aktuálního požadavku . Hodnoty přidružené k aktuálnímu požadavku jsou označovány jako **hodnoty okolí**. Pro účely přehlednosti se v dokumentaci označují hodnoty postupu předávané metodám jako **explicitní hodnoty**.

Následující příklad ukazuje okolní hodnoty a explicitní hodnoty. Poskytuje okolní hodnoty z aktuálního požadavku `{ id = 17, }`a explicitní hodnoty: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

Předcházející kód:

* Vrátí`/Widget/Index/17`
* Získá <xref:Microsoft.AspNetCore.Routing.LinkGenerator> přes [DI](xref:fundamentals/dependency-injection).

Následující kód neobsahuje žádné okolní `{ controller = "Home", action = "Subscribe", id = 17, }`hodnoty a explicitní hodnoty: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

Předchozí metoda vrátí`/Home/Subscribe/17`

Následující kód ve `WidgetController` `/Widget/Subscribe/17`přiznáních :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

Následující kód poskytuje řadič z okolních hodnot v `{ action = "Edit", id = 17, }`aktuálním požadavku a explicitní hodnoty: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

V předchozím kódu:

* `/Gadget/Edit/17`je vrácena.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url>získá <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
vygeneruje adresu URL s absolutní cestou pro metodu akce. Adresa URL obsahuje `action` zadaný název a `route` hodnoty.

Následující kód poskytuje okolní hodnoty z aktuálního `{ page = "./Edit, id = 17, }`požadavku a explicitní hodnoty: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

Předchozí kód nastaví, `url` `/Edit/17` když upravit holicí strojek stránka obsahuje následující direktivu stránky:

 `@page "{id:int}"`

Pokud stránka Úpravy šablonu `"{id:int}"` trasy `url` neobsahuje, je `/Edit?id=17`.

Chování MVC přidává <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> vrstvu složitosti kromě pravidel popsaných zde:

* `IUrlHelper`vždy poskytuje hodnoty trasy z aktuálního požadavku jako hodnoty okolí.
* [IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) vždy zkopíruje aktuální `action` a `controller` směrování hodnoty jako explicitní hodnoty, pokud přepsána vývojářem.
* [IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) vždy zkopíruje aktuální `page` hodnotu trasy jako explicitní hodnotu, pokud není přepsána. <!--by the user-->
* `IUrlHelper.Page`vždy přepíše `handler` aktuální hodnotu trasy `null` jako explicitní hodnoty, pokud není přepsána.

Uživatelé jsou často překvapeni podrobnostmi chování okolních hodnot, protože MVC nezdá se, že dodržovat vlastní pravidla. Z historických důvodů a z `action`důvodů `controller` `page`kompatibility `handler` mají určité hodnoty trasy, například , , , a mají své vlastní chování zvláštních případů.

Ekvivalentní funkce poskytované `LinkGenerator.GetPathByAction` a `LinkGenerator.GetPathByPage` duplikuje `IUrlHelper` tyto anomálie pro kompatibilitu.

### <a name="url-generation-process"></a>Proces generování adres URL

Jakmile je nalezena sada koncových bodů kandidáta, algoritmus generování adresy URL:

* Zpracovává koncové body iterativně.
* Vrátí první úspěšný výsledek.

První krok v tomto procesu se nazývá **zneplatnění hodnoty postupu**.  Zneplatnění hodnoty postupu je proces, při kterém směrování rozhoduje o tom, které hodnoty postupu z okolních hodnot by měly být použity a které by měly být ignorovány. Každá hodnota okolí je považována za a buď v kombinaci s explicitní hodnoty nebo ignorovány.

Nejlepší způsob, jak přemýšlet o roli okolních hodnot je, že se pokoušejí uložit vývojáři aplikací psaní, v některých běžných případech. Scénáře, kde jsou užitečné hodnoty okolí, se tradičně vztahují k MVC:

* Při propojení s jinou akcí ve stejném řadiči není nutné zadat název řadiče.
* Při propojení s jiným řadičem ve stejné oblasti není nutné zadat název oblasti.
* Při propojení se stejnou metodou akce není nutné zadanit hodnoty trasy.
* Při propojení s jinou částí aplikace nechcete přenášet hodnoty trasy, které nemají v této části aplikace žádný význam.

Volání `LinkGenerator` nebo `IUrlHelper` že `null` návrat jsou obvykle způsobeny nepochopení hodnoty trasy zneplatnění. Poradce při potížích s neplatností hodnoty postupu explicitním zadáním více hodnot trasy, abyste zjistili, zda se tím problém vyřešil.

Zplatnění hodnoty postupu funguje za předpokladu, že schéma adresy URL aplikace je hierarchické, s hierarchií vytvořenou zleva doprava. Zvažte základní šablonu `{controller}/{action}/{id?}` trasy řadiče, abyste získali intuitivní představu o tom, jak to funguje v praxi. **Změna** hodnoty **zruší platnost** všech hodnot trasy, které se zobrazí vpravo. To odráží předpoklad o hierarchii. Pokud má aplikace hodnotu `id`okolí pro aplikaci a operace `controller`určuje jinou hodnotu pro :

* `id`nebude znovu použit, `{controller}` protože je nalevo od `{id?}`.

Některé příklady prokazující tuto zásadu:

* Pokud explicitní hodnoty obsahují `id`hodnotu pro `id` , je hodnota okolí pro ignorována. Hodnoty okolí `controller` pro `action` a lze použít.
* Pokud explicitní hodnoty obsahují `action`hodnotu pro `action` , všechny okolní hodnoty pro je ignorována. Lze použít `controller` hodnoty okolí pro. Pokud se explicitní `action` hodnota pro liší `action`od `id` hodnoty okolí pro , nebude hodnota použita.  Pokud je explicitní `action` hodnota pro je stejná jako hodnota okolí pro `action`, lze použít hodnotu. `id`
* Pokud explicitní hodnoty obsahují `controller`hodnotu pro `controller` , všechny okolní hodnoty pro je ignorována. Pokud explicitní hodnota `controller` pro se liší `controller`od `action` hodnoty `id` okolí pro , a hodnoty nebudou použity. Pokud je explicitní `controller` hodnota pro je stejná `action` jako `id` hodnota okolí pro `controller`, hodnoty a lze použít.

Tento proces je dále komplikován existencí tras atributů a vyhrazených konvenčních tras. Kontroler konvenční trasy, jako je například `{controller}/{action}/{id?}` určit hierarchii pomocí parametrů trasy. Pro [vyhrazené konvenční trasy](xref:mvc/controllers/routing#dcr) a trasy [atributů](xref:mvc/controllers/routing#ar) regulátorům a žiletkovým stránkám:

* Existuje hierarchie hodnot trasy.
* Nezobrazují se v šabloně.

Pro tyto případy definuje generování adresy URL koncept **požadovaných hodnot.** Koncové body vytvořené řadiči a razor pages mají zadané požadované hodnoty, které umožňují zneplatnění hodnoty trasy.

Algoritmus zneplatnění hodnoty trasy podrobně:

* Požadované názvy hodnot jsou kombinovány s parametry trasy a poté zpracovány zleva doprava.
* Pro každý parametr se porovná hodnota okolí a explicitní hodnota:
    * Pokud okolní hodnota a explicitní hodnota jsou stejné, proces pokračuje.
    * Pokud je k dispozici hodnota okolí a explicitní hodnota není, hodnota okolí se používá při generování adresy URL.
    * Pokud hodnota okolí není k dispozici a explicitní hodnota je, odmítnout hodnotu okolí a všechny následné hodnoty okolí.
    * Pokud jsou přítomny hodnota okolí a explicitní hodnota a dvě hodnoty se liší, odmítnout hodnotu okolí a všechny následné hodnoty okolí.

V tomto okamžiku operace generování adresy URL je připravena k vyhodnocení omezení trasy. Sada přijatých hodnot je kombinována s výchozími hodnotami parametrů, které jsou k dispozici omezením. Pokud všechna omezení přecházejí, operace pokračuje.

Dále **lze přijaté hodnoty** použít k rozbalení šablony trasy. Šablona trasy je zpracována:

* Zleva doprava.
* Každý parametr má svou přijatou hodnotu nahrazenou.
* S následujícími zvláštními případy:
  * Pokud přijaté hodnoty chybí hodnota a parametr má výchozí hodnotu, použije se výchozí hodnota.
  * Pokud přijaté hodnoty chybí hodnotu a parametr je volitelný, zpracování pokračuje.
  * Pokud má libovolný parametr trasy vpravo od chybějícího volitelného parametru hodnotu, operace se nezdaří.
  * <!-- review default-valued parameters optional parameters --> Souvislé parametry s výchozí hodnotou a volitelné parametry jsou pokud možno sbaleny.

Do řetězce dotazu jsou přidány explicitně poskytnuté hodnoty, které neodpovídají segmentu trasy. V následující tabulce je uveden výsledek `{controller}/{action}/{id?}`při použití šablony trasy .

| Okolní hodnoty                     | Explicitní hodnoty                        | Výsledek                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| regulátor = "Domů"                | akce = "O"                       | `/Home/About`           |
| regulátor = "Domů"                | controller = "Objednávka", akce = "O" | `/Order/About`          |
| regulátor = "Domů", barva = "Červená" | akce = "O"                       | `/Home/About`           |
| regulátor = "Domů"                | akce = "O", barva = "Červená"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Problémy s neplatností hodnoty trasy

Od ASP.NET Core 3.0, některá schémata generování adres URL používaná v dřívějších verzích ASP.NET Core nefungují dobře s generováním adres URL. Tým ASP.NET Core plánuje přidat funkce pro řešení těchto potřeb v budoucí verzi. Pro tuto chvíli je nejlepším řešením použít starší směrování.

Následující kód ukazuje příklad schématu generování adres URL, které není podporováno směrováním.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

V předchozím kódu se `culture` parametr trasy používá pro lokalizaci. Touha je mít `culture` parametr vždy přijat jako okolní hodnota. `culture` Parametr však není přijat jako hodnota okolí z důvodu způsobu, jakým požadované hodnoty fungují:

* V `"default"` šabloně trasy `culture` je parametr trasy `controller`nalevo `controller` od , takže `culture`se změní na nezruší platnost .
* V `"blog"` šabloně trasy `culture` je parametr trasy považován za `controller`pravý od , který se zobrazí v požadovaných hodnotách.

## <a name="configuring-endpoint-metadata"></a>Konfigurace metadat koncového bodu

Následující odkazy poskytují informace o konfiguraci metadat koncového bodu:

* [Povolení corsu s směrováním koncových bodů](xref:security/cors#enable-cors-with-endpoint-routing)
* [Ukázka iAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) `[MinimumAgeAuthorize]` pomocí vlastního atributu
* [Testování ověřování pomocí atributu [Authorize]](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [Výběr schématu s atributem [Authorize]](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [Použití zásad pomocí atributu [Authorize]](xref:security/authorization/policies#applying-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>Porovnávání hostitelů v trasách s RequireHost

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*>použije omezení na trasu, která vyžaduje zadaného hostitele. Parametr `RequireHost` [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) může být:

* Hostitel: `www.domain.com`, `www.domain.com` odpovídá libovolnému portu.
* Hostitel se `*.domain.com`zástupnými `www.domain.com`znaky: , zápasy , `subdomain.domain.com`nebo `www.subdomain.domain.com` na libovolném portu.
* Port: `*:5000`, odpovídá portu 5000 libovolnému hostiteli.
* Hostitel a `www.domain.com:5000` port: nebo `*.domain.com:5000`, odpovídá hostiteli a portu.

Více parametrů lze zadat `RequireHost` `[Host]`pomocí nebo . Omezení odpovídá hostitelům platným pro libovolný parametr. Například `[Host("domain.com", "*.domain.com")]` `domain.com`shody `www.domain.com`, `subdomain.domain.com`a .

Následující kód `RequireHost` používá vyžadovat zadaného hostitele na trase:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

Následující kód používá `[Host]` atribut na řadiči vyžadovat některý ze zadaných hostitelů:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

Pokud `[Host]` je atribut použit na řadič i metodu akce:

* Atribut akce se používá.
* Atribut řadiče je ignorován.

## <a name="performance-guidance-for-routing"></a>Pokyny pro výkon pro směrování

Většina směrování byla aktualizována v ASP.NET Core 3.0 pro zvýšení výkonu.

Pokud má aplikace problémy s výkonem, je jako problém často podezření na směrování. Důvod, proč je podezření na směrování je, že rozhraní jako řadiče a Razor Pages sestavy množství času stráveného uvnitř rozhraní v jejich protokolování zpráv. Pokud existuje významný rozdíl mezi časem hlášeným řadiči a celkovým časem požadavku:

* Vývojáři eliminují kód aplikace jako zdroj problému.
* Je běžné předpokládat, že směrování je příčinou.

Směrování je testován o výkonu pomocí tisíců koncových bodů. Je nepravděpodobné, že typická aplikace narazí na problém s výkonem právě tím, že je příliš velká. Nejběžnější hlavní příčinou pomalého výkonu směrování je obvykle špatně se chovající vlastní middleware.

Tato následující ukázka kódu ukazuje základní techniku pro zúžení zdroje zpoždění:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Na časové směrování:

* Propojit každý middleware s kopií časování middleware je uvedeno v předchozím kódu.
* Přidejte jedinečný identifikátor, který koreluje data časování s kódem.

Jedná se o základní způsob, jak zúžit zpoždění, když `10ms`je to významné, například více než .  Odečtením `Time 2` `Time 1` času stráveného uvnitř `UseRouting` middlewaru od zpráv.

Následující kód používá kompaktnější přístup k předchozímu kódu časování:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Potenciálně nákladné funkce směrování

Následující seznam obsahuje určitý přehled o funkcích směrování, které jsou poměrně nákladné ve srovnání se základními šablonami tras:

* Regulární výrazy: Je možné psát regulární výrazy, které jsou složité nebo mají dlouhou provozní dobu s malým množstvím vstupu.

* Komplexní segmenty`{x}-{y}-{z}`( ): 
  * Jsou výrazně dražší než analýza pravidelného segmentu cesty URL.
  * Výsledkem je přidělení mnoha dalších podřetězců.
  * Logika složitého segmentu nebyla aktualizována v ASP.NET aktualizaci výkonu směrování Jádra 3.0.

* Synchronní přístup k datům: Mnoho složitých aplikací má přístup k databázi jako součást jejich směrování. ASP.NET core 2.2 a starší směrování nemusí poskytovat správné body rozšiřitelnosti pro podporu směrování přístupu k databázi. Například <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> a jsou synchronní. Rozšiřitelnost body, <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> jako <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> jsou a asynchronní.

## <a name="guidance-for-library-authors"></a>Pokyny pro autory knihovny

Tato část obsahuje pokyny pro autory knihovny, kteří jsou na základě směrování. Tyto podrobnosti jsou určeny k zajištění, že vývojáři aplikací mají dobré zkušenosti s používáním knihoven a architektur, které rozšiřují směrování.

### <a name="define-endpoints"></a>Definování koncových bodů

Chcete-li vytvořit rámec, který používá směrování pro porovnávání adres URL, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>začněte definováním uživatelského prostředí, které vychází nad .

**DO** stavět na <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>vrcholu . To umožňuje uživatelům vytvořit rámec s jinými funkcemi ASP.NET Core bez záměny. Každá šablona ASP.NET Core obsahuje směrování. Předpokládejme, že směrování je k dispozici a známé pro uživatele.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**DO** vrátit zapečetěný typ betonu <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>z volání, které implementuje `MapMyFramework(...)` . Většina `Map...` metod rozhraní se řídí tímto vzorem. Rozhraní: `IEndpointConventionBuilder`

* Umožňuje komosability metadat.
* Je zaměřena na různé metody rozšíření.

Deklarování vlastní typ umožňuje přidat vlastní funkce specifické pro architekturu tvůrce. Je v pořádku zabalit tvůrce deklarované ho rámcem a předávat mu hovory.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**Zvažte** psaní <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>vlastní . `EndpointDataSource`je primitivní základní úroveň nižší úrovně pro deklarování a aktualizaci kolekce koncových bodů. `EndpointDataSource`je výkonné rozhraní API používané řadiči a Razor Pages.

Testy směrování mají [základní příklad](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) neaktualaktua zdroje dat.

**NEPOKOUŠEJTE** se `EndpointDataSource` zaregistrovat ve výchozím nastavení. Vyžadovat, aby uživatelé <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>registrovali váš rámec v aplikaci . Filozofie směrování je, že nic není zahrnuta ve výchozím nastavení, a to `UseEndpoints` je místo pro registraci koncových bodů.

### <a name="creating-routing-integrated-middleware"></a>Vytváření middlewaru integrovaného s směrováním

**Zvažte** definování typů metadat jako rozhraní.

**DO** umožňují používat typy metadat jako atribut na třídy a metody.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Architektury, jako jsou řadiče a Razor Pages podporují použití atributy metadat typy a metody. Pokud deklarujete typy metadat:

* Zpřístupněte je jako [atributy](/dotnet/csharp/programming-guide/concepts/attributes/).
* Většina uživatelů je obeznámena s použitím atributů.

Deklarování typu metadat jako rozhraní přidává další vrstvu flexibility:

* Rozhraní lze skládat.
* Vývojáři mohou deklarovat své vlastní typy, které kombinují více zásad.

**Do** umožňují přepsat metadata, jak je znázorněno v následujícím příkladu:

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

Nejlepší způsob, jak postupovat podle těchto pokynů, je vyhnout se definování **metadat značek**:

* Nehledejte pouze přítomnost typu metadat.
* Definujte vlastnost metadat a zkontrolujte vlastnost.

Kolekce metadat je seřazena a podporuje přepsání podle priority. V případě řadičů metadata na metodu akce je nejkonkrétnější.

**DO,** aby middleware užitečné s a bez směrování.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

Jako příklad tohoto pokynu `UseAuthorization` zvažte middleware. Autorizace middleware umožňuje předat záložní politiky. <!-- shown where?  (shown here) --> Záložní zásady, pokud jsou zadány, platí pro oba:

* Koncové body bez zadané zásady.
* Požadavky, které neodpovídají koncovému bodu.

Díky autorizaci middleware užitečné mimo kontext směrování. Autorizace middleware může být použit pro tradiční middleware programování.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Směrování je zodpovědný za mapování požadavků NA ADRESY URI na koncové body a odesílání příchozích požadavků na tyto koncové body. Trasy jsou definovány v aplikaci a nakonfigurovány při spuštění aplikace. Trasa může volitelně extrahovat hodnoty z adresy URL obsažené v požadavku a tyto hodnoty lze pak použít pro zpracování požadavku. Pomocí informací o trase z aplikace, směrování je také schopen generovat adresy URL, které mapují na koncové body.

Chcete-li použít nejnovější scénáře směrování v ASP.NET jádrem 2.2, zadejte [verzi kompatibility](xref:mvc/compatibility-version) s registrací služeb MVC v `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Tato <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> možnost určuje, zda směrování by měl interně <xref:Microsoft.AspNetCore.Routing.IRouter>používat logiku založenou na koncovém bodu nebo logiku založenou na ASP.NET jádra 2.1 nebo staršího. Pokud je verze kompatibility nastavena na hodnotu `true`2.2 nebo novější, je výchozí hodnota . Nastavte hodnotu `false` tak, aby používala logiku předchozího směrování:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Další informace <xref:Microsoft.AspNetCore.Routing.IRouter>o směrování na základě této informace naleznete v [ASP.NET verzi tohoto tématu Core 2.1](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

> [!IMPORTANT]
> Tento dokument se týká nízkoúrovňového ASP.NET směrování Jádra. Informace o směrování Core MVC <xref:mvc/controllers/routing>ASP.NET naleznete v tématu . Informace o konvencích směrování <xref:razor-pages/razor-pages-conventions>v nástroji Razor Pages naleznete v tématu .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="routing-basics"></a>Základy směrování

Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné. Výchozí konvenční `{controller=Home}/{action=Index}/{id?}`trasa :

* Podporuje základní a popisné schéma směrování.
* Je užitečným výchozím bodem pro aplikace založené na uznatého.

Vývojáři obvykle přidávají další trasy do oblastí s vysokým provozem aplikace ve specializovaných situacích pomocí [směrování atributů](xref:mvc/controllers/routing#attribute-routing) nebo vyhrazených konvenčních tras. Mezi příklady specializovaných situací patří koncové body blogu a elektronického obchodu.

Webová api by měla používat směrování atributů k modelování funkčnosti aplikace jako sadu prostředků, kde jsou operace reprezentovány slovesy HTTP. To znamená, že mnoho operací, například GET a POST, na stejném logickém prostředku používá stejnou adresu URL. Směrování atributů poskytuje úroveň řízení, která je potřeba pečlivě navrhnout rozložení veřejného koncového bodu rozhraní API.

Aplikace Razor Pages používají výchozí konvenční směrování k zobrazování pojmenovaných prostředků ve složce *Stránky* aplikace. K dispozici jsou další konvence, které umožňují přizpůsobit chování směrování Razor Pages. Další informace naleznete v tématech <xref:razor-pages/index> a <xref:razor-pages/razor-pages-conventions>.

Podpora generování adres URL umožňuje aplikaci vyvíjet bez pevných kódovacích adres URL, které aplikaci spojují. Tato podpora umožňuje začít s základní konfiguraci směrování a úpravy tras po určení rozložení prostředků aplikace.

Směrování používá koncové`Endpoint` *body* ( ) k reprezentaci logických koncových bodů v aplikaci.

Koncový bod definuje delegáta pro zpracování požadavků a kolekci libovolných metadat. Metadata se používá implementovat průřezové obavy založené na zásadách a konfiguraci připojené ke každému koncovému bodu.

Systém směrování má následující charakteristiky:

* Syntaxe šablony trasy se používá k definování tras s tokenizovanými parametry trasy.
* Konfigurace koncového bodu konvenčního stylu a atributu je povolena.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>se používá k určení, zda parametr URL obsahuje platnou hodnotu pro dané omezení koncového bodu.
* Modely aplikací, jako jsou stránky MVC/Razor, registrují všechny své koncové body, které mají předvídatelnou implementaci scénářů směrování.
* Implementace směrování umožňuje rozhodování směrování, kdykoli je to požadováno v kanálu middlewaru.
* Middleware, který se zobrazí po směrování Middleware můžete zkontrolovat výsledek rozhodnutí směrování Middleware koncového bodu rozhodnutí pro daný požadavek URI.
* Je možné vytvořit výčet všech koncových bodů v aplikaci kdekoli v kanálu middlewaru.
* Aplikace může pomocí směrování generovat adresy URL (například pro přesměrování nebo odkazy) na základě informací o koncovém bodu a vyhnout se tak pevně zakódovaným adresám URL, což pomáhá udržovatelnost.
* Generování adres URL je založeno na adresách, které podporují libovolnou rozšiřitelnost:

  * Rozhraní API generátoru odkazů (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) lze vyřešit kdekoli pomocí vkládání [závislostí (DI)](xref:fundamentals/dependency-injection) pro generování adres URL.
  * Kde rozhraní API generátoru propojení není <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> k dispozici prostřednictvím DI, nabízí metody pro vytváření adres URL.

> [!NOTE]
> S vydáním směrování koncových bodů v ASP.NET Core 2.2 je propojení koncových bodů omezeno na akce a stránky MVC/Razor Pages. Rozšíření možností propojení koncových bodů je plánováno pro budoucí verze.

Směrování je připojeno k [middlewarovému](xref:fundamentals/middleware/index) kanálu třídou. <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> [ASP.NET Core MVC](xref:mvc/overview) přidává směrování do middlewarového kanálu jako součást své konfigurace a zpracovává směrování v aplikacích MVC a Razor Pages. Informace o tom, jak používat směrování jako samostatnou součást, naleznete v části [Použití middlewaru směrování.](#use-routing-middleware)

### <a name="url-matching"></a>Párování adres URL

Porovnávání adres URL je proces, kterým směrování odesílá příchozí požadavek do *koncového bodu*. Tento proces je založen na datech v cestě url, ale může být rozšířen tak, aby zvážil všechna data v požadavku. Možnost odesílat požadavky na samostatné obslužné rutiny je klíčem k škálování velikosti a složitosti aplikace.

Systém směrování v směrování koncových bodů je zodpovědný za všechna rozhodnutí o odeslání. Vzhledem k tomu, že middleware používá zásady založené na vybraném koncovém bodu, je důležité, aby jakékoli rozhodnutí, které může ovlivnit odesílání nebo použití zásad zabezpečení, bylo provedeno uvnitř systému směrování.

Při spuštění delegáta koncového bodu vlastnosti [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) jsou nastaveny na příslušné hodnoty na základě zpracování požadavku provádí tak daleko.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) je slovník *hodnot trasy* vytvořených z trasy. Tyto hodnoty jsou obvykle určeny tokenizací adresy URL a lze je použít k přijetí vstupu uživatele nebo k dalším rozhodnutím o odesílání uvnitř aplikace.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) je vlastnost taška další chdatné trasy. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>jsou k dispozici pro podporu asociace dat stavu s každou trasu tak, aby aplikace může rozhodovat na základě které trasy uzavřeno. Tyto hodnoty jsou definovány vývojářem a žádným způsobem **neovlivňují** chování směrování. Navíc hodnoty ukrytýv [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) může být libovolného typu, na rozdíl od [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), které musí být převoditelné do a z řetězců.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) je seznam tras, které se podílely na úspěšném porovnání požadavku. Trasy mohou být vnořeny uvnitř sebe. Vlastnost <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> odráží cestu přes logický strom tras, které vedly ke shodě. Obecně platí, že <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> první položka v je kolekce tras a by měla být použita pro generování adresy URL. Poslední položka <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> v je obslužná rutina postupu, která odpovídá.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>Generování adres URL pomocí linkgenerátoru

Generování adresy URL je proces, kterým může směrování vytvořit cestu URL na základě sady hodnot trasy. To umožňuje logické oddělení mezi koncovými body a adresy URL, které k nim přistupují.

Směrování koncového bodu zahrnuje<xref:Microsoft.AspNetCore.Routing.LinkGenerator>rozhraní API generátoru propojení ( ). <xref:Microsoft.AspNetCore.Routing.LinkGenerator>je singleton služba, která může být načtena z [DI](xref:fundamentals/dependency-injection). Rozhraní API lze použít mimo kontext vykonávajícího požadavku. MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> a scénáře, které <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>spoléhají na , jako jsou tag [helpery](xref:mvc/views/tag-helpers/intro), HTML helpery a [výsledky akcí](xref:mvc/controllers/actions), použijte generátor odkazů poskytnout možnosti generování propojení.

Generátor propojení je podpořen konceptem schémat *adres* a *adres*. Schéma adresy je způsob, jak určit koncové body, které by měly být považovány za generování propojení. Například scénáře název trasy a hodnoty trasy, které mnoho uživatelů zná ze stránek MVC/Razor, jsou implementovány jako schéma adres.

Odkaz generátor může odkaz na MVC / Razor Stránky akce a stránky prostřednictvím následujících metod rozšíření:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Přetížení těchto metod přijímá argumenty, `HttpContext`které zahrnují . Tyto metody jsou funkčně rovnocenné `Url.Action` a `Url.Page` nabízejí však další flexibilitu a možnosti.

Metody `GetPath*` jsou nejvíce `Url.Action` podobné `Url.Page` a v tom, že generují identifikátor URI obsahující absolutní cestu. Metody `GetUri*` vždy generovat absolutní URI obsahující schéma a hostitele. Metody, které `HttpContext` přijímají generovat identifikátor URI v kontextu vykonávajícího požadavku. Hodnoty trasy okolí, základní cesta URL, schéma a hostitel z vykonávajícího požadavku se používají, pokud nejsou přepsány.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>je volána s adresou. Generování identifikátoru URI probíhá ve dvou krocích:

1. Adresa je vázána na seznam koncových bodů, které odpovídají adrese.
1. Každý koncový bod `RoutePattern` je vyhodnocen, dokud není nalezen vzorek postupu, který odpovídá zadaným hodnotám. Výsledný výstup je kombinován s ostatními částmi URI dodanými do generátoru propojení a vráceny.

Metody poskytované <xref:Microsoft.AspNetCore.Routing.LinkGenerator> podporou standardních možností generování propojení pro jakýkoli typ adresy. Nejpohodlnější způsob, jak používat generátor propojení je prostřednictvím rozšíření metody, které provádějí operace pro určitý typ adresy.

| Metoda rozšíření   | Popis                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generuje identifikátor URI s absolutní cestou na základě zadaných hodnot. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generuje absolutní identifikátor URI na základě zadaných hodnot.             |

> [!WARNING]
> Věnujte pozornost následujícím <xref:Microsoft.AspNetCore.Routing.LinkGenerator> důsledkům volání metod:
>
> * V `GetUri*` konfiguraci aplikace, která neověřuje `Host` záhlaví příchozích požadavků, používejte metody rozšíření opatrně. Pokud `Host` záhlaví příchozích požadavků není ověřeno, nelze klientovi odeslat zpět do klienta v zobrazení nebo na stránce. Doporučujeme, aby všechny produkční aplikace `Host` nakonfigurovaly svůj server tak, aby ověřoval záhlaví proti známým platným hodnotám.
>
> * Používejte <xref:Microsoft.AspNetCore.Routing.LinkGenerator> s opatrností v `Map` middleware v kombinaci s nebo `MapWhen`. `Map*`změní základní cestu vykonávajícího požadavku, což má vliv na výstup generování propojení. Všechna <xref:Microsoft.AspNetCore.Routing.LinkGenerator> api umožňují určení základní cesty. Vždy zadejte prázdnou základní `Map*`cestu, která má vrátit vliv na generování propojení.

## <a name="differences-from-earlier-versions-of-routing"></a>Rozdíly od předchozích verzí směrování

Existuje několik rozdílů mezi směrováním koncových bodů v ASP.NET core 2.2 nebo novějšía a starší verze směrování v ASP.NET Core:

* Systém směrování koncových bodů <xref:Microsoft.AspNetCore.Routing.IRouter>nepodporuje rozšiřitelnost založenou na <xref:Microsoft.AspNetCore.Routing.Route>vlastnostech, včetně dědění z .

* Směrování koncových bodů nepodporuje [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). Chcete-li`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`pokračovat [compatibility version](xref:mvc/compatibility-version) v používání kompatibilní hospo-

* Směrování koncových bodů má při použití konvenčních tras odlišné chování pro caseing generovaných identifikátorů URI.

  Zvažte následující výchozí šablonu trasy:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Předpokládejme, že vygenerujete odkaz na akci pomocí následující trasy:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  S <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, tento kód `/blog/ReadPost/17`generuje IDENTIFIKÁTOR URI , který respektuje velká a malá písmena zapředpokladu hodnotu postupu. Směrování koncových bodů v ASP.NET jádrem `/Blog/ReadPost/17` 2.2 nebo novějším ("Blog" je velkými písmeny). Směrování koncových `IOutboundParameterTransformer` bodů poskytuje rozhraní, které lze použít k přizpůsobení tohoto chování globálně nebo k použití různých konvencí pro mapování adres URL.

  Další informace naleznete v [části Reference parametrtransformátoru.](#parameter-transformer-reference)

* Generování odkazů používané stránkami MVC/Razor pages s konvenčními trasami se při pokusu o propojení s řadičem nebo akcí nebo stránkou, která neexistuje, chová jinak.

  Zvažte následující výchozí šablonu trasy:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Předpokládejme, že vygenerujete odkaz na akci pomocí výchozí šablony s následujícími:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  S `IRouter`-based routing, výsledek `/Blog/ReadPost/17`je vždy `BlogController` , i v případě, `ReadPost` že neexistuje nebo nemá metodu akce. Podle očekávání směrování koncových bodů v ASP.NET Core `/Blog/ReadPost/17` 2.2 nebo novější vytváří, pokud existuje metoda akce. *Směrování koncového bodu však vytvoří prázdný řetězec, pokud akce neexistuje.* Koncepčně směrování koncových bodů nepředpokládá, že koncový bod existuje, pokud akce neexistuje.

* *Algoritmus zneplatnění okolní hodnoty* generování propojení se při použití se směrováním koncového bodu chová jinak.

  *Zneplatnění okolní hodnoty* je algoritmus, který rozhoduje o tom, které hodnoty trasy z aktuálně spuštěný požadavek (okolní hodnoty) lze použít v operacích generování propojení. Konvenční směrování vždy zneplatnilo další hodnoty trasy při propojení s jinou akcí. Směrování atributů nemělto chování před vydáním ASP.NET Core 2.2. V dřívějších verzích ASP.NET Core, odkazy na jinou akci, které používají stejné názvy parametrů trasy za následek chyby generování propojení. V ASP.NET jádrem 2.2 nebo novějším, obě formy směrování zneplatňují hodnoty při propojení s jinou akcí.

  Vezměme si následující příklad v ASP.NET jádrem 2.1 nebo starším. Při propojení s jinou akcí (nebo jinou stránkou) lze hodnoty trasy znovu použít nežádoucími způsoby.

  V */Pages/Store/Product.cshtml*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  V */Pages/Login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  Pokud je `/Store/Product/18` identifikátor URI v ASP.NET jádrem 2.1 nebo starším, `@Url.Page("/Login")` `/Login/18`je odkaz generovaný na stránce Store/Info od . Hodnota `id` 18 je znovu použita, i když cíl odkazu je zcela odlišná část aplikace. Hodnota `id` postupu v kontextu `/Login` stránky je pravděpodobně hodnota ID uživatele, nikoli hodnota ID produktu úložiště.

  V směrování koncového bodu s ASP.NET jádrem `/Login`2.2 nebo novějším je výsledkem . Hodnoty okolí se znovu nepoužijí, pokud je propojený cíl jinou akcí nebo stránkou.

* Syntaxe parametru trasy round-tripping: Lomítka se nekóduje při`**`použití syntaxe parametru catch-all s dvojitou hvězdičkou ( ).

  Během generování propojení systém směrování zakóduje hodnotu zachycenou v`**`parametru double-asterisk `{**myparametername}`( ) catch-all (například) s výjimkou lomítka. Double-hvězdička catch-all je podporován `IRouter`a založené směrování v ASP.NET Core 2.2 nebo novější.

  Jedna syntaxe parametru catch-all v předchozích verzích ASP.NET Core (`{*myparametername}`) zůstává podporována a lomítka jsou kódována.

  | Trasa              | Propojení generované pomocí<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts`(lomítko je zakódováno)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Middleware příklad

V následujícím příkladu middleware <xref:Microsoft.AspNetCore.Routing.LinkGenerator> používá rozhraní API k vytvoření odkazu na metodu akce, která uvádí seznam produktů úložiště. Pomocí generátoru odkazů vstřikováním `GenerateLink` do třídy a volání je k dispozici pro všechny třídy v aplikaci.

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

Většina aplikací vytváří <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> trasy voláním nebo jednou <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>z podobných metod rozšíření definovaných na . Všechny metody <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> rozšíření vytvořit instanci <xref:Microsoft.AspNetCore.Routing.Route> a přidat do kolekce tras.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nepřijímá parametr obslužné rutiny trasy. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>přidá pouze trasy, které <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>jsou zpracovány . Další informace o směrování v MVC naleznete v tématu <xref:mvc/controllers/routing>.

Následující příklad kódu je příkladem <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> volání používaného typickou ASP.NET definici trasy Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Tato šablona odpovídá cestě URL a extrahuje hodnoty trasy. Cesta například `/Products/Details/17` generuje následující hodnoty trasy: `{ controller = Products, action = Details, id = 17 }`.

Hodnoty trasy jsou určeny rozdělením cesty URL do segmentů a porovnáním každého segmentu s názvem *parametru trasy* v šabloně trasy. Parametry trasy jsou pojmenovány. Parametry definované uzavřením názvu parametru `{ ... }`do složených závorek .

Předchozí šablona může také odpovídat cestě `/` `{ controller = Home, action = Index }`URL a vytvářet hodnoty . K tomu `{controller}` dochází, `{action}` protože parametry a `id` a mají výchozí hodnoty a parametr trasy je volitelný. Znaménko`=`rovná se ( ) následované hodnotou za názvem parametru trasy definuje výchozí hodnotu parametru. Otazník`?`( ) za názvem parametru trasy definuje volitelný parametr.

Parametry trasy s výchozí hodnotou *vždy* vytvoří hodnotu trasy, když se trasa shoduje. Volitelné parametry nevytvářejí hodnotu trasy, pokud neexistuje odpovídající segment cesty URL. V části [Odkaz na šablonu trasy](#route-template-reference) naleznete důkladný popis scénářů a syntaxe šablony trasy.

V následujícím příkladu definuje `{id:int}` definice parametru trasy `id` omezení [trasy](#route-constraint-reference) pro parametr trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Tato šablona odpovídá `/Products/Details/17` cestě `/Products/Details/Apples`URL, jako je, ale ne . Omezení trasy <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementují a kontrolují hodnoty postupu, abyste je ověřili. V tomto příkladu `id` musí být hodnota trasy převoditelná na celé číslo. Viz [odkaz na omezení trasy](#route-constraint-reference) pro vysvětlení omezení postupu poskytované rámcem.

Další přetížení <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> hodnot accept `constraints` `dataTokens`pro `defaults`, a . Typické použití těchto parametrů je předat anonymně zadaný objekt, kde názvy vlastností anonymní typ odpovídají názvům parametrů postupu.

Následující <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> příklady vytvářejí ekvivalentní trasy:

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
> Vložková syntaxe pro definování omezení a výchozích hodnot může být vhodná pro jednoduché trasy. Existují však scénáře, jako jsou datové tokeny, které nejsou podporovány vložená syntaxe.

Následující příklad ukazuje několik dalších scénářů:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Předchozí šablona odpovídá cestě `/Blog/All-About-Routing/Introduction` URL jako a `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`extrahuje hodnoty . Výchozí hodnoty postupu `controller` `action` a jsou vytvořeny postupem, i když v šabloně nejsou žádné odpovídající parametry trasy. Výchozí hodnoty lze zadat v šabloně postupu. Parametr `article` trasy je definován jako *catch-all* vzhledem dvojité hvězdičky (`**`) před názvem parametru trasy. Catch-all parametry trasy zachytit zbytek cesty URL a může také odpovídat prázdný řetězec.

Následující příklad přidá omezení trasy a datové tokeny:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Předchozí šablona odpovídá cestě `/en-US/Products/5` URL jako a `{ controller = Products, action = Details, id = 5 }` extrahuje `{ locale = en-US }`hodnoty a datové tokeny .

![Místní Windows tokeny](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generování adresy URL třídy trasy

Třída <xref:Microsoft.AspNetCore.Routing.Route> může také provádět generování adres URL kombinací sady hodnot trasy se šablonou trasy. Toto je logicky opačný proces odpovídající cestě URL.

> [!TIP]
> Chcete-li lépe porozumět generování adresy URL, představte si, jakou adresu URL chcete vygenerovat, a pak přemýšlejte o tom, jak by šablona trasy odpovídala této adrese URL. Jaké hodnoty by byly vyrobeny? Toto je hrubý ekvivalent, jak <xref:Microsoft.AspNetCore.Routing.Route> funguje generování adresy URL ve třídě.

Následující příklad používá obecnou výchozí trasu ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

S hodnotami `{ controller = Products, action = List }`trasy `/Products/List` je generována adresa URL. Hodnoty trasy jsou nahrazeny odpovídajícími parametry trasy a tvoří cestu URL. Vzhledem k tomu, `id` že je volitelný parametr trasy, `id`je adresa URL úspěšně generována bez hodnoty pro .

S hodnotami `{ controller = Home, action = Index }`trasy `/` je generována adresa URL. Zakalené hodnoty trasy odpovídají výchozím hodnotám a segmenty odpovídající výchozím hodnotám jsou bezpečně vynechány.

Obě adresy URL generované round-trip s následující`/Home/Index` `/`definicí trasy ( a ) vytvářejí stejné hodnoty trasy, které byly použity ke generování adresy URL.

> [!NOTE]
> Aplikace využívající ASP.NET Core MVC by měla používat <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> ke generování adres URL namísto volání do směrování přímo.

Další informace o generování adres URL naleznete v části odkaz na [generování adres URL.](#url-generation-reference)

## <a name="use-routing-middleware"></a>Použití middlewaru směrování

Odkaz na [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) v souboru projektu aplikace.

Přidání směrování do servisního kontejneru v `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Trasy musí být konfigurovány v metodě. `Startup.Configure` Ukázková aplikace používá následující api:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Odpovídá pouze požadavkům HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

V následující tabulce jsou uvedeny odpovědi s danými identifikátory URI.

| Identifikátor URI                    | Odpověď                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Dobrý den! Hodnoty postupu: [operace, vytvoření], [id, 3] |
| `/package/track/-3`    | Dobrý den! Hodnoty trasy: [provoz, trať], [id, -3] |
| `/package/track/-3/`   | Dobrý den! Hodnoty trasy: [provoz, trať], [id, -3] |
| `/package/track/`      | Žádost neprojde, žádná shoda.              |
| `GET /hello/Joe`       | Ahoj, Joe!                                          |
| `POST /hello/Joe`      | Požadavek spadá přes, odpovídá pouze HTTP GET. |
| `GET /hello/Joe/Smith` | Žádost neprojde, žádná shoda.              |

Rámec poskytuje sadu rozšiřujících metod pro<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>vytváření tras ( ):

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

Metody `Map[Verb]` používají omezení k omezení trasy na sloveso HTTP v názvu metody. Viz například <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>a .

## <a name="route-template-reference"></a>Odkaz na šablonu trasy

Tokeny v rámci složených závorek (`{ ... }`) definují parametry trasy, které jsou *vázány,* pokud je trasa spárována. V segmentu trasy můžete definovat více než jeden parametr trasy, ale musí být odděleny hodnotou literálu. Například `{controller=Home}{action=Index}` není platná trasa, protože neexistuje žádná hodnota `{controller}` literálu mezi a `{action}`. Tyto parametry trasy musí mít název a mohou mít další atributy zadané.

Doslovný text jiný než parametry `{id}`trasy (například) a oddělovač `/` cesty se musí shodovat s textem v adrese URL. Porovnávání textu nerozlišuje malá a velká písmena a je založena na dekódované reprezentaci cesty url. Chcete-li porovnat oddělovač parametrů`{` `}`oddělovač parametrů doslovné trasy`{{` `}}`( nebo ), unikne oddělovači opakováním znaku ( nebo ).

Další důležité informace mají vzory adres URL, které se pokoušejí zachytit název souboru s volitelnou příponou souboru. Zvažte například `files/{filename}.{ext?}`šablonu . Pokud jsou `filename` hodnoty `ext` pro oba a existují, jsou vyplněny obě hodnoty. Pokud v adrese `filename` URL existuje pouze hodnota pro, trasa`.`se shoduje, protože koncové období ( ) je volitelné. Následující adresy URL odpovídají této trase:

* `/files/myFile.txt`
* `/files/myFile`

Hvězdičku (`*`) nebo dvojitou hvězdičku`**`( ) můžete použít jako předponu parametru trasy k vazbě na zbytek identifikátoru URI. Tyto parametry se nazývají *catch-all.* Například `blog/{**slug}` odpovídá všem identifikátorům `/blog` URI, který začíná a má `slug` za ním libovolnou hodnotu, která je přiřazena hodnotě postupu. Catch-all parametry mohou také odpovídat prázdný řetězec.

Parametr catch-all unikne příslušným znakům, když je trasa použita`/`ke generování adresy URL, včetně znaků oddělovače cest ( ). Například trasa `foo/{*path}` s `{ path = "my/path" }` hodnotami `foo/my%2Fpath`trasy generuje . Všimněte si uvozené lomítko. K oddělovacím znakům cesty `**` okolení použijte předponu parametru trasy. Trasa `foo/{**path}` `{ path = "my/path" }` s `foo/my/path`generuje .

Parametry postupu mohou mít *výchozí hodnoty* určené zadáním výchozí hodnoty za názvem`=`parametru odděleným znaménkem rovná se ( ). Definuje například `{controller=Home}` `Home` jako výchozí hodnotu pro `controller`. Výchozí hodnota se používá, pokud v adrese URL parametru není žádná hodnota. Parametry trasy jsou volitelné připojením otazníku (`?`) na konec `id?`názvu parametru, jako v . Rozdíl mezi volitelnými hodnotami a výchozími parametry postupu spoá,&mdash;že parametr trasy s výchozí hodnotou vždy vytvoří hodnotu, volitelný parametr má hodnotu pouze v případě, že hodnota je poskytnuta adresou URL požadavku.

Parametry trasy mohou mít omezení, která musí odpovídat hodnotě trasy vázané z adresy URL. Přidání dvojtečky (`:`) a názvu omezení za název parametru trasy určuje *vsazené omezení* parametru trasy. Pokud omezení vyžaduje argumenty, jsou uzavřeny v závorcích (`(...)`) za názvem omezení. Vícevřádkových omezení lze zadat připojením`:`jinédvojtečky ( ) a názvu omezení.

Název omezení a argumenty jsou <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> předány službě <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> k vytvoření instance pro použití při zpracování adres URL. Například šablona `blog/{article:minlength(10)}` trasy určuje `minlength` omezení s `10`argumentem . Další informace o omezeních postupu a seznam omezení poskytovaných rámcem naleznete v referenční části [omezení trasy.](#route-constraint-reference)

Parametry trasy mohou mít také parametrové transformátory, které transformují hodnotu parametru při generování odkazů a odpovídajících akcí a stránek na adresy URL. Podobně jako omezení mohou být transformátory parametrů přidány do parametru trasy přidáním dvojtečky (`:`) a názvu transformátoru za název parametru trasy. Například šablona `blog/{article:slugify}` trasy určuje `slugify` transformátor. Další informace o transformátorech parametrů naleznete v [části Reference parametrtransformátoru.](#parameter-transformer-reference)

Následující tabulka ukazuje ukázkové šablony tras a jejich chování.

| Šablona postupu                           | Příklad odpovídajícího identifikátoru URI    | Identifikátor URI požadavku&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Odpovídá pouze jedné `/hello`cestě .                                     |
| `{Page=Home}`                            | `/`                     | Odpovídá a `Page` `Home`nastavuje na .                                         |
| `{Page=Home}`                            | `/Contact`              | Odpovídá a `Page` `Contact`nastavuje na .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mapuje `Products` na `List` ovladač a akci.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapy k `Products` ovladači `Details` `id` a akci (nastaveno na 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapy na `Home` řadič `Index` a`id` metodu (je ignorována).        |

Použití šablony je obecně nejjednodušší přístup k směrování. Omezení a výchozí hodnoty lze zadat také mimo šablonu trasy.

> [!TIP]
> Povolte [protokolování,](xref:fundamentals/logging/index) abyste viděli, jak integrované <xref:Microsoft.AspNetCore.Routing.Route>implementace směrování, například , shodu jsou požadavky na shodu.

## <a name="reserved-routing-names"></a>Rezervované názvy směrování

Následující klíčová slova jsou vyhrazené názvy a nelze je použít jako názvy nebo parametry trasy:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Odkaz na omezení postupu

Omezení trasy se spustí, když došlo ke shodě s příchozí adresou URL a cesta url je tokenizována na hodnoty trasy. Omezení postupu obvykle kontrolují hodnotu postupu přidruženou prostřednictvím šablony postupu a rozhodují o tom, zda je hodnota přijatelná. Některá omezení postupu používají data mimo hodnotu postupu k posouzení, zda lze požadavek směrovat. Například <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> může přijmout nebo odmítnout požadavek na základě jeho slovesa HTTP. Omezení se používají v požadavcích na směrování a generování propojení.

> [!WARNING]
> Nepoužívejte omezení pro **ověření vstupu**. Pokud omezení se používají pro **ověření vstupu**, neplatné vstupní výsledky v *404 - nebyl nalezen* odpověď namísto *400 - chybný požadavek* s příslušnou chybovou zprávu. Omezení trasy se používají k **rozdvojení** podobných tras, nikoli k ověření vstupů pro určitou trasu.

Následující tabulka ukazuje ukázková omezení trasy a jejich očekávané chování.

| omezení | Příklad | Příklady shod | Poznámky |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Odpovídá libovolnému celému číselníku. |
| `bool` | `{active:bool}` | `true`, `FALSE` | Shody `true` nebo 'false. Case-insensitive. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platné `DateTime` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platné `decimal` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platné `double` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platné `float` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Odpovídá platné `Guid` hodnotě. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Odpovídá platné `long` hodnotě. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Řetězec musí být alespoň 4 znaky. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Řetězec má maximálně 8 znaků. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Řetězec musí mít přesně 12 znaků. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Řetězec musí být alespoň 8 a má maximálně 16 znaků. |
| `min(value)` | `{age:min(18)}` | `19` | Hodnota celého čísla musí být alespoň 18. |
| `max(value)` | `{age:max(120)}` | `91` | Celá hodnota celkem 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Hodnota celého čísla musí být alespoň 18 a maximálně 120. |
| `alpha` | `{name:alpha}` | `Rick` | Řetězec se musí skládat z `a` - `z`jednoho nebo více abecedních znaků .  Case-insensitive. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Řetězec musí odpovídat regulárnímu výrazu. Podívejte se na tipy pro definici regulárního výrazu. |
| `required` | `{name:required}` | `Rick` | Slouží k vynucení, že hodnota neparametr je k dispozici během generování adresy URL. |

Na jeden parametr lze použít více omezení oddělených dvojtečkou. Například následující omezení omezuje parametr na celou hodnotu 1 nebo vyšší:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Omezení trasy, která ověřují adresu URL a jsou `int` `DateTime`převedena na typ CLR (například nebo ) vždy používají invariantní jazykovou verzi. Tato omezení předpokládají, že adresu URL je nelokalizovatelné. Omezení trasy poskytovaná rámcem nemění hodnoty uložené v hodnotách trasy. Všechny hodnoty trasy analyzované z adresy URL jsou uloženy jako řetězce. Například `float` omezení se pokusí převést hodnotu trasy na float, ale převedená hodnota se používá pouze k ověření, že může být převedena na float.

## <a name="regular-expressions"></a>Regulární výrazy

Rozhraní ASP.NET Core `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` přidává k konstruktoru regulárního výrazu. Viz <xref:System.Text.RegularExpressions.RegexOptions> popis těchto členů.

Regulární výrazy používají oddělovače a tokeny podobné těm, které používají směrování a jazyk C#. Tokeny regulárního výrazu musí být uvozeny. Použití regulárního výrazu `^\d{3}-\d{2}-\d{4}$` v směrování:

* Výraz musí mít jednotlivé `\` znaky zpětného lomítka `\\` uvedené v řetězci jako dvojité znaky zpětného lomítka ve zdrojovém kódu.
* Regulární výraz `\\` nás musí `\` uniknout znak uzamykat řetězec.
* Regulární výraz nevyžaduje `\\` při použití [doslovných řetězcových literál](/dotnet/csharp/language-reference/keywords/string).

Chcete-li uniknout znakům `{` `}`oddělovače parametrů směrování `{{`, `}` `[[`, `]]` `[`, `]`zdvojnásobte znaky ve výrazu , , . V následující tabulce je uveden regulární výraz a uvozená verze:

| Regulární výraz    | Uvozené regulární výraz     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy používané při směrování `^` často začínají znakem stříšky a shodují se s počáteční pozicí řetězce. Výrazy často končí `$` znakem dolaru a shodují se na konci řetězce. Znaky `^` `$` a zajišťují, že regulární výraz odpovídá celé hodnotě parametru trasy. Bez `^` znaků `$` a regulární výraz odpovídá libovolnému podřetězci v řetězci, což je často nežádoucí. V následující tabulce jsou uvedeny příklady a vysvětleny, proč se shodují nebo neshodují.

| Expression   | Řetězec    | Shoda | Poznámka               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | Mz        | Ano   | Odpovídá výrazu    |
| `[a-z]{2}`   | MZ        | Ano   | Neuznačují malá a velká písmen    |
| `^[a-z]{2}$` | hello     | Ne    | Viz `^` `$` a výše |
| `^[a-z]{2}$` | 123abc456 | Ne    | Viz `^` `$` a výše |

Další informace o syntaxi regulárních výrazů naleznete [v tématu .NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr na známou sadu možných hodnot, použijte regulární výraz. Například `{action:regex(^(list|get|create)$)}` pouze odpovídá `action` hodnotě `list` `get`trasy `create`do , , nebo . Pokud je předán do slovníku `^(list|get|create)$` omezení, řetězec je ekvivalentní. Omezení, která jsou předána ve slovníku omezení (není vnímák v rámci šablony), které neodpovídají jedné ze známých omezení, jsou také považovány za regulární výrazy.

## <a name="custom-route-constraints"></a>Vlastní omezení trasy

Kromě předdefinovaných omezení trasy lze implementací <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozhraní vytvořit omezení vlastní trasy. Rozhraní <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> obsahuje jednu `Match`metodu `true` , která vrátí, `false` pokud je splněna omezení a jinak.

Chcete-li <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>použít vlastní , musí být typ omezení <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> postupu registrován u aplikace v kontejneru služeb aplikace. A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> je slovník, který mapuje <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> klíče omezení trasy na implementace, které tato omezení ověřují. Aplikace <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> lze aktualizovat buď `Startup.ConfigureServices` jako součást [služby. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) volání nebo <xref:Microsoft.AspNetCore.Routing.RouteOptions> konfigurací `services.Configure<RouteOptions>`přímo s . Příklad:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Omezení pak lze použít na trasy obvyklým způsobem pomocí názvu určeného při registraci typu omezení. Příklad:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Odkaz na parametr transformátor

Parametrické transformátory:

* Spustit při generování propojení <xref:Microsoft.AspNetCore.Routing.Route>pro .
* Implementovat `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* Jsou konfigurovány pomocí <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Vezměte hodnotu trasy parametru a transformujte ji na novou hodnotu řetězce.
* Výsledkem je použití transformované hodnoty ve generovaném propojení.

`slugify` Například vlastní parametr transformátor `blog\{article:slugify}` ve `Url.Action(new { article = "MyTestArticle" })` vzoru `blog\my-test-article`trasy s generuje .

Chcete-li použít parametrový transformátor ve <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> vzoru `Startup.ConfigureServices`trasy, nakonfigurujte jej nejprve pomocí aplikace :

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Parametr transformátory jsou používány v rámci transformovat URI, kde se řeší koncový bod. Například ASP.NET Core MVC používá parametr transformátory k `area`transformaci `controller` `action`hodnoty `page`trasy použité tak, aby odpovídala , , a .

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

S předchozí trasou je `SubscriptionManagementController.GetAll` akce spárována `/subscription-management/get-all`s identifikátorem URI . Transformátor parametrů nezmění hodnoty trasy použité ke generování propojení. Například `Url.Action("GetAll", "SubscriptionManagement")` výstupy `/subscription-management/get-all`.

ASP.NET Core poskytuje konvencí rozhraní API pro použití parametrových transformátorů s generovanými trasami:

* ASP.NET Core MVC `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` má konvenci rozhraní API. Tato konvence platí zadaný parametr transformátor u všech tras atributů v aplikaci. Parametr transformer transformuje atribut route tokeny jako jsou nahrazeny. Další informace naleznete [v tématu Použití transformátoru parametrů k přizpůsobení nahrazení tokenu](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` má konvenci rozhraní API. Tato konvence aplikuje zadaný parametr transformátoru na všechny automaticky zjištěné Razor Pages. Parametr transformer transformuje segmenty názvů složek a souborů tras Razor Pages. Další informace naleznete [v tématu Použití transformátoru parametrů k přizpůsobení tras stránek](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Odkaz na generování adresy URL

Následující příklad ukazuje, jak vygenerovat propojení s trasou danou <xref:Microsoft.AspNetCore.Routing.RouteCollection>slovníkem hodnot trasy a .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Generované <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> na konci předchozího vzorku je `/package/create/123`. Slovník poskytuje hodnoty `operation` trasy `id` a v šabloně "Track `package/{operation}/{id}`Package Route" . Podrobnosti naleznete v ukázkovém kódu v části [Use Routing Middleware](#use-routing-middleware) nebo v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Druhý parametr konstruktoru <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> je kolekce *okolních hodnot*. Okolní hodnoty jsou vhodné použít, protože omezují počet hodnot, které musí vývojář zadat v kontextu požadavku. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení. V ASP.NET `About` akce aplikace Core MVC `HomeController`aplikace , není nutné zadat hodnotu trasy `Index` kontroléru pro propojení s akcí,&mdash;která `Home` se používá okolní hodnota.

Hodnoty okolí, které neodpovídají parametru, jsou ignorovány. Okolní hodnoty jsou také ignorovány, když explicitně zadaný hodnota přepíše hodnotu okolí. Párování probíhá zleva doprava v adrese URL.

Hodnoty explicitně zadaných, ale které neodpovídají segmentu trasy, jsou přidány do řetězce dotazu. V následující tabulce je uveden výsledek `{controller}/{action}/{id?}`při použití šablony trasy .

| Okolní hodnoty                     | Explicitní hodnoty                        | Výsledek                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| regulátor = "Domů"                | akce = "O"                       | `/Home/About`           |
| regulátor = "Domů"                | controller = "Objednávka", akce = "O" | `/Order/About`          |
| regulátor = "Domů", barva = "Červená" | akce = "O"                       | `/Home/About`           |
| regulátor = "Domů"                | akce = "O", barva = "Červená"        | `/Home/About?color=Red` |

Pokud má trasa výchozí hodnotu, která neodpovídá parametru, a tato hodnota je explicitně poskytnuta, musí odpovídat výchozí hodnotě:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Generování propojení generuje propojení pouze pro tuto trasu, pokud jsou k dispozici odpovídající hodnoty pro `controller` a `action` jsou k dispozici.

## <a name="complex-segments"></a>Složité segmenty

Složité segmenty (například) `[Route("/x{token}y")]`jsou zpracovány porovnáním literály zprava doleva nechamtivým způsobem. Podrobné vysvětlení, jak jsou složité segmenty spárovány, naleznete v [tomto kódu.](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) [Ukázka kódu](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) není používán ASP.NET Core, ale poskytuje dobré vysvětlení složitých segmentů.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Směrování je zodpovědný za mapování požadavků NA IDENTIFIKÁTORY URI na obslužné rutiny trasy a odeslání příchozích požadavků. Trasy jsou definovány v aplikaci a nakonfigurovány při spuštění aplikace. Trasa může volitelně extrahovat hodnoty z adresy URL obsažené v požadavku a tyto hodnoty lze pak použít pro zpracování požadavku. Pomocí nakonfigurovaných tras z aplikace je směrování schopno generovat adresy URL, které se mapují na obslužné rutiny trasy.

Chcete-li použít nejnovější scénáře směrování v ASP.NET jádrem 2.1, zadejte [verzi kompatibility](xref:mvc/compatibility-version) s registrací služeb MVC v `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Tento dokument se týká nízkoúrovňového ASP.NET směrování Jádra. Informace o směrování Core MVC <xref:mvc/controllers/routing>ASP.NET naleznete v tématu . Informace o konvencích směrování <xref:razor-pages/razor-pages-conventions>v nástroji Razor Pages naleznete v tématu .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="routing-basics"></a>Základy směrování

Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné. Výchozí konvenční `{controller=Home}/{action=Index}/{id?}`trasa :

* Podporuje základní a popisné schéma směrování.
* Je užitečným výchozím bodem pro aplikace založené na uznatého.

Vývojáři obvykle přidávají další trasy do oblastí s vysokým provozem aplikace ve specializovaných situacích (například koncové body blogu a elektronického obchodu) pomocí [směrování atributů](xref:mvc/controllers/routing#attribute-routing) nebo vyhrazených konvenčních tras.

Webová api by měla používat směrování atributů k modelování funkčnosti aplikace jako sadu prostředků, kde jsou operace reprezentovány slovesy HTTP. To znamená, že mnoho operací (například GET, POST) na stejném logickém prostředku bude používat stejnou adresu URL. Směrování atributů poskytuje úroveň řízení, která je potřeba pečlivě navrhnout rozložení veřejného koncového bodu rozhraní API.

Aplikace Razor Pages používají výchozí konvenční směrování k zobrazování pojmenovaných prostředků ve složce *Stránky* aplikace. K dispozici jsou další konvence, které umožňují přizpůsobit chování směrování Razor Pages. Další informace naleznete v tématech <xref:razor-pages/index> a <xref:razor-pages/razor-pages-conventions>.

Podpora generování adres URL umožňuje aplikaci vyvíjet bez pevných kódovacích adres URL, které aplikaci spojují. Tato podpora umožňuje začít s základní konfiguraci směrování a úpravy tras po určení rozložení prostředků aplikace.

Směrování používá implementace <xref:Microsoft.AspNetCore.Routing.IRouter> postupů:

* Mapuje příchozí požadavky na *obslužné rutiny*trasy .
* Vygenerujte adresy URL použité v odpovědích.

Ve výchozím nastavení má aplikace jednu kolekci tras. Při doručení požadavku jsou trasy v kolekci zpracovány v pořadí, v jakém existují v kolekci. Rozhraní framework se pokusí porovnat adresu URL příchozího požadavku s <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> postupem v kolekci voláním metody na každé trase v kolekci. Odpověď může použít směrování ke generování adres URL (například pro přesměrování nebo propojení) na základě informací o postupu a vyhnout se tak pevně zakódovaným adresám URL, což pomáhá udržovatelnost.

Systém směrování má následující charakteristiky:

* Syntaxe šablony trasy se používá k definování tras s tokenizovanými parametry trasy.
* Konfigurace koncového bodu konvenčního stylu a atributu je povolena.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>se používá k určení, zda parametr URL obsahuje platnou hodnotu pro dané omezení koncového bodu.
* Modely aplikací, jako jsou stránky MVC/Razor, registrují všechny jejich trasy, které mají předvídatelnou implementaci scénářů směrování.
* Odpověď může použít směrování ke generování adres URL (například pro přesměrování nebo propojení) na základě informací o postupu a vyhnout se tak pevně zakódovaným adresám URL, což pomáhá udržovatelnost.
* Generování adresy URL je založeno na trasách, které podporují libovolnou rozšiřitelnost. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>nabízí metody vytváření adres URL.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
Směrování je připojeno k [middlewarovému](xref:fundamentals/middleware/index) kanálu třídou. <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> [ASP.NET Core MVC](xref:mvc/overview) přidává směrování do middlewarového kanálu jako součást své konfigurace a zpracovává směrování v aplikacích MVC a Razor Pages. Informace o tom, jak používat směrování jako samostatnou součást, naleznete v části [Použití middlewaru směrování.](#use-routing-middleware)

### <a name="url-matching"></a>Párování adres URL

Porovnávání adres URL je proces, kterým směrování odešle příchozí požadavek na *obslužnou rutinu*. Tento proces je založen na datech v cestě url, ale může být rozšířen tak, aby zvážil všechna data v požadavku. Možnost odesílat požadavky na samostatné obslužné rutiny je klíčem k škálování velikosti a složitosti aplikace.

Příchozí požadavky zadají <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, který <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> volá metodu na každé trase v pořadí. Instance <xref:Microsoft.AspNetCore.Routing.IRouter> zvolí, zda *zpracovat* požadavek nastavením [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) <xref:Microsoft.AspNetCore.Http.RequestDelegate>na non-null . Pokud trasa nastaví obslužnou rutinu pro požadavek, zpracování trasy se zastaví a obslužná rutina je vyvolána ke zpracování požadavku. Pokud není nalezena žádná obslužná rutina trasy pro zpracování požadavku, middleware předá požadavek na další middleware v kanálu požadavku.

Primární vstup <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> do je [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) spojené s aktuální požadavek. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) a [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) jsou výstupy nastavené po shody trasy.

Shoda, která <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> volá také nastaví vlastnosti [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) na příslušné hodnoty na základě zpracování požadavku provedeného tak daleko.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) je slovník *hodnot trasy* vytvořených z trasy. Tyto hodnoty jsou obvykle určeny tokenizací adresy URL a lze je použít k přijetí vstupu uživatele nebo k dalším rozhodnutím o odesílání uvnitř aplikace.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) je vlastnost taška další chdatné trasy. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>jsou k dispozici pro podporu asociace dat stavu s každou trasu tak, aby aplikace může rozhodovat na základě které trasy uzavřeno. Tyto hodnoty jsou definovány vývojářem a žádným způsobem **neovlivňují** chování směrování. Navíc hodnoty ukrytýv [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) může být libovolného typu, na rozdíl od [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), které musí být převoditelné do a z řetězců.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) je seznam tras, které se podílely na úspěšném porovnání požadavku. Trasy mohou být vnořeny uvnitř sebe. Vlastnost <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> odráží cestu přes logický strom tras, které vedly ke shodě. Obecně platí, že <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> první položka v je kolekce tras a by měla být použita pro generování adresy URL. Poslední položka <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> v je obslužná rutina postupu, která odpovídá.

<a name="lg"></a>

### <a name="url-generation"></a>Generování adresy URL

Generování adresy URL je proces, kterým může směrování vytvořit cestu URL na základě sady hodnot trasy. To umožňuje logické oddělení mezi obslužnými rutinami trasy a adresy URL, které k nim přistupují.

Generování adres URL následuje podobný iterativní proces, ale začíná voláním uživatelského nebo rámcového kódu do <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> metody kolekce tras. Každá *trasa* má svou <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> metodu volánou postupně, dokud není vrácena hodnota non-null. <xref:Microsoft.AspNetCore.Routing.VirtualPathData>

Primární vstupy <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> jsou:

* [VirtualPathContext.httpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Trasy primárně používají hodnoty trasy <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> poskytnuté <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> a rozhodnout, zda je možné generovat adresu URL a jaké hodnoty zahrnout. Jsou <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> sada hodnot postupu, které byly vytvořeny z odpovídající aktuální požadavek. Naopak jsou <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> hodnoty trasy, které určují, jak generovat požadovanou adresu URL pro aktuální operaci. Poskytuje <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> se v případě, že by trasa měla získat služby nebo další údaje spojené s aktuálním kontextem.

> [!TIP]
> Představte si [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) jako sadu přepsání pro [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). Generování adresy URL se pokouší znovu použít hodnoty trasy z aktuálního požadavku ke generování adres URL pro propojení pomocí stejných hodnot trasy nebo trasy.

Výstup <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> je <xref:Microsoft.AspNetCore.Routing.VirtualPathData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData>je paralelou <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData>obsahuje <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> pro výstupní adresu URL a některé další vlastnosti, které by měly být nastaveny podle trasy.

Vlastnost [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) obsahuje *virtuální cestu* vytvořenou trasou. V závislosti na vašich potřebách může být nutné cestu dále zpracovat. Pokud chcete vykreslit vygenerovanou adresu URL v HTML, předložte základní cestu aplikace.

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) je odkaz na trasu, která úspěšně vygenerovala adresu URL.

Vlastnosti [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) jsou slovníkem dalších dat souvisejících s postupem, který vygeneroval adresu URL. Toto je paralela [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Vytvoření tras

Směrování poskytuje <xref:Microsoft.AspNetCore.Routing.Route> třídu jako <xref:Microsoft.AspNetCore.Routing.IRouter>standardní implementaci . <xref:Microsoft.AspNetCore.Routing.Route>Používá syntaxi *šablony trasy* k definování vzorů tak, aby odpovídaly cestě URL při <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> volání. <xref:Microsoft.AspNetCore.Routing.Route>používá stejnou šablonu trasy ke <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> generování adresy URL při volání.

Většina aplikací vytváří <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> trasy voláním nebo jednou <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>z podobných metod rozšíření definovaných na . Všechny metody <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> rozšíření vytvořit instanci <xref:Microsoft.AspNetCore.Routing.Route> a přidat do kolekce tras.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nepřijímá parametr obslužné rutiny trasy. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>přidá pouze trasy, které <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>jsou zpracovány . Výchozí obslužná rutina `IRouter`je a obslužná rutina nemusí požadavek zpracovat. Například ASP.NET Core MVC je obvykle nakonfigurován jako výchozí obslužná rutina, která zpracovává pouze požadavky, které odpovídají dostupnému řadiči a akci. Další informace o směrování v MVC naleznete v tématu <xref:mvc/controllers/routing>.

Následující příklad kódu je příkladem <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> volání používaného typickou ASP.NET definici trasy Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Tato šablona odpovídá cestě URL a extrahuje hodnoty trasy. Cesta například `/Products/Details/17` generuje následující hodnoty trasy: `{ controller = Products, action = Details, id = 17 }`.

Hodnoty trasy jsou určeny rozdělením cesty URL do segmentů a porovnáním každého segmentu s názvem *parametru trasy* v šabloně trasy. Parametry trasy jsou pojmenovány. Parametry definované uzavřením názvu parametru `{ ... }`do složených závorek .

Předchozí šablona může také odpovídat cestě `/` `{ controller = Home, action = Index }`URL a vytvářet hodnoty . K tomu `{controller}` dochází, `{action}` protože parametry a `id` a mají výchozí hodnoty a parametr trasy je volitelný. Znaménko`=`rovná se ( ) následované hodnotou za názvem parametru trasy definuje výchozí hodnotu parametru. Otazník`?`( ) za názvem parametru trasy definuje volitelný parametr.

Parametry trasy s výchozí hodnotou *vždy* vytvoří hodnotu trasy, když se trasa shoduje. Volitelné parametry nevytvářejí hodnotu trasy, pokud neexistuje odpovídající segment cesty URL. V části [Odkaz na šablonu trasy](#route-template-reference) naleznete důkladný popis scénářů a syntaxe šablony trasy.

V následujícím příkladu definuje `{id:int}` definice parametru trasy `id` omezení [trasy](#route-constraint-reference) pro parametr trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Tato šablona odpovídá `/Products/Details/17` cestě `/Products/Details/Apples`URL, jako je, ale ne . Omezení trasy <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementují a kontrolují hodnoty postupu, abyste je ověřili. V tomto příkladu `id` musí být hodnota trasy převoditelná na celé číslo. Viz [odkaz na omezení trasy](#route-constraint-reference) pro vysvětlení omezení postupu poskytované rámcem.

Další přetížení <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> hodnot accept `constraints` `dataTokens`pro `defaults`, a . Typické použití těchto parametrů je předat anonymně zadaný objekt, kde názvy vlastností anonymní typ odpovídají názvům parametrů postupu.

Následující <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> příklady vytvářejí ekvivalentní trasy:

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
> Vložková syntaxe pro definování omezení a výchozích hodnot může být vhodná pro jednoduché trasy. Existují však scénáře, jako jsou datové tokeny, které nejsou podporovány vložená syntaxe.

Následující příklad ukazuje několik dalších scénářů:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Předchozí šablona odpovídá cestě `/Blog/All-About-Routing/Introduction` URL jako a `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`extrahuje hodnoty . Výchozí hodnoty postupu `controller` `action` a jsou vytvořeny postupem, i když v šabloně nejsou žádné odpovídající parametry trasy. Výchozí hodnoty lze zadat v šabloně postupu. Parametr `article` trasy je definován jako *catch-all* vzhledem hvězdičky`*`( ) před názvem parametru trasy. Catch-all parametry trasy zachytit zbytek cesty URL a může také odpovídat prázdný řetězec.

Následující příklad přidá omezení trasy a datové tokeny:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Předchozí šablona odpovídá cestě `/en-US/Products/5` URL jako a `{ controller = Products, action = Details, id = 5 }` extrahuje `{ locale = en-US }`hodnoty a datové tokeny .

![Místní Windows tokeny](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generování adresy URL třídy trasy

Třída <xref:Microsoft.AspNetCore.Routing.Route> může také provádět generování adres URL kombinací sady hodnot trasy se šablonou trasy. Toto je logicky opačný proces odpovídající cestě URL.

> [!TIP]
> Chcete-li lépe porozumět generování adresy URL, představte si, jakou adresu URL chcete vygenerovat, a pak přemýšlejte o tom, jak by šablona trasy odpovídala této adrese URL. Jaké hodnoty by byly vyrobeny? Toto je hrubý ekvivalent, jak <xref:Microsoft.AspNetCore.Routing.Route> funguje generování adresy URL ve třídě.

Následující příklad používá obecnou výchozí trasu ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

S hodnotami `{ controller = Products, action = List }`trasy `/Products/List` je generována adresa URL. Hodnoty trasy jsou nahrazeny odpovídajícími parametry trasy a tvoří cestu URL. Vzhledem k tomu, `id` že je volitelný parametr trasy, `id`je adresa URL úspěšně generována bez hodnoty pro .

S hodnotami `{ controller = Home, action = Index }`trasy `/` je generována adresa URL. Zakalené hodnoty trasy odpovídají výchozím hodnotám a segmenty odpovídající výchozím hodnotám jsou bezpečně vynechány.

Obě adresy URL generované round-trip s následující`/Home/Index` `/`definicí trasy ( a ) vytvářejí stejné hodnoty trasy, které byly použity ke generování adresy URL.

> [!NOTE]
> Aplikace využívající ASP.NET Core MVC by měla používat <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> ke generování adres URL namísto volání do směrování přímo.

Další informace o generování adres URL naleznete v části odkaz na [generování adres URL.](#url-generation-reference)

## <a name="use-routing-middleware"></a>Použití middlewaru směrování

Odkaz na [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) v souboru projektu aplikace.

Přidání směrování do servisního kontejneru v `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Trasy musí být konfigurovány v metodě. `Startup.Configure` Ukázková aplikace používá následující api:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Odpovídá pouze požadavkům HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

V následující tabulce jsou uvedeny odpovědi s danými identifikátory URI.

| Identifikátor URI                    | Odpověď                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Dobrý den! Hodnoty postupu: [operace, vytvoření], [id, 3] |
| `/package/track/-3`    | Dobrý den! Hodnoty trasy: [provoz, trať], [id, -3] |
| `/package/track/-3/`   | Dobrý den! Hodnoty trasy: [provoz, trať], [id, -3] |
| `/package/track/`      | Žádost neprojde, žádná shoda.              |
| `GET /hello/Joe`       | Ahoj, Joe!                                          |
| `POST /hello/Joe`      | Požadavek spadá přes, odpovídá pouze HTTP GET. |
| `GET /hello/Joe/Smith` | Žádost neprojde, žádná shoda.              |

Pokud konfigurujete jednu <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> trasu, volání předávání v instanci. `IRouter` Nebudete muset používat <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

Rámec poskytuje sadu rozšiřujících metod pro<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>vytváření tras ( ):

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

Některé z uvedených <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>metod, <xref:Microsoft.AspNetCore.Http.RequestDelegate>například , vyžadují . Používá <xref:Microsoft.AspNetCore.Http.RequestDelegate> se jako *obslužná rutina postupu,* když se trasa shoduje. Jiné metody v této rodině umožňují konfiguraci kanálu middlewaru pro použití jako obslužná rutina trasy. Pokud `Map*` metoda nepřijímá obslužnou <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>rutinu, <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>například , používá .

Metody `Map[Verb]` používají omezení k omezení trasy na sloveso HTTP v názvu metody. Viz například <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>a .

## <a name="route-template-reference"></a>Odkaz na šablonu trasy

Tokeny v rámci složených závorek (`{ ... }`) definují parametry trasy, které jsou *vázány,* pokud je trasa spárována. V segmentu trasy můžete definovat více než jeden parametr trasy, ale musí být odděleny hodnotou literálu. Například `{controller=Home}{action=Index}` není platná trasa, protože neexistuje žádná hodnota `{controller}` literálu mezi a `{action}`. Tyto parametry trasy musí mít název a mohou mít další atributy zadané.

Doslovný text jiný než parametry `{id}`trasy (například) a oddělovač `/` cesty se musí shodovat s textem v adrese URL. Porovnávání textu nerozlišuje malá a velká písmena a je založena na dekódované reprezentaci cesty url. Chcete-li porovnat oddělovač parametrů`{` `}`oddělovač parametrů doslovné trasy`{{` `}}`( nebo ), unikne oddělovači opakováním znaku ( nebo ).

Další důležité informace mají vzory adres URL, které se pokoušejí zachytit název souboru s volitelnou příponou souboru. Zvažte například `files/{filename}.{ext?}`šablonu . Pokud jsou `filename` hodnoty `ext` pro oba a existují, jsou vyplněny obě hodnoty. Pokud v adrese `filename` URL existuje pouze hodnota pro, trasa`.`se shoduje, protože koncové období ( ) je volitelné. Následující adresy URL odpovídají této trase:

* `/files/myFile.txt`
* `/files/myFile`

Hvězdičku (`*`) můžete použít jako předponu parametru trasy k vytvoření vazby na zbytek identifikátoru URI. Tento parametr se nazývá *catch-all.* Například `blog/{*slug}` odpovídá všem identifikátorům `/blog` URI, který začíná a má `slug` za ním libovolnou hodnotu, která je přiřazena hodnotě postupu. Catch-all parametry mohou také odpovídat prázdný řetězec.

Parametr catch-all unikne příslušným znakům, když je trasa použita`/`ke generování adresy URL, včetně znaků oddělovače cest ( ). Například trasa `foo/{*path}` s `{ path = "my/path" }` hodnotami `foo/my%2Fpath`trasy generuje . Všimněte si uvozené lomítko.

Parametry postupu mohou mít *výchozí hodnoty* určené zadáním výchozí hodnoty za názvem`=`parametru odděleným znaménkem rovná se ( ). Definuje například `{controller=Home}` `Home` jako výchozí hodnotu pro `controller`. Výchozí hodnota se používá, pokud v adrese URL parametru není žádná hodnota. Parametry trasy jsou volitelné připojením otazníku (`?`) na konec `id?`názvu parametru, jako v . Rozdíl mezi volitelnými hodnotami a výchozími parametry postupu spoá,&mdash;že parametr trasy s výchozí hodnotou vždy vytvoří hodnotu, volitelný parametr má hodnotu pouze v případě, že hodnota je poskytnuta adresou URL požadavku.

Parametry trasy mohou mít omezení, která musí odpovídat hodnotě trasy vázané z adresy URL. Přidání dvojtečky (`:`) a názvu omezení za název parametru trasy určuje *vsazené omezení* parametru trasy. Pokud omezení vyžaduje argumenty, jsou uzavřeny v závorcích (`(...)`) za názvem omezení. Vícevřádkových omezení lze zadat připojením`:`jinédvojtečky ( ) a názvu omezení.

Název omezení a argumenty jsou <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> předány službě <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> k vytvoření instance pro použití při zpracování adres URL. Například šablona `blog/{article:minlength(10)}` trasy určuje `minlength` omezení s `10`argumentem . Další informace o omezeních postupu a seznam omezení poskytovaných rámcem naleznete v referenční části [omezení trasy.](#route-constraint-reference)

Následující tabulka ukazuje ukázkové šablony tras a jejich chování.

| Šablona postupu                           | Příklad odpovídajícího identifikátoru URI    | Identifikátor URI požadavku&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Odpovídá pouze jedné `/hello`cestě .                                     |
| `{Page=Home}`                            | `/`                     | Odpovídá a `Page` `Home`nastavuje na .                                         |
| `{Page=Home}`                            | `/Contact`              | Odpovídá a `Page` `Contact`nastavuje na .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mapuje `Products` na `List` ovladač a akci.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapy k `Products` ovladači `Details` `id` a akci (nastaveno na 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapy na `Home` řadič `Index` a`id` metodu (je ignorována).        |

Použití šablony je obecně nejjednodušší přístup k směrování. Omezení a výchozí hodnoty lze zadat také mimo šablonu trasy.

> [!TIP]
> Povolte [protokolování,](xref:fundamentals/logging/index) abyste viděli, jak integrované <xref:Microsoft.AspNetCore.Routing.Route>implementace směrování, například , shodu jsou požadavky na shodu.

## <a name="route-constraint-reference"></a>Odkaz na omezení postupu

Omezení trasy se spustí, když došlo ke shodě s příchozí adresou URL a cesta url je tokenizována na hodnoty trasy. Omezení postupu obvykle kontrolují hodnotu postupu přidruženou prostřednictvím šablony postupu a rozhodují o tom, zda je hodnota přijatelná. Některá omezení postupu používají data mimo hodnotu postupu k posouzení, zda lze požadavek směrovat. Například <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> může přijmout nebo odmítnout požadavek na základě jeho slovesa HTTP. Omezení se používají v požadavcích na směrování a generování propojení.

> [!WARNING]
> Nepoužívejte omezení pro **ověření vstupu**. Pokud omezení se používají pro **ověření vstupu**, neplatné vstupní výsledky v *404 - nebyl nalezen* odpověď namísto *400 - chybný požadavek* s příslušnou chybovou zprávu. Omezení trasy se používají k **rozdvojení** podobných tras, nikoli k ověření vstupů pro určitou trasu.

Následující tabulka ukazuje ukázková omezení trasy a jejich očekávané chování.

| omezení | Příklad | Příklady shod | Poznámky |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Shoduje se s libovolným celéčíslo |
| `bool` | `{active:bool}` | `true`, `FALSE` | `true` Shody `false` nebo (malá a velká písmena) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platné `DateTime` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platné `decimal` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platné `double` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platné `float` hodnotě v invariantní jazykové verzi. Viz předchozí upozornění.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Odpovídá platné `Guid` hodnotě. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Odpovídá platné `long` hodnotě. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Řetězec musí mít alespoň 4 znaky. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Řetězec nesmí být větší než 8 znaků. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Řetězec musí mít přesně 12 znaků. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Řetězec musí mít alespoň 8 a nesmí mít přes 16 znaků. |
| `min(value)` | `{age:min(18)}` | `19` | Hodnota celého čísla musí být alespoň 18 |
| `max(value)` | `{age:max(120)}` | `91` | Celá hodnota nesmí být větší než 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Hodnota celého čísla musí být alespoň 18, ale ne větší než 120 |
| `alpha` | `{name:alpha}` | `Rick` | Řetězec se musí skládat z`a`-`z`jednoho nebo více abecedních znaků ( , bez rozlišování velkých a malých písmen) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Řetězec musí odpovídat regulárnímu výrazu (viz tipy k definování regulárního výrazu) |
| `required` | `{name:required}` | `Rick` | Slouží k vynucení, že hodnota neparametrje přítomna během generování adresy URL. |

Na jeden parametr lze použít více omezení oddělených dvojtečkou. Například následující omezení omezuje parametr na celou hodnotu 1 nebo vyšší:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Omezení trasy, která ověřují adresu URL a jsou `int` `DateTime`převedena na typ CLR (například nebo ) vždy používají invariantní jazykovou verzi. Tato omezení předpokládají, že adresu URL je nelokalizovatelné. Omezení trasy poskytovaná rámcem nemění hodnoty uložené v hodnotách trasy. Všechny hodnoty trasy analyzované z adresy URL jsou uloženy jako řetězce. Například `float` omezení se pokusí převést hodnotu trasy na float, ale převedená hodnota se používá pouze k ověření, že může být převedena na float.

## <a name="regular-expressions"></a>Regulární výrazy

Rozhraní ASP.NET Core `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` přidává k konstruktoru regulárního výrazu. Viz <xref:System.Text.RegularExpressions.RegexOptions> popis těchto členů.

Regulární výrazy používají oddělovače a tokeny podobné těm, které používá Směrování a jazyk C#. Tokeny regulárního výrazu musí být uvozeny. Chcete-li použít `^\d{3}-\d{2}-\d{4}$` regulární výraz v `\` směrování, musí mít výraz znaky `\\` (jedno zpětné lomítko) uvedené v řetězci `\` jako (dvojité zpětné lomítko) znaky ve zdrojovém souboru C#, aby se vyhnuly řídicímu znaku řetězce (pokud nepoužijete [doslovné literály řetězce).](/dotnet/csharp/language-reference/keywords/string) Chcete-li uniknout znaky oddělovače`{`parametrů směrování ( , `}` `}`, `[[` `[` `]`), zdvojnásobte znaky ve výrazu (`{{`, , ). `]]` V následující tabulce je uveden regulární výraz a uvozená verze.

| Regulární výraz    | Uvozené regulární výraz     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy používané při směrování`^`často začínají znakem stříšky ( ) a shodují se s počáteční pozicí řetězce. Výrazy často končí znakem`$`dolaru ( ) a shodují se na konci řetězce. Znaky `^` `$` a zajišťují, že regulární výraz odpovídá celé hodnotě parametru trasy. Bez `^` znaků `$` a regulární výraz odpovídá libovolnému podřetězci v řetězci, což je často nežádoucí. V následující tabulce jsou uvedeny příklady a vysvětleny, proč se shodují nebo neshodují.

| Expression   | Řetězec    | Shoda | Poznámka               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | Mz        | Ano   | Odpovídá výrazu    |
| `[a-z]{2}`   | MZ        | Ano   | Neuznačují malá a velká písmen    |
| `^[a-z]{2}$` | hello     | Ne    | Viz `^` `$` a výše |
| `^[a-z]{2}$` | 123abc456 | Ne    | Viz `^` `$` a výše |

Další informace o syntaxi regulárních výrazů naleznete [v tématu .NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr na známou sadu možných hodnot, použijte regulární výraz. Například `{action:regex(^(list|get|create)$)}` pouze odpovídá `action` hodnotě `list` `get`trasy `create`do , , nebo . Pokud je předán do slovníku `^(list|get|create)$` omezení, řetězec je ekvivalentní. Omezení, která jsou předána ve slovníku omezení (není vnímák v rámci šablony), které neodpovídají jedné ze známých omezení, jsou také považovány za regulární výrazy.

## <a name="custom-route-constraints"></a>Vlastní omezení postupu

Kromě předdefinovaných omezení trasy lze implementací <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozhraní vytvořit omezení vlastní trasy. Rozhraní <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> obsahuje jednu `Match`metodu `true` , která vrátí, `false` pokud je splněna omezení a jinak.

Chcete-li <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>použít vlastní , musí být typ omezení <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> postupu registrován u aplikace v kontejneru služeb aplikace. A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> je slovník, který mapuje <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> klíče omezení trasy na implementace, které tato omezení ověřují. Aplikace <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> lze aktualizovat buď `Startup.ConfigureServices` jako součást [služby. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) volání nebo <xref:Microsoft.AspNetCore.Routing.RouteOptions> konfigurací `services.Configure<RouteOptions>`přímo s . Příklad:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Omezení pak lze použít na trasy obvyklým způsobem pomocí názvu určeného při registraci typu omezení. Příklad:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>Odkaz na generování adresy URL

Následující příklad ukazuje, jak vygenerovat propojení s trasou danou <xref:Microsoft.AspNetCore.Routing.RouteCollection>slovníkem hodnot trasy a .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Generované <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> na konci předchozího vzorku je `/package/create/123`. Slovník poskytuje hodnoty `operation` trasy `id` a v šabloně "Track `package/{operation}/{id}`Package Route" . Podrobnosti naleznete v ukázkovém kódu v části [Use Routing Middleware](#use-routing-middleware) nebo v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Druhý parametr konstruktoru <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> je kolekce *okolních hodnot*. Okolní hodnoty jsou vhodné použít, protože omezují počet hodnot, které musí vývojář zadat v kontextu požadavku. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení. V ASP.NET `About` akce aplikace Core MVC `HomeController`aplikace , není nutné zadat hodnotu trasy `Index` kontroléru pro propojení s akcí,&mdash;která `Home` se používá okolní hodnota.

Hodnoty okolí, které neodpovídají parametru, jsou ignorovány. Okolní hodnoty jsou také ignorovány, když explicitně zadaný hodnota přepíše hodnotu okolí. Párování probíhá zleva doprava v adrese URL.

Hodnoty explicitně zadaných, ale které neodpovídají segmentu trasy, jsou přidány do řetězce dotazu. V následující tabulce je uveden výsledek `{controller}/{action}/{id?}`při použití šablony trasy .

| Okolní hodnoty                     | Explicitní hodnoty                        | Výsledek                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| regulátor = "Domů"                | akce = "O"                       | `/Home/About`           |
| regulátor = "Domů"                | controller = "Objednávka", akce = "O" | `/Order/About`          |
| regulátor = "Domů", barva = "Červená" | akce = "O"                       | `/Home/About`           |
| regulátor = "Domů"                | akce = "O", barva = "Červená"        | `/Home/About?color=Red` |

Pokud má trasa výchozí hodnotu, která neodpovídá parametru, a tato hodnota je explicitně poskytnuta, musí odpovídat výchozí hodnotě:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Generování propojení generuje propojení pouze pro tuto trasu, pokud jsou k dispozici odpovídající hodnoty pro `controller` a `action` jsou k dispozici.

## <a name="complex-segments"></a>Složité segmenty

Složité segmenty (například) `[Route("/x{token}y")]`jsou zpracovány porovnáním literály zprava doleva nechamtivým způsobem. Podrobné vysvětlení, jak jsou složité segmenty spárovány, naleznete v [tomto kódu.](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) [Ukázka kódu](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) není používán ASP.NET Core, ale poskytuje dobré vysvětlení složitých segmentů.

::: moniker-end
