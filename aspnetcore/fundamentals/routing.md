---
title: Směrování v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak ASP.NET Core směrování zodpovídá za mapování identifikátorů URI požadavků na selektory koncových bodů a odesílání příchozích požadavků do koncových bodů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/24/2019
uid: fundamentals/routing
ms.openlocfilehash: 14fe4bf9266ec6b2d588fa26d7a460531e33aa30
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71295495"
---
# <a name="routing-in-aspnet-core"></a>Směrování v ASP.NET Core

Pomocí nástroje [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/)a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Směrování zodpovídá za mapování identifikátorů URI požadavků na koncové body a odesílání příchozích požadavků do těchto koncových bodů. Trasy jsou v aplikaci definované a nakonfigurované při spuštění aplikace. Trasa může volitelně extrahovat hodnoty z adresy URL obsažené v žádosti a tyto hodnoty pak lze použít pro zpracování požadavků. Směrování pomocí informací o trasách z aplikace taky umožňuje generovat adresy URL, které se mapují na koncové body.

> [!IMPORTANT]
> Tento dokument popisuje směrování ASP.NET Core nízké úrovně. Informace o ASP.NET Core směrování MVC najdete v tématu <xref:mvc/controllers/routing>. Informace o konvencích směrování v Razor Pages najdete v <xref:razor-pages/razor-pages-conventions>tématu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Základy směrování

Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné. Výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}`:

* Podporuje základní a popisné schéma směrování.
* Je užitečným výchozím bodem pro aplikace založené na uživatelském rozhraní.

Vývojáři obvykle přidávají další stručný trasy do oblastí s vysokým provozem v aplikaci ve zvláštních situacích (například koncové body blogu a elektronického obchodování) pomocí [Směrování atributů](xref:mvc/controllers/routing#attribute-routing) nebo vyhrazených konvenčních tras.

Webové rozhraní API by mělo používat směrování atributů k modelování funkcí aplikace jako sady prostředků, ve kterých jsou operace reprezentované příkazy HTTP. To znamená, že mnoho operací (například GET, POST) na stejném logickém prostředku bude používat stejnou adresu URL. Směrování atributů poskytuje úroveň řízení, která je nutná k pečlivému návrhu rozložení veřejného koncového bodu rozhraní API.

Aplikace Razor Pages používají výchozí konvenční směrování pro obsluhu pojmenovaných prostředků ve složce *Pages* v aplikaci. K dispozici jsou další konvence, které vám umožní přizpůsobit Razor Pages chování směrování. Další informace naleznete v tématu <xref:razor-pages/index> a <xref:razor-pages/razor-pages-conventions>.

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
> Propojení koncových bodů je omezené na akce a stránky MVC/Razor Pages. Pro budoucí verze jsou plánovány rozšíření funkcí pro propojení koncových bodů.

Směrování je k kanálu <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> [middleware](xref:fundamentals/middleware/index) připojeno třídou. [ASP.NET Core MVC](xref:mvc/overview) v rámci své konfigurace přidává směrování do kanálu middlewaru a zpracovává směrování v MVC a Razor Pages aplikacích. Informace o tom, jak používat směrování jako samostatnou součást, najdete v části [použití middlewaru pro směrování](#use-routing-middleware) .

### <a name="url-matching"></a>Shoda adresy URL

Shoda adresy URL je proces, podle kterého směrování odešle příchozí požadavek na *koncový bod*. Tento proces je založený na datech v cestě URL, ale dá se rozšířit, aby v žádosti mohla být považovat všechna data. Schopnost odesílat žádosti na samostatné obslužné rutiny je klíč pro škálování velikosti a složitosti aplikace.

Když middleware směrování spustí, nastaví koncový bod (`Endpoint`) a hodnoty směrování na funkci <xref:Microsoft.AspNetCore.Http.HttpContext>na. Pro aktuální požadavek:

* Volání `HttpContext.GetEndpoint` získá koncový bod.
* `HttpRequest.RouteValues`Získá kolekci hodnot tras.

Middleware spuštěný poté, co middleware směrování uvidí koncový bod a provede akci. Middleware autorizace může například dotazování kolekci metadat koncového bodu pro zásadu autorizace. Po spuštění všech middlewarů v kanálu zpracování požadavků je vyvolán delegát vybraného koncového bodu.

Systém směrování v rámci směrování koncových bodů zodpovídá za všechna rozhodnutí o odesílání. Vzhledem k tomu, že middleware používá zásady na základě vybraného koncového bodu, je důležité, aby jakékoli rozhodnutí, které může ovlivnit odesílání nebo použití zásad zabezpečení, bylo provedeno v rámci systému směrování.

Po spuštění delegáta koncového bodu jsou vlastnosti [RouteContext. parametr RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) nastaveny na odpovídající hodnoty na základě dosud provedeného zpracování požadavků.

[Parametr RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) je slovník *hodnot tras* vytvořených z trasy. Tyto hodnoty se obvykle určují pomocí tokenizací adresy URL a dají se použít k přijetí vstupu uživatele nebo k dalšímu odesílání rozhodnutí v rámci aplikace.

[Parametr RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) je kontejner objektů a dat pro další data související s odpovídající trasou. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>jsou k dispozici pro podporu přidružování dat o stavu k jednotlivým cestám, aby aplikace mohla učinit rozhodnutí na základě toho, na které trase odpovídá. Tyto hodnoty jsou definované vývojářem a **neovlivňují chování** směrování jakýmkoli způsobem. Kromě toho hodnoty dočasně ukládané v [parametr RouteData. Datatokeny](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) můžou být libovolného typu, na rozdíl od [parametr RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), které musí být převoditelné na a z řetězců.

[Parametr RouteData. routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) je seznam tras, které byly součástí úspěšného porovnání požadavku. Trasy mohou být vnořeny do sebe navzájem. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> Vlastnost odráží cestu v logickém stromu tras, jejichž výsledkem byla shoda. Obecně platí, že první položka <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> v nástroji je kolekce tras a měla by se používat pro generování adresy URL. Poslední položka v <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> je obslužná rutina trasy, která se shoduje.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>Generování adresy URL pomocí LinkGenerator

Generování adresy URL je proces, podle kterého směrování může vytvořit cestu adresy URL na základě sady hodnot tras. To umožňuje logické oddělení mezi vašimi koncovými body a adresami URL, které k nim mají přístup.

Směrování koncového bodu zahrnuje rozhraní API generátoru odkazů (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator>je služba typu Singleton, kterou lze načíst z DI. Rozhraní API lze použít mimo kontext vykonávajícího požadavku. MVC a scénáře, které spoléhají na <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, jako jsou [například pomocníky značek](xref:mvc/views/tag-helpers/intro), HTML helps a [výsledky akcí](xref:mvc/controllers/actions), používají generátor propojení k poskytování možností vytváření odkazů. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>

Generátor propojení se zálohuje konceptem *adres* a *schémat adres*. Schéma adres je způsob, jak určit koncové body, které by měly být považovány za vytváření odkazů. Například název trasy a hodnoty tras vycházejí z MVC/Razor Pages jsou implementovány jako schéma adres.

Generátor propojení může propojit s akcemi MVC/Razor Pages a stránkami prostřednictvím následujících rozšiřujících metod:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Přetížení těchto metod akceptuje argumenty, které zahrnují `HttpContext`. Tyto metody jsou funkčně ekvivalentní `Url.Action` a `Url.Page` ale nabízejí další flexibilitu a možnosti.

Metody jsou nejčastěji `Url.Action` podobné a `Url.Page` v tom, že generují identifikátor URI obsahující absolutní cestu. `GetPath*` `GetUri*` Metody vždy generují absolutní identifikátor URI obsahující schéma a hostitele. Metody, které přijímají `HttpContext` identifikátor URI v kontextu zpracovávaného požadavku. Použijí se hodnoty tras, základní cesta, schéma a hostitel z zpracovávaného požadavku, pokud nejsou přepsány.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>je volána s adresou. K vygenerování identifikátoru URI dochází ve dvou krocích:

1. Adresa je svázána se seznamem koncových bodů, které odpovídají dané adrese.
1. Každý koncový bod `RoutePattern` je vyhodnocen, dokud se nenajde vzor směrování, který odpovídá zadaným hodnotám. Výsledný výstup je v kombinaci s ostatními částmi identifikátoru URI dodanými generátorem odkazů a vrácenými.

Metody poskytované <xref:Microsoft.AspNetCore.Routing.LinkGenerator> funkcí support standard pro vytváření odkazů pro jakýkoli typ adresy. Nejpohodlnější způsob použití generátoru odkazů je prostřednictvím metod rozšíření, které provádějí operace pro konkrétní typ adresy.

| Metoda rozšíření | Popis |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Vygeneruje identifikátor URI s absolutní cestou na základě zadaných hodnot. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Vygeneruje absolutní identifikátor URI na základě zadaných hodnot.             |

> [!WARNING]
> Věnujte pozornost následujícím důsledkům volání <xref:Microsoft.AspNetCore.Routing.LinkGenerator> metod:
>
> * Používejte `GetUri*` rozšiřující metody s opatrností v konfiguraci aplikace, která `Host` neověřuje hlavičku příchozích požadavků. `Host` Pokud záhlaví příchozích požadavků není ověřeno, lze nedůvěryhodný vstup žádosti poslat zpátky klientovi v identifikátorech URI na stránce zobrazení nebo stránky. Doporučujeme, aby všechny produkční aplikace nakonfigurovali server, aby ověřili `Host` hlavičku se známými platnými hodnotami.
>
> * Používejte <xref:Microsoft.AspNetCore.Routing.LinkGenerator> s opatrností v middleware v `Map` kombinaci `MapWhen`s nebo. `Map*`změní základní cestu spouštěné žádosti, která má vliv na výstup vytváření odkazů. <xref:Microsoft.AspNetCore.Routing.LinkGenerator> Všechna rozhraní API umožňují zadat základní cestu. Vždy zadat prázdnou základní cestu, která `Map*`bude mít vliv na generování odkazů.

## <a name="differences-from-earlier-versions-of-routing"></a>Rozdíly oproti starším verzím směrování

Mezi směrováním koncových bodů a verzemi směrování starších než v ASP.NET Core 2,2 existuje několik rozdílů:

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

  Pomocí <xref:Microsoft.AspNetCore.Routing.IRouter>směrování založeného na tomto kódu generuje `/blog/ReadPost/17`identifikátor URI, který respektuje velikost zadané hodnoty trasy. Směrování koncových bodů v ASP.NET Core 2,2 nebo `/Blog/ReadPost/17` novějším má za následek to, že je "blog" na velká písmena. Směrování koncového bodu `IOutboundParameterTransformer` poskytuje rozhraní, které se dá použít k globálnímu přizpůsobení tohoto chování, nebo k aplikování různých konvencí pro mapování adres URL.

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

  V `IRouter`rámci směrování založeného na službě je výsledek `/Blog/ReadPost/17`vždy, i když `BlogController` neexistuje nebo `ReadPost` nemá metodu Action. Podle očekávání, směrování koncového bodu v ASP.NET Core 2,2 nebo `/Blog/ReadPost/17` vyšší vytvoří, pokud metoda Action existuje. *Směrování koncových bodů ale vytvoří prázdný řetězec, pokud akce neexistuje.* V koncepčním případě směrování koncového bodu nepředpokládá, že koncový bod existuje, pokud akce neexistuje.

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

  Pokud je `/Store/Product/18` identifikátor URI v ASP.NET Core 2,1 nebo starším, odkaz vygenerovaný na `@Url.Page("/Login")` stránce úložiště/informace je `/Login/18`. `id` Hodnota 18 se znovu použije, i když je cíl propojení jinou součástí aplikace. Hodnota trasy v kontextu `/Login` stránky je pravděpodobně hodnota ID uživatele, nikoli hodnota ID produktu úložiště. `id`

  V rámci směrování koncového bodu s ASP.NET Core 2,2 nebo novějším `/Login`je výsledkem. Okolní hodnoty se znovu nepoužijí, pokud je cíl propojení jinou akcí nebo stránkou.

* Syntaxe parametru trasy s kulatým Trip: Lomítka nejsou kódována při použití syntaxe parametrů Double-hvězdička (`**`) catch-ALL.

  Během generování propojení systém směrování zakóduje hodnotu zachycenou parametrem zachycení dvojité hvězdičky (`**`) ( `{**myparametername}`například) s výjimkou lomítka. Směrování s dvojitou hvězdičkou (catch-All `IRouter`) je podporováno v rámci směrování založeného na ASP.NET Core 2,2 nebo novějším.

  V předchozích verzích ASP.NET Core () se podporuje jednoduchá hvězdička All – všechny`{*myparametername}`syntaxe parametrů () a lomítka jsou kódovaná.

  | Cestě              | Odkaz vygeneroval s<br>`Url.Action(new { category = "admin/products" })`&hellip; |
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

### <a name="create-routes"></a>Vytváření tras

Většina aplikací vytváří trasy voláním <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> nebo jedné z podobných metod rozšíření definovaných v. <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> Kterákoli z <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> rozšiřujících metod vytvoří <xref:Microsoft.AspNetCore.Routing.Route> instanci a přidá ji do kolekce tras.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nepřijímá parametr obslužné rutiny trasy. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>přidá pouze trasy, které jsou zpracovávány <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Další informace o směrování v MVC najdete v tématu <xref:mvc/controllers/routing>.

Následující příklad kódu je příkladem <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> volání využívaného typickou ASP.NET Core definice trasy MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Tato šablona odpovídá cestě URL a extrahuje hodnoty tras. Například cesta `/Products/Details/17` generuje následující hodnoty trasy: `{ controller = Products, action = Details, id = 17 }`.

Hodnoty tras se určují rozdělením cesty URL na segmenty a porovnáním jednotlivých segmentů s názvem *parametru trasy* v šabloně směrování. Parametry směrování jsou pojmenovány. Parametry definované ohraničujícím název parametru ve složených závorkách `{ ... }`.

Předchozí šablona může také odpovídat cestě `/` URL a vydávat hodnoty. `{ controller = Home, action = Index }` K tomu dochází, `{controller}` protože `{action}` parametry směrování a `id` mají výchozí hodnoty a parametr trasy je nepovinný. Znak rovná se (`=`) následovaný hodnotou po názvu parametru trasy, který definuje výchozí hodnotu parametru. Otazník (`?`) po názvu parametru trasy definuje volitelný parametr.

Parametry směrování s výchozí hodnotou *vždy* vytvoří hodnotu trasy, když odpovídá trasa. Volitelné parametry neposkytují hodnotu trasy, pokud nebyl nalezen žádný odpovídající segment cesty URL. Podrobný popis scénářů a syntaxe šablon směrování najdete v části referenční dokumentace k [šabloně směrování](#route-template-reference) .

V následujícím příkladu definuje definice `{id:int}` parametru trasy [](#route-constraint-reference) `id` omezení trasy pro parametr trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Tato šablona odpovídá cestě URL, například `/Products/Details/17` ne. `/Products/Details/Apples` Omezení tras implementují <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a kontrolují hodnoty směrování a ověřují je. V tomto příkladu musí být hodnota `id` trasy převoditelná na celé číslo. Vysvětlení omezení trasy poskytovaných rozhraním naleznete v tématu [Route-Constraint-reference](#route-constraint-reference) .

Další přetížení <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> přijímají hodnoty pro `constraints`, `dataTokens`a `defaults`. Typické použití těchto parametrů je předání anonymního typu objektu, kde názvy vlastností anonymního typu odpovídají názvům parametrů tras.

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

Předchozí šablona odpovídá cestě URL jako `/Blog/All-About-Routing/Introduction` a extrahuje hodnoty. `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` Výchozí hodnoty tras pro `controller` a `action` jsou vytvářeny trasou, i když v šabloně nejsou odpovídající parametry směrování. V šabloně směrování lze zadat výchozí hodnoty. Parametr trasy je definován jako *catch-All* pomocí vzhledu dvojité hvězdičky (`**`) před názvem parametru trasy. `article` Catch – všechny parametry tras zaznamenávají zbytek cesty URL a můžou taky odpovídat prázdnému řetězci.

Následující příklad přidá omezení trasy a datové tokeny:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Předchozí šablona odpovídá cestě URL jako `/en-US/Products/5` a extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` a datové tokeny `{ locale = en-US }`.

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

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

V `Startup.Configure` metodě musí být nakonfigurovány trasy. Ukázková aplikace používá následující rozhraní API:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Odpovídá pouze požadavkům HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

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

Tokeny ve složených závorkách (`{ ... }`) definují *parametry trasy* , které jsou svázané, pokud je trasa shodná. V segmentu směrování můžete definovat více než jeden parametr trasy, ale musí být oddělený literálovou hodnotou. Například `{controller=Home}{action=Index}` není platná trasa, protože hodnota literálu není mezi `{controller}` a `{action}`. Tyto parametry tras musí mít název a můžou mít zadané další atributy.

Textový literál jiný než parametry směrování (například `{id}`) a oddělovač `/` cesty musí odpovídat textu v adrese URL. U porovnávání textu se nerozlišují malá a velká písmena a na základě dekódovat reprezentace cesty URL. Chcete-li spárovat oddělovač parametrů trasy`{` (nebo `}`) literálu, zařídíte oddělovač opakováním znaku (`{{` nebo `}}`).

Vzory adres URL, které se pokoušejí zachytit název souboru s volitelnou příponou souboru, mají další požadavky. Představte si třeba šablonu `files/{filename}.{ext?}`. Pokud hodnoty pro obojí `filename` i `ext` existují, naplní se obě hodnoty. Je-li v adrese `filename` URL pouze hodnota, která je v adrese URL, odpovídá trasa, protože`.`koncová tečka () je volitelná. Tuto trasu odpovídají následujícím adresám URL:

* `/files/myFile.txt`
* `/files/myFile`

K vytvoření vazby na zbytek identifikátoru URI můžete použít hvězdičku (`*`) nebo dvojitou hvězdičku (`**`) jako předponu parametru trasy. Tyto parametry se nazývají *catch-All* . Například `blog/{**slug}` odpovídá jakémukoli identifikátoru URI, který `/blog` začíná a má libovolnou hodnotu, `slug` která je za ní přiřazena hodnota trasy. Catch – všechny parametry můžou odpovídat také prázdnému řetězci.

Parametr catch-All řídí příslušné znaky, pokud je použita cesta pro vygenerování adresy URL, včetně znaků oddělovače (`/`). Například trasa `foo/{*path}` s hodnotami `{ path = "my/path" }` trasy vygeneruje `foo/my%2Fpath`. Všimněte si řídicího znaku lomítka. Do oddělovacích znaků cesty pro přenos cest použijte `**` předponu parametru Route. Trasa `foo/{**path}` s `{ path = "my/path" }` vygeneruje `foo/my/path`.

Parametry směrování můžou mít *výchozí hodnoty* určené zadáním výchozí hodnoty za názvem parametru odděleným symbolem rovná se (`=`). Například `{controller=Home}` `controller`definuje `Home` jako výchozí hodnotu pro. Výchozí hodnota se použije v případě, že v adrese URL parametru není k dispozici žádná hodnota. Parametry směrování jsou povinny připojením otazníku (`?`) na konec názvu parametru, jako v. `id?` Rozdíl mezi volitelnými hodnotami a výchozími parametry směrování je, že parametr trasy s výchozí hodnotou vždy vytvoří hodnotu&mdash;, která má volitelný parametr hodnotu, pouze pokud je hodnota poskytnuta adresou URL požadavku.

Parametry směrování můžou mít omezení, která se musí shodovat s hodnotou trasy svázanou z adresy URL. Přidání dvojtečky (`:`) a názvu omezení za názvem parametru trasy Určuje *vložené omezení* pro parametr trasy. Pokud omezení vyžaduje argumenty, jsou uzavřeny v závorkách (`(...)`) za názvem omezení. Přidáním dalších dvojtečk (`:`) a názvu omezení lze zadat více vložených omezení.

Název omezení a argumenty jsou předány <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> službě za účelem vytvoření <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> instance pro použití při zpracování adresy URL. Například šablona `blog/{article:minlength(10)}` trasy `minlength` určuje omezení s argumentem `10`. Další informace o omezeních tras a seznam omezení poskytovaných rozhraním najdete v části [referenční informace k omezením trasy](#route-constraint-reference) .

Parametry směrování můžou mít také transformaci parametrů, které transformují hodnotu parametru při generování odkazů a porovnání akcí a stránek s adresami URL. Podobně jako omezení můžou být transformátory parametrů přidány do parametru trasy, a to přidáním dvojtečky`:`() a názvu transformátoru za názvem parametru trasy. Například šablona `blog/{article:slugify}` trasy `slugify` určuje transformátor. Další informace o transformačních parametrech naleznete v části [Referenční příručka pro parametry](#parameter-transformer-reference) transformátoru.

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
| `int` | `{id:int}` | `123456789`, `-123456789` | Odpovídá jakémukoli celému číslu |
| `bool` | `{active:bool}` | `true`, `FALSE` | Odpovídá `true` nebo`false` (nerozlišuje velká a malá písmena) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platné `DateTime` hodnotě (v neutrální jazykové verzi – viz upozornění) |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platné `decimal` hodnotě (v neutrální jazykové verzi – viz upozornění) |
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platné `double` hodnotě (v neutrální jazykové verzi – viz upozornění) |
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platné `float` hodnotě (v neutrální jazykové verzi – viz upozornění) |
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

Regulární výrazy používají oddělovače a tokeny podobné těm, které používá směrování a C# jazyk. Tokeny regulárního výrazu musí být uvozeny řídicími znaky. Chcete-li použít regulární `^\d{3}-\d{2}-\d{4}$` výraz ve směrování, musí `\` mít výraz (jedno zpětné lomítko), které je zadáno v řetězci `\\` jako (dvojité zpětné lomítko) ve C# zdrojovém souboru, aby mohl řídicí znak`\` řídicí znak řetězce (Pokud se nepoužívají [doslovné řetězce literálů](/dotnet/csharp/language-reference/keywords/string)). Chcete-li řídicí znaky oddělovače parametrů směrování`{`( `}`, `[`, `]`,) `}``{{` `[[`, poklikejte na znaky ve výrazu (,,, ).`]]` V následující tabulce je uveden regulární výraz a verze s řídicím znakem.

| Regulární výraz    | Regulární výraz s řídicím znakem     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy používané v směrování často začínají znakem stříšky`^`() a odpovídají počáteční pozici řetězce. Výrazy se často končí znakem dolaru (`$`) a koncem řetězce. Znaky `^` a`$` zajišťují, že regulární výraz odpovídá celé hodnotě parametru Route. Bez znaků `$` a regulární výraz odpovídá jakémukoli podřetězci v rámci řetězce, což je často nežádoucí. `^` Následující tabulka obsahuje příklady a vysvětlení, proč se shodují nebo neshodují.

| Výraz   | String    | Shoda | Komentář               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | Dobrý den     | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | mz        | Ano   | Výraz shody    |
| `[a-z]{2}`   | MZ        | Ano   | Nerozlišuje velká a malá písmena    |
| `^[a-z]{2}$` | Dobrý den     | Ne    | Viz `^` a `$` vyšší |
| `^[a-z]{2}$` | 123abc456 | Ne    | Viz `^` a `$` vyšší |

Další informace o syntaxi regulárního výrazu naleznete v tématu [.NET Framework regulární výrazy](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr na známou sadu možných hodnot, použijte regulární výraz. Například `{action:regex(^(list|get|create)$)}` odpovídápouze`list`hodnotě `get`trasy, nebo`create`. `action` Pokud je předána do slovníku omezení, je `^(list|get|create)$` řetězec ekvivalentní. Omezení, která jsou předána do slovníku omezení (nejsou vložena v rámci šablony), která neodpovídají jednomu ze známých omezení, jsou také považována za regulární výrazy.

## <a name="custom-route-constraints"></a>Vlastní omezení trasy

Kromě předdefinovaných omezení trasy lze vytvořit vlastní omezení trasy implementací <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozhraní. Rozhraní obsahuje jedinou metodu, `Match`která vrací `true` , pokud je omezení splněno, a `false` jinak. <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>

Pokud chcete použít vlastní <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, musí být typ omezení trasy registrovaný <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> v aplikaci v kontejneru služeb aplikace. Je slovník, který mapuje klíče omezení tras na <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementace, které ověřují tato omezení. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> Aplikace <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> se dá v `Startup.ConfigureServices` rámci služeb aktualizovat buď jako součást [služby. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) volání nebo přímou konfigurací <xref:Microsoft.AspNetCore.Routing.RouteOptions> s. `services.Configure<RouteOptions>` Příklad:

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

Například vlastní `slugify` parametr Transformer ve vzoru `blog\{article:slugify}` směrování s `Url.Action(new { article = "MyTestArticle" })` vygenerováním `blog\my-test-article`.

Chcete-li použít transformující parametr ve schématu směrování, nakonfigurujte jej nejprve pomocí <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>: `Startup.ConfigureServices`

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Transformátory parametrů používá rozhraní k transformaci identifikátoru URI, kde se Endpoint vyřeší. ASP.NET Core MVC například používá transformaci parametrů k `area`transformaci hodnoty trasy používané k porovnávání, `controller`, `action`a `page`.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

S předchozí trasou se akce `SubscriptionManagementController.GetAll()` shodují s identifikátorem URI. `/subscription-management/get-all` Transformující parametr nemění hodnoty trasy použité k vygenerování odkazu. Například `Url.Action("GetAll", "SubscriptionManagement")` výstupy `/subscription-management/get-all`.

ASP.NET Core poskytuje konvence rozhraní API pro použití parametrů Transformers s vygenerovanými trasami:

* ASP.NET Core MVC má `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` konvenci rozhraní API. Tato konvence aplikuje na všechny trasy atributů v aplikaci zadaného parametru Transformer. Parametr Transformer transformuje tokeny, když jsou nahrazeny. Další informace najdete v tématu [Použití transformátoru parametrů k přizpůsobení náhrady tokenu](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages má `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` konvence rozhraní API. Tato konvence u všech automaticky zjištěných Razor Pages aplikuje zadaný transformátor parametrů. Parametr Transformer přetransformuje segmenty složky a názvu souboru na trasy Razor Pages. Další informace najdete v tématu [Použití transformátoru parametrů k přizpůsobení cest stránky](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Odkaz na generování adresy URL

Následující příklad ukazuje, jak vygenerovat odkaz na trasu s ohledem na slovník hodnot směrování a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Vygenerovalo se na konci předchozí `/package/create/123`ukázky. <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> Slovník poskytuje `operation` hodnoty a `id` trasu pro šablonu sledování trasy `package/{operation}/{id}`balíčku. Podrobnosti najdete v ukázkovém kódu v části [použití middleware pro směrování](#use-routing-middleware) nebo v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Druhý parametr <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> konstruktoru je kolekce *okolních hodnot*. Okolní hodnoty jsou vhodné k použití, protože omezují počet hodnot, které vývojář musí určit v rámci kontextu požadavku. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. `About` V akci `Home` `Index` aplikace ASP.NET Core MVC není nutné zadávat hodnotu trasy kontroléru&mdash; ,kterábudepropojenasakcí,kterájepoužitaokolníhodnotou.`HomeController`

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
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Směrování zodpovídá za mapování identifikátorů URI požadavků na koncové body a odesílání příchozích požadavků do těchto koncových bodů. Trasy jsou v aplikaci definované a nakonfigurované při spuštění aplikace. Trasa může volitelně extrahovat hodnoty z adresy URL obsažené v žádosti a tyto hodnoty pak lze použít pro zpracování požadavků. Směrování pomocí informací o trasách z aplikace taky umožňuje generovat adresy URL, které se mapují na koncové body.

Pokud chcete použít nejnovější scénáře směrování v ASP.NET Core 2,2, zadejte [verzi kompatibility](xref:mvc/compatibility-version) pro registraci služby MVC v `Startup.ConfigureServices`těchto umístěních:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Možnost určuje, jestli má směrování interně používat logiku založenou na koncovém bodu <xref:Microsoft.AspNetCore.Routing.IRouter>nebo logiku na základě ASP.NET Core 2,1 nebo starší verze. <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> Pokud je verze kompatibility nastavená na 2,2 nebo novější, výchozí hodnota je `true`. Nastavte hodnotu `false` na použít předchozí logiku směrování:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Další informace o <xref:Microsoft.AspNetCore.Routing.IRouter>směrování na základě služby najdete v [tomto tématu ve verzi ASP.NET Core 2,1](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

> [!IMPORTANT]
> Tento dokument popisuje směrování ASP.NET Core nízké úrovně. Informace o ASP.NET Core směrování MVC najdete v tématu <xref:mvc/controllers/routing>. Informace o konvencích směrování v Razor Pages najdete v <xref:razor-pages/razor-pages-conventions>tématu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Základy směrování

Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné. Výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}`:

* Podporuje základní a popisné schéma směrování.
* Je užitečným výchozím bodem pro aplikace založené na uživatelském rozhraní.

Vývojáři obvykle přidávají další stručný trasy do oblastí s vysokým provozem v aplikaci ve zvláštních situacích (například koncové body blogu a elektronického obchodování) pomocí [Směrování atributů](xref:mvc/controllers/routing#attribute-routing) nebo vyhrazených konvenčních tras.

Webové rozhraní API by mělo používat směrování atributů k modelování funkcí aplikace jako sady prostředků, ve kterých jsou operace reprezentované příkazy HTTP. To znamená, že mnoho operací (například GET, POST) na stejném logickém prostředku bude používat stejnou adresu URL. Směrování atributů poskytuje úroveň řízení, která je nutná k pečlivému návrhu rozložení veřejného koncového bodu rozhraní API.

Aplikace Razor Pages používají výchozí konvenční směrování pro obsluhu pojmenovaných prostředků ve složce *Pages* v aplikaci. K dispozici jsou další konvence, které vám umožní přizpůsobit Razor Pages chování směrování. Další informace naleznete v tématu <xref:razor-pages/index> a <xref:razor-pages/razor-pages-conventions>.

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

Směrování je k kanálu <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> [middleware](xref:fundamentals/middleware/index) připojeno třídou. [ASP.NET Core MVC](xref:mvc/overview) v rámci své konfigurace přidává směrování do kanálu middlewaru a zpracovává směrování v MVC a Razor Pages aplikacích. Informace o tom, jak používat směrování jako samostatnou součást, najdete v části [použití middlewaru pro směrování](#use-routing-middleware) .

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

Směrování koncového bodu zahrnuje rozhraní API generátoru odkazů (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator>je služba typu Singleton, kterou lze načíst z DI. Rozhraní API lze použít mimo kontext vykonávajícího požadavku. MVC a scénáře, které spoléhají na <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, jako jsou [například pomocníky značek](xref:mvc/views/tag-helpers/intro), HTML helps a [výsledky akcí](xref:mvc/controllers/actions), používají generátor propojení k poskytování možností vytváření odkazů. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>

Generátor propojení se zálohuje konceptem *adres* a *schémat adres*. Schéma adres je způsob, jak určit koncové body, které by měly být považovány za vytváření odkazů. Například název trasy a hodnoty tras vycházejí z MVC/Razor Pages jsou implementovány jako schéma adres.

Generátor propojení může propojit s akcemi MVC/Razor Pages a stránkami prostřednictvím následujících rozšiřujících metod:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Přetížení těchto metod akceptuje argumenty, které zahrnují `HttpContext`. Tyto metody jsou funkčně ekvivalentní `Url.Action` a `Url.Page` ale nabízejí další flexibilitu a možnosti.

Metody jsou nejčastěji `Url.Action` podobné a `Url.Page` v tom, že generují identifikátor URI obsahující absolutní cestu. `GetPath*` `GetUri*` Metody vždy generují absolutní identifikátor URI obsahující schéma a hostitele. Metody, které přijímají `HttpContext` identifikátor URI v kontextu zpracovávaného požadavku. Použijí se hodnoty tras, základní cesta, schéma a hostitel z zpracovávaného požadavku, pokud nejsou přepsány.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>je volána s adresou. K vygenerování identifikátoru URI dochází ve dvou krocích:

1. Adresa je svázána se seznamem koncových bodů, které odpovídají dané adrese.
1. Každý koncový bod `RoutePattern` je vyhodnocen, dokud se nenajde vzor směrování, který odpovídá zadaným hodnotám. Výsledný výstup je v kombinaci s ostatními částmi identifikátoru URI dodanými generátorem odkazů a vrácenými.

Metody poskytované <xref:Microsoft.AspNetCore.Routing.LinkGenerator> funkcí support standard pro vytváření odkazů pro jakýkoli typ adresy. Nejpohodlnější způsob použití generátoru odkazů je prostřednictvím metod rozšíření, které provádějí operace pro konkrétní typ adresy.

| Metoda rozšíření   | Popis                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Vygeneruje identifikátor URI s absolutní cestou na základě zadaných hodnot. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Vygeneruje absolutní identifikátor URI na základě zadaných hodnot.             |

> [!WARNING]
> Věnujte pozornost následujícím důsledkům volání <xref:Microsoft.AspNetCore.Routing.LinkGenerator> metod:
>
> * Používejte `GetUri*` rozšiřující metody s opatrností v konfiguraci aplikace, která `Host` neověřuje hlavičku příchozích požadavků. `Host` Pokud záhlaví příchozích požadavků není ověřeno, lze nedůvěryhodný vstup žádosti poslat zpátky klientovi v identifikátorech URI na stránce zobrazení nebo stránky. Doporučujeme, aby všechny produkční aplikace nakonfigurovali server, aby ověřili `Host` hlavičku se známými platnými hodnotami.
>
> * Používejte <xref:Microsoft.AspNetCore.Routing.LinkGenerator> s opatrností v middleware v `Map` kombinaci `MapWhen`s nebo. `Map*`změní základní cestu spouštěné žádosti, která má vliv na výstup vytváření odkazů. <xref:Microsoft.AspNetCore.Routing.LinkGenerator> Všechna rozhraní API umožňují zadat základní cestu. Vždy zadat prázdnou základní cestu, která `Map*`bude mít vliv na generování odkazů.

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

  Pomocí <xref:Microsoft.AspNetCore.Routing.IRouter>směrování založeného na tomto kódu generuje `/blog/ReadPost/17`identifikátor URI, který respektuje velikost zadané hodnoty trasy. Směrování koncových bodů v ASP.NET Core 2,2 nebo `/Blog/ReadPost/17` novějším má za následek to, že je "blog" na velká písmena. Směrování koncového bodu `IOutboundParameterTransformer` poskytuje rozhraní, které se dá použít k globálnímu přizpůsobení tohoto chování, nebo k aplikování různých konvencí pro mapování adres URL.

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

  V `IRouter`rámci směrování založeného na službě je výsledek `/Blog/ReadPost/17`vždy, i když `BlogController` neexistuje nebo `ReadPost` nemá metodu Action. Podle očekávání, směrování koncového bodu v ASP.NET Core 2,2 nebo `/Blog/ReadPost/17` vyšší vytvoří, pokud metoda Action existuje. *Směrování koncových bodů ale vytvoří prázdný řetězec, pokud akce neexistuje.* V koncepčním případě směrování koncového bodu nepředpokládá, že koncový bod existuje, pokud akce neexistuje.

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

  Pokud je `/Store/Product/18` identifikátor URI v ASP.NET Core 2,1 nebo starším, odkaz vygenerovaný na `@Url.Page("/Login")` stránce úložiště/informace je `/Login/18`. `id` Hodnota 18 se znovu použije, i když je cíl propojení jinou součástí aplikace. Hodnota trasy v kontextu `/Login` stránky je pravděpodobně hodnota ID uživatele, nikoli hodnota ID produktu úložiště. `id`

  V rámci směrování koncového bodu s ASP.NET Core 2,2 nebo novějším `/Login`je výsledkem. Okolní hodnoty se znovu nepoužijí, pokud je cíl propojení jinou akcí nebo stránkou.

* Syntaxe parametru trasy s kulatým Trip: Lomítka nejsou kódována při použití syntaxe parametrů Double-hvězdička (`**`) catch-ALL.

  Během generování propojení systém směrování zakóduje hodnotu zachycenou parametrem zachycení dvojité hvězdičky (`**`) ( `{**myparametername}`například) s výjimkou lomítka. Směrování s dvojitou hvězdičkou (catch-All `IRouter`) je podporováno v rámci směrování založeného na ASP.NET Core 2,2 nebo novějším.

  V předchozích verzích ASP.NET Core () se podporuje jednoduchá hvězdička All – všechny`{*myparametername}`syntaxe parametrů () a lomítka jsou kódovaná.

  | Cestě              | Odkaz vygeneroval s<br>`Url.Action(new { category = "admin/products" })`&hellip; |
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

### <a name="create-routes"></a>Vytváření tras

Většina aplikací vytváří trasy voláním <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> nebo jedné z podobných metod rozšíření definovaných v. <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> Kterákoli z <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> rozšiřujících metod vytvoří <xref:Microsoft.AspNetCore.Routing.Route> instanci a přidá ji do kolekce tras.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nepřijímá parametr obslužné rutiny trasy. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>přidá pouze trasy, které jsou zpracovávány <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Další informace o směrování v MVC najdete v tématu <xref:mvc/controllers/routing>.

Následující příklad kódu je příkladem <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> volání využívaného typickou ASP.NET Core definice trasy MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Tato šablona odpovídá cestě URL a extrahuje hodnoty tras. Například cesta `/Products/Details/17` generuje následující hodnoty trasy: `{ controller = Products, action = Details, id = 17 }`.

Hodnoty tras se určují rozdělením cesty URL na segmenty a porovnáním jednotlivých segmentů s názvem *parametru trasy* v šabloně směrování. Parametry směrování jsou pojmenovány. Parametry definované ohraničujícím název parametru ve složených závorkách `{ ... }`.

Předchozí šablona může také odpovídat cestě `/` URL a vydávat hodnoty. `{ controller = Home, action = Index }` K tomu dochází, `{controller}` protože `{action}` parametry směrování a `id` mají výchozí hodnoty a parametr trasy je nepovinný. Znak rovná se (`=`) následovaný hodnotou po názvu parametru trasy, který definuje výchozí hodnotu parametru. Otazník (`?`) po názvu parametru trasy definuje volitelný parametr.

Parametry směrování s výchozí hodnotou *vždy* vytvoří hodnotu trasy, když odpovídá trasa. Volitelné parametry neposkytují hodnotu trasy, pokud nebyl nalezen žádný odpovídající segment cesty URL. Podrobný popis scénářů a syntaxe šablon směrování najdete v části referenční dokumentace k [šabloně směrování](#route-template-reference) .

V následujícím příkladu definuje definice `{id:int}` parametru trasy [](#route-constraint-reference) `id` omezení trasy pro parametr trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Tato šablona odpovídá cestě URL, například `/Products/Details/17` ne. `/Products/Details/Apples` Omezení tras implementují <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a kontrolují hodnoty směrování a ověřují je. V tomto příkladu musí být hodnota `id` trasy převoditelná na celé číslo. Vysvětlení omezení trasy poskytovaných rozhraním naleznete v tématu [Route-Constraint-reference](#route-constraint-reference) .

Další přetížení <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> přijímají hodnoty pro `constraints`, `dataTokens`a `defaults`. Typické použití těchto parametrů je předání anonymního typu objektu, kde názvy vlastností anonymního typu odpovídají názvům parametrů tras.

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

Předchozí šablona odpovídá cestě URL jako `/Blog/All-About-Routing/Introduction` a extrahuje hodnoty. `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` Výchozí hodnoty tras pro `controller` a `action` jsou vytvářeny trasou, i když v šabloně nejsou odpovídající parametry směrování. V šabloně směrování lze zadat výchozí hodnoty. Parametr trasy je definován jako *catch-All* pomocí vzhledu dvojité hvězdičky (`**`) před názvem parametru trasy. `article` Catch – všechny parametry tras zaznamenávají zbytek cesty URL a můžou taky odpovídat prázdnému řetězci.

Následující příklad přidá omezení trasy a datové tokeny:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Předchozí šablona odpovídá cestě URL jako `/en-US/Products/5` a extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` a datové tokeny `{ locale = en-US }`.

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

Tokeny ve složených závorkách (`{ ... }`) definují *parametry trasy* , které jsou svázané, pokud je trasa shodná. V segmentu směrování můžete definovat více než jeden parametr trasy, ale musí být oddělený literálovou hodnotou. Například `{controller=Home}{action=Index}` není platná trasa, protože hodnota literálu není mezi `{controller}` a `{action}`. Tyto parametry tras musí mít název a můžou mít zadané další atributy.

Textový literál jiný než parametry směrování (například `{id}`) a oddělovač `/` cesty musí odpovídat textu v adrese URL. U porovnávání textu se nerozlišují malá a velká písmena a na základě dekódovat reprezentace cesty URL. Chcete-li spárovat oddělovač parametrů trasy`{` (nebo `}`) literálu, zařídíte oddělovač opakováním znaku (`{{` nebo `}}`).

Vzory adres URL, které se pokoušejí zachytit název souboru s volitelnou příponou souboru, mají další požadavky. Představte si třeba šablonu `files/{filename}.{ext?}`. Pokud hodnoty pro obojí `filename` i `ext` existují, naplní se obě hodnoty. Je-li v adrese `filename` URL pouze hodnota, která je v adrese URL, odpovídá trasa, protože`.`koncová tečka () je volitelná. Tuto trasu odpovídají následujícím adresám URL:

* `/files/myFile.txt`
* `/files/myFile`

K vytvoření vazby na zbytek identifikátoru URI můžete použít hvězdičku (`*`) nebo dvojitou hvězdičku (`**`) jako předponu parametru trasy. Tyto parametry se nazývají *catch-All* . Například `blog/{**slug}` odpovídá jakémukoli identifikátoru URI, který `/blog` začíná a má libovolnou hodnotu, `slug` která je za ní přiřazena hodnota trasy. Catch – všechny parametry můžou odpovídat také prázdnému řetězci.

Parametr catch-All řídí příslušné znaky, pokud je použita cesta pro vygenerování adresy URL, včetně znaků oddělovače (`/`). Například trasa `foo/{*path}` s hodnotami `{ path = "my/path" }` trasy vygeneruje `foo/my%2Fpath`. Všimněte si řídicího znaku lomítka. Do oddělovacích znaků cesty pro přenos cest použijte `**` předponu parametru Route. Trasa `foo/{**path}` s `{ path = "my/path" }` vygeneruje `foo/my/path`.

Parametry směrování můžou mít *výchozí hodnoty* určené zadáním výchozí hodnoty za názvem parametru odděleným symbolem rovná se (`=`). Například `{controller=Home}` `controller`definuje `Home` jako výchozí hodnotu pro. Výchozí hodnota se použije v případě, že v adrese URL parametru není k dispozici žádná hodnota. Parametry směrování jsou povinny připojením otazníku (`?`) na konec názvu parametru, jako v. `id?` Rozdíl mezi volitelnými hodnotami a výchozími parametry směrování je, že parametr trasy s výchozí hodnotou vždy vytvoří hodnotu&mdash;, která má volitelný parametr hodnotu, pouze pokud je hodnota poskytnuta adresou URL požadavku.

Parametry směrování můžou mít omezení, která se musí shodovat s hodnotou trasy svázanou z adresy URL. Přidání dvojtečky (`:`) a názvu omezení za názvem parametru trasy Určuje *vložené omezení* pro parametr trasy. Pokud omezení vyžaduje argumenty, jsou uzavřeny v závorkách (`(...)`) za názvem omezení. Přidáním dalších dvojtečk (`:`) a názvu omezení lze zadat více vložených omezení.

Název omezení a argumenty jsou předány <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> službě za účelem vytvoření <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> instance pro použití při zpracování adresy URL. Například šablona `blog/{article:minlength(10)}` trasy `minlength` určuje omezení s argumentem `10`. Další informace o omezeních tras a seznam omezení poskytovaných rozhraním najdete v části [referenční informace k omezením trasy](#route-constraint-reference) .

Parametry směrování můžou mít také transformaci parametrů, které transformují hodnotu parametru při generování odkazů a porovnání akcí a stránek s adresami URL. Podobně jako omezení můžou být transformátory parametrů přidány do parametru trasy, a to přidáním dvojtečky`:`() a názvu transformátoru za názvem parametru trasy. Například šablona `blog/{article:slugify}` trasy `slugify` určuje transformátor. Další informace o transformačních parametrech naleznete v části [Referenční příručka pro parametry](#parameter-transformer-reference) transformátoru.

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
| `int` | `{id:int}` | `123456789`, `-123456789` | Odpovídá jakémukoli celému číslu |
| `bool` | `{active:bool}` | `true`, `FALSE` | Odpovídá `true` nebo`false` (nerozlišuje velká a malá písmena) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platné `DateTime` hodnotě (v neutrální jazykové verzi – viz upozornění) |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platné `decimal` hodnotě (v neutrální jazykové verzi – viz upozornění) |
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platné `double` hodnotě (v neutrální jazykové verzi – viz upozornění) |
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platné `float` hodnotě (v neutrální jazykové verzi – viz upozornění) |
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

Regulární výrazy používají oddělovače a tokeny podobné těm, které používá směrování a C# jazyk. Tokeny regulárního výrazu musí být uvozeny řídicími znaky. Chcete-li použít regulární `^\d{3}-\d{2}-\d{4}$` výraz ve směrování, musí `\` mít výraz (jedno zpětné lomítko), které je zadáno v řetězci `\\` jako (dvojité zpětné lomítko) ve C# zdrojovém souboru, aby mohl řídicí znak`\` řídicí znak řetězce (Pokud se nepoužívají [doslovné řetězce literálů](/dotnet/csharp/language-reference/keywords/string)). Chcete-li řídicí znaky oddělovače parametrů směrování`{`( `}`, `[`, `]`,) `}``{{` `[[`, poklikejte na znaky ve výrazu (,,, ).`]]` V následující tabulce je uveden regulární výraz a verze s řídicím znakem.

| Regulární výraz    | Regulární výraz s řídicím znakem     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy používané v směrování často začínají znakem stříšky`^`() a odpovídají počáteční pozici řetězce. Výrazy se často končí znakem dolaru (`$`) a koncem řetězce. Znaky `^` a`$` zajišťují, že regulární výraz odpovídá celé hodnotě parametru Route. Bez znaků `$` a regulární výraz odpovídá jakémukoli podřetězci v rámci řetězce, což je často nežádoucí. `^` Následující tabulka obsahuje příklady a vysvětlení, proč se shodují nebo neshodují.

| Výraz   | String    | Shoda | Komentář               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | Dobrý den     | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | mz        | Ano   | Výraz shody    |
| `[a-z]{2}`   | MZ        | Ano   | Nerozlišuje velká a malá písmena    |
| `^[a-z]{2}$` | Dobrý den     | Ne    | Viz `^` a `$` vyšší |
| `^[a-z]{2}$` | 123abc456 | Ne    | Viz `^` a `$` vyšší |

Další informace o syntaxi regulárního výrazu naleznete v tématu [.NET Framework regulární výrazy](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr na známou sadu možných hodnot, použijte regulární výraz. Například `{action:regex(^(list|get|create)$)}` odpovídápouze`list`hodnotě `get`trasy, nebo`create`. `action` Pokud je předána do slovníku omezení, je `^(list|get|create)$` řetězec ekvivalentní. Omezení, která jsou předána do slovníku omezení (nejsou vložena v rámci šablony), která neodpovídají jednomu ze známých omezení, jsou také považována za regulární výrazy.

## <a name="custom-route-constraints"></a>Vlastní omezení trasy

Kromě předdefinovaných omezení trasy lze vytvořit vlastní omezení trasy implementací <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozhraní. Rozhraní obsahuje jedinou metodu, `Match`která vrací `true` , pokud je omezení splněno, a `false` jinak. <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>

Pokud chcete použít vlastní <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, musí být typ omezení trasy registrovaný <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> v aplikaci v kontejneru služeb aplikace. Je slovník, který mapuje klíče omezení tras na <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementace, které ověřují tato omezení. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> Aplikace <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> se dá v `Startup.ConfigureServices` rámci služeb aktualizovat buď jako součást [služby. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) volání nebo přímou konfigurací <xref:Microsoft.AspNetCore.Routing.RouteOptions> s. `services.Configure<RouteOptions>` Příklad:

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

Například vlastní `slugify` parametr Transformer ve vzoru `blog\{article:slugify}` směrování s `Url.Action(new { article = "MyTestArticle" })` vygenerováním `blog\my-test-article`.

Chcete-li použít transformující parametr ve schématu směrování, nakonfigurujte jej nejprve pomocí <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>: `Startup.ConfigureServices`

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Transformátory parametrů používá rozhraní k transformaci identifikátoru URI, kde se Endpoint vyřeší. ASP.NET Core MVC například používá transformaci parametrů k `area`transformaci hodnoty trasy používané k porovnávání, `controller`, `action`a `page`.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

S předchozí trasou se akce `SubscriptionManagementController.GetAll()` shodují s identifikátorem URI. `/subscription-management/get-all` Transformující parametr nemění hodnoty trasy použité k vygenerování odkazu. Například `Url.Action("GetAll", "SubscriptionManagement")` výstupy `/subscription-management/get-all`.

ASP.NET Core poskytuje konvence rozhraní API pro použití parametrů Transformers s vygenerovanými trasami:

* ASP.NET Core MVC má `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` konvenci rozhraní API. Tato konvence aplikuje na všechny trasy atributů v aplikaci zadaného parametru Transformer. Parametr Transformer transformuje tokeny, když jsou nahrazeny. Další informace najdete v tématu [Použití transformátoru parametrů k přizpůsobení náhrady tokenu](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages má `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` konvence rozhraní API. Tato konvence u všech automaticky zjištěných Razor Pages aplikuje zadaný transformátor parametrů. Parametr Transformer přetransformuje segmenty složky a názvu souboru na trasy Razor Pages. Další informace najdete v tématu [Použití transformátoru parametrů k přizpůsobení cest stránky](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Odkaz na generování adresy URL

Následující příklad ukazuje, jak vygenerovat odkaz na trasu s ohledem na slovník hodnot směrování a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Vygenerovalo se na konci předchozí `/package/create/123`ukázky. <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> Slovník poskytuje `operation` hodnoty a `id` trasu pro šablonu sledování trasy `package/{operation}/{id}`balíčku. Podrobnosti najdete v ukázkovém kódu v části [použití middleware pro směrování](#use-routing-middleware) nebo v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Druhý parametr <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> konstruktoru je kolekce *okolních hodnot*. Okolní hodnoty jsou vhodné k použití, protože omezují počet hodnot, které vývojář musí určit v rámci kontextu požadavku. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. `About` V akci `Home` `Index` aplikace ASP.NET Core MVC není nutné zadávat hodnotu trasy kontroléru&mdash; ,kterábudepropojenasakcí,kterájepoužitaokolníhodnotou.`HomeController`

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
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
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

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Základy směrování

Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné. Výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}`:

* Podporuje základní a popisné schéma směrování.
* Je užitečným výchozím bodem pro aplikace založené na uživatelském rozhraní.

Vývojáři obvykle přidávají další stručný trasy do oblastí s vysokým provozem v aplikaci ve zvláštních situacích (například koncové body blogu a elektronického obchodování) pomocí [Směrování atributů](xref:mvc/controllers/routing#attribute-routing) nebo vyhrazených konvenčních tras.

Webové rozhraní API by mělo používat směrování atributů k modelování funkcí aplikace jako sady prostředků, ve kterých jsou operace reprezentované příkazy HTTP. To znamená, že mnoho operací (například GET, POST) na stejném logickém prostředku bude používat stejnou adresu URL. Směrování atributů poskytuje úroveň řízení, která je nutná k pečlivému návrhu rozložení veřejného koncového bodu rozhraní API.

Aplikace Razor Pages používají výchozí konvenční směrování pro obsluhu pojmenovaných prostředků ve složce *Pages* v aplikaci. K dispozici jsou další konvence, které vám umožní přizpůsobit Razor Pages chování směrování. Další informace naleznete v tématu <xref:razor-pages/index> a <xref:razor-pages/razor-pages-conventions>.

Podpora generování adresy URL umožňuje, aby se aplikace vyvinula bez adres URL s pevným kódováním, aby bylo možné propojit aplikaci dohromady. Tato podpora umožňuje začít se základní konfigurací směrování a upravovat trasy po určení rozložení prostředků aplikace.

Směrování používá *trasy* (implementace <xref:Microsoft.AspNetCore.Routing.IRouter>) pro:

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

Směrování je k kanálu <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> [middleware](xref:fundamentals/middleware/index) připojeno třídou. [ASP.NET Core MVC](xref:mvc/overview) v rámci své konfigurace přidává směrování do kanálu middlewaru a zpracovává směrování v MVC a Razor Pages aplikacích. Informace o tom, jak používat směrování jako samostatnou součást, najdete v části [použití middlewaru pro směrování](#use-routing-middleware) .

### <a name="url-matching"></a>Shoda adresy URL

Shoda adresy URL je proces, podle kterého směrování odesílá příchozí požadavek *obslužné rutině*. Tento proces je založený na datech v cestě URL, ale dá se rozšířit, aby v žádosti mohla být považovat všechna data. Schopnost odesílat žádosti na samostatné obslužné rutiny je klíč pro škálování velikosti a složitosti aplikace.

Příchozí požadavky vstupují <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>do, který <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> volá metodu v každé trase v sekvenci. Instance zvolí, zda má být žádost *zpracována* nastavením [RouteContext. Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) na jinou hodnotu než null <xref:Microsoft.AspNetCore.Http.RequestDelegate>. <xref:Microsoft.AspNetCore.Routing.IRouter> Pokud trasa nastaví obslužnou rutinu pro požadavek, zpracování směrování se zastaví a obslužná rutina se vyvolá pro zpracování žádosti. Pokud se pro zpracování požadavku nenajde žádná obslužná rutina tras, middleware si požadavek doplní k dalšímu middlewaru v kanálu žádosti.

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

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Trasy primárně využívají hodnoty tras poskytované nástrojem <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> a <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> k rozhodnutí, zda je možné vygenerovat adresu URL a jaké hodnoty mají být zahrnuty. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> Je sada hodnot tras, které byly vytvořeny z porovnání s aktuálním požadavkem. Naproti tomu <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> jsou hodnoty trasy, které určují, jak se má vygenerovat požadovaná adresa URL pro aktuální operaci. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> Je k dispozici v případě, že trasa má získat služby nebo další data přidružená k aktuálnímu kontextu.

> [!TIP]
> [VirtualPathContext. Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) se považuje za sadu přepsání pro [VirtualPathContext. AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). Generování adresy URL se pokusí znovu použít hodnoty směrování z aktuální žádosti, aby se vygenerovaly adresy URL pro odkazy pomocí stejné trasy nebo hodnoty tras.

<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> Výstupem<xref:Microsoft.AspNetCore.Routing.VirtualPathData>je. <xref:Microsoft.AspNetCore.Routing.VirtualPathData>je paralelní z <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData><xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> obsahuje adresu URL výstupu a některé další vlastnosti, které by měly být nastavené trasou.

Vlastnost [VirtualPathData. VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) obsahuje *virtuální cestu* vytvořenou trasou. V závislosti na vašich potřebách možná budete muset zpracovat cestu dále. Pokud chcete vygenerovanou adresu URL vykreslit ve formátu HTML, předřaďte základní cestu aplikace.

[VirtualPathData. router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) je odkaz na trasu, která adresu URL úspěšně vygenerovala.

Vlastnosti [VirtualPathData. DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) je slovník dalších dat souvisejících s trasou, která adresu URL vygenerovala. To je paralelní pro [parametr RouteData. Datatokeny](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Vytváření tras

Směrování poskytuje <xref:Microsoft.AspNetCore.Routing.Route> třídu jako standardní <xref:Microsoft.AspNetCore.Routing.IRouter>implementaci. <xref:Microsoft.AspNetCore.Routing.Route>používá syntaxi *šablony směrování* k definování vzorů, které se budou shodovat s cestou <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> URL, když je volána. <xref:Microsoft.AspNetCore.Routing.Route>používá stejnou šablonu trasy k vygenerování adresy URL, <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> když se zavolá.

Většina aplikací vytváří trasy voláním <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> nebo jedné z podobných metod rozšíření definovaných v. <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> Kterákoli z <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> rozšiřujících metod vytvoří <xref:Microsoft.AspNetCore.Routing.Route> instanci a přidá ji do kolekce tras.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nepřijímá parametr obslužné rutiny trasy. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>přidá pouze trasy, které jsou zpracovávány <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Výchozí obslužná rutina je `IRouter`a obslužná rutina nemusí požadavek zpracovat. Například ASP.NET Core MVC je obvykle nakonfigurován jako výchozí obslužná rutina, která zpracovává pouze požadavky, které odpovídají dostupnému kontroleru a akci. Další informace o směrování v MVC najdete v tématu <xref:mvc/controllers/routing>.

Následující příklad kódu je příkladem <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> volání využívaného typickou ASP.NET Core definice trasy MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Tato šablona odpovídá cestě URL a extrahuje hodnoty tras. Například cesta `/Products/Details/17` generuje následující hodnoty trasy: `{ controller = Products, action = Details, id = 17 }`.

Hodnoty tras se určují rozdělením cesty URL na segmenty a porovnáním jednotlivých segmentů s názvem *parametru trasy* v šabloně směrování. Parametry směrování jsou pojmenovány. Parametry definované ohraničujícím název parametru ve složených závorkách `{ ... }`.

Předchozí šablona může také odpovídat cestě `/` URL a vydávat hodnoty. `{ controller = Home, action = Index }` K tomu dochází, `{controller}` protože `{action}` parametry směrování a `id` mají výchozí hodnoty a parametr trasy je nepovinný. Znak rovná se (`=`) následovaný hodnotou po názvu parametru trasy, který definuje výchozí hodnotu parametru. Otazník (`?`) po názvu parametru trasy definuje volitelný parametr.

Parametry směrování s výchozí hodnotou *vždy* vytvoří hodnotu trasy, když odpovídá trasa. Volitelné parametry neposkytují hodnotu trasy, pokud nebyl nalezen žádný odpovídající segment cesty URL. Podrobný popis scénářů a syntaxe šablon směrování najdete v části referenční dokumentace k [šabloně směrování](#route-template-reference) .

V následujícím příkladu definuje definice `{id:int}` parametru trasy [](#route-constraint-reference) `id` omezení trasy pro parametr trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Tato šablona odpovídá cestě URL, například `/Products/Details/17` ne. `/Products/Details/Apples` Omezení tras implementují <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a kontrolují hodnoty směrování a ověřují je. V tomto příkladu musí být hodnota `id` trasy převoditelná na celé číslo. Vysvětlení omezení trasy poskytovaných rozhraním naleznete v tématu [Route-Constraint-reference](#route-constraint-reference) .

Další přetížení <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> přijímají hodnoty pro `constraints`, `dataTokens`a `defaults`. Typické použití těchto parametrů je předání anonymního typu objektu, kde názvy vlastností anonymního typu odpovídají názvům parametrů tras.

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

Předchozí šablona odpovídá cestě URL jako `/Blog/All-About-Routing/Introduction` a extrahuje hodnoty. `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` Výchozí hodnoty tras pro `controller` a `action` jsou vytvářeny trasou, i když v šabloně nejsou odpovídající parametry směrování. V šabloně směrování lze zadat výchozí hodnoty. Parametr Route je definován jako *catch-All* pomocí vzhledu hvězdičky (`*`) před názvem parametru trasy. `article` Catch – všechny parametry tras zaznamenávají zbytek cesty URL a můžou taky odpovídat prázdnému řetězci.

Následující příklad přidá omezení trasy a datové tokeny:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Předchozí šablona odpovídá cestě URL jako `/en-US/Products/5` a extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` a datové tokeny `{ locale = en-US }`.

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

Pokud konfigurujete jednu trasu, zavolejte <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> předávání `IRouter` do instance. Nebudete muset používat <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

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

Některé z uvedených metod, například <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Http.RequestDelegate>vyžadují. Je použit jako *obslužná rutina trasy* při porovnání trasy. <xref:Microsoft.AspNetCore.Http.RequestDelegate> Jiné metody v této rodině umožňují konfigurovat kanál middlewaru pro použití jako obslužná rutina trasy. Pokud metoda nepřijímá obslužnou rutinu, <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>například, používá <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. `Map*`

`Map[Verb]` Metody používají omezení k omezení trasy na příkaz HTTP v názvu metody. Například viz <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> a <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Odkaz na šablonu směrování

Tokeny ve složených závorkách (`{ ... }`) definují *parametry trasy* , které jsou svázané, pokud je trasa shodná. V segmentu směrování můžete definovat více než jeden parametr trasy, ale musí být oddělený literálovou hodnotou. Například `{controller=Home}{action=Index}` není platná trasa, protože hodnota literálu není mezi `{controller}` a `{action}`. Tyto parametry tras musí mít název a můžou mít zadané další atributy.

Textový literál jiný než parametry směrování (například `{id}`) a oddělovač `/` cesty musí odpovídat textu v adrese URL. U porovnávání textu se nerozlišují malá a velká písmena a na základě dekódovat reprezentace cesty URL. Chcete-li spárovat oddělovač parametrů trasy`{` (nebo `}`) literálu, zařídíte oddělovač opakováním znaku (`{{` nebo `}}`).

Vzory adres URL, které se pokoušejí zachytit název souboru s volitelnou příponou souboru, mají další požadavky. Představte si třeba šablonu `files/{filename}.{ext?}`. Pokud hodnoty pro obojí `filename` i `ext` existují, naplní se obě hodnoty. Je-li v adrese `filename` URL pouze hodnota, která je v adrese URL, odpovídá trasa, protože`.`koncová tečka () je volitelná. Tuto trasu odpovídají následujícím adresám URL:

* `/files/myFile.txt`
* `/files/myFile`

K vytvoření vazby na zbytek identifikátoru URI můžete použít hvězdičku (`*`) jako předponu parametru trasy. Tento parametr se nazývá *catch-All* . Například `blog/{*slug}` odpovídá jakémukoli identifikátoru URI, který `/blog` začíná a má libovolnou hodnotu, `slug` která je za ní přiřazena hodnota trasy. Catch – všechny parametry můžou odpovídat také prázdnému řetězci.

Parametr catch-All řídí příslušné znaky, pokud je použita cesta pro vygenerování adresy URL, včetně znaků oddělovače (`/`). Například trasa `foo/{*path}` s hodnotami `{ path = "my/path" }` trasy vygeneruje `foo/my%2Fpath`. Všimněte si řídicího znaku lomítka.

Parametry směrování můžou mít *výchozí hodnoty* určené zadáním výchozí hodnoty za názvem parametru odděleným symbolem rovná se (`=`). Například `{controller=Home}` `controller`definuje `Home` jako výchozí hodnotu pro. Výchozí hodnota se použije v případě, že v adrese URL parametru není k dispozici žádná hodnota. Parametry směrování jsou povinny připojením otazníku (`?`) na konec názvu parametru, jako v. `id?` Rozdíl mezi volitelnými hodnotami a výchozími parametry směrování je, že parametr trasy s výchozí hodnotou vždy vytvoří hodnotu&mdash;, která má volitelný parametr hodnotu, pouze pokud je hodnota poskytnuta adresou URL požadavku.

Parametry směrování můžou mít omezení, která se musí shodovat s hodnotou trasy svázanou z adresy URL. Přidání dvojtečky (`:`) a názvu omezení za názvem parametru trasy Určuje *vložené omezení* pro parametr trasy. Pokud omezení vyžaduje argumenty, jsou uzavřeny v závorkách (`(...)`) za názvem omezení. Přidáním dalších dvojtečk (`:`) a názvu omezení lze zadat více vložených omezení.

Název omezení a argumenty jsou předány <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> službě za účelem vytvoření <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> instance pro použití při zpracování adresy URL. Například šablona `blog/{article:minlength(10)}` trasy `minlength` určuje omezení s argumentem `10`. Další informace o omezeních tras a seznam omezení poskytovaných rozhraním najdete v části [referenční informace k omezením trasy](#route-constraint-reference) .

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
| `int` | `{id:int}` | `123456789`, `-123456789` | Odpovídá jakémukoli celému číslu |
| `bool` | `{active:bool}` | `true`, `FALSE` | Odpovídá `true` nebo`false` (nerozlišuje velká a malá písmena) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platné `DateTime` hodnotě (v neutrální jazykové verzi – viz upozornění) |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platné `decimal` hodnotě (v neutrální jazykové verzi – viz upozornění) |
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platné `double` hodnotě (v neutrální jazykové verzi – viz upozornění) |
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platné `float` hodnotě (v neutrální jazykové verzi – viz upozornění) |
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

Regulární výrazy používají oddělovače a tokeny podobné těm, které používá směrování a C# jazyk. Tokeny regulárního výrazu musí být uvozeny řídicími znaky. Chcete-li použít regulární `^\d{3}-\d{2}-\d{4}$` výraz ve směrování, musí `\` mít výraz (jedno zpětné lomítko), které je zadáno v řetězci `\\` jako (dvojité zpětné lomítko) ve C# zdrojovém souboru, aby mohl řídicí znak`\` řídicí znak řetězce (Pokud se nepoužívají [doslovné řetězce literálů](/dotnet/csharp/language-reference/keywords/string)). Chcete-li řídicí znaky oddělovače parametrů směrování`{`( `}`, `[`, `]`,) `}``{{` `[[`, poklikejte na znaky ve výrazu (,,, ).`]]` V následující tabulce je uveden regulární výraz a verze s řídicím znakem.

| Regulární výraz    | Regulární výraz s řídicím znakem     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy používané v směrování často začínají znakem stříšky`^`() a odpovídají počáteční pozici řetězce. Výrazy se často končí znakem dolaru (`$`) a koncem řetězce. Znaky `^` a`$` zajišťují, že regulární výraz odpovídá celé hodnotě parametru Route. Bez znaků `$` a regulární výraz odpovídá jakémukoli podřetězci v rámci řetězce, což je často nežádoucí. `^` Následující tabulka obsahuje příklady a vysvětlení, proč se shodují nebo neshodují.

| Výraz   | String    | Shoda | Komentář               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | Dobrý den     | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | Shody podřetězců     |
| `[a-z]{2}`   | mz        | Ano   | Výraz shody    |
| `[a-z]{2}`   | MZ        | Ano   | Nerozlišuje velká a malá písmena    |
| `^[a-z]{2}$` | Dobrý den     | Ne    | Viz `^` a `$` vyšší |
| `^[a-z]{2}$` | 123abc456 | Ne    | Viz `^` a `$` vyšší |

Další informace o syntaxi regulárního výrazu naleznete v tématu [.NET Framework regulární výrazy](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr na známou sadu možných hodnot, použijte regulární výraz. Například `{action:regex(^(list|get|create)$)}` odpovídápouze`list`hodnotě `get`trasy, nebo`create`. `action` Pokud je předána do slovníku omezení, je `^(list|get|create)$` řetězec ekvivalentní. Omezení, která jsou předána do slovníku omezení (nejsou vložena v rámci šablony), která neodpovídají jednomu ze známých omezení, jsou také považována za regulární výrazy.

## <a name="custom-route-constraints"></a>Vlastní omezení trasy

Kromě předdefinovaných omezení trasy lze vytvořit vlastní omezení trasy implementací <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozhraní. Rozhraní obsahuje jedinou metodu, `Match`která vrací `true` , pokud je omezení splněno, a `false` jinak. <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>

Pokud chcete použít vlastní <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, musí být typ omezení trasy registrovaný <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> v aplikaci v kontejneru služeb aplikace. Je slovník, který mapuje klíče omezení tras na <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementace, které ověřují tato omezení. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> Aplikace <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> se dá v `Startup.ConfigureServices` rámci služeb aktualizovat buď jako součást [služby. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) volání nebo přímou konfigurací <xref:Microsoft.AspNetCore.Routing.RouteOptions> s. `services.Configure<RouteOptions>` Příklad:

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

Vygenerovalo se na konci předchozí `/package/create/123`ukázky. <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> Slovník poskytuje `operation` hodnoty a `id` trasu pro šablonu sledování trasy `package/{operation}/{id}`balíčku. Podrobnosti najdete v ukázkovém kódu v části [použití middleware pro směrování](#use-routing-middleware) nebo v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Druhý parametr <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> konstruktoru je kolekce *okolních hodnot*. Okolní hodnoty jsou vhodné k použití, protože omezují počet hodnot, které vývojář musí určit v rámci kontextu požadavku. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. `About` V akci `Home` `Index` aplikace ASP.NET Core MVC není nutné zadávat hodnotu trasy kontroléru&mdash; ,kterábudepropojenasakcí,kterájepoužitaokolníhodnotou.`HomeController`

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
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end
