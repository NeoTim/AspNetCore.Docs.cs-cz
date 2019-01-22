---
title: Směrování v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak je zodpovědná za mapování požadavku identifikátory URI pro koncový bod selektory a dispatching příchozí požadavky do koncových bodů směrování ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2019
uid: fundamentals/routing
ms.openlocfilehash: 070200b6fdc8b3178e2b7b12375ba1dd56080697
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444373"
---
# <a name="routing-in-aspnet-core"></a>Směrování v ASP.NET Core

Podle [Ryanem Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/), a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="<= aspnetcore-1.1"

1.1 verzi tohoto tématu, stáhněte si [směrování v ASP.NET Core (verze 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Routing_1.x.pdf).

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Směrování je zodpovědná za mapování požadavku identifikátory URI pro koncový bod selektory a dispatching příchozí požadavky do koncových bodů. Trasy jsou definovány v aplikaci a nakonfigurovaná při spuštění aplikace. Trasy můžete volitelně extrakci hodnot z adresy URL, obsaženy v požadavku a tyto hodnoty je pak možné pro zpracování požadavku. Pomocí informací o směrování z aplikace, směrování je také možnost k vygenerování adres URL, které mapují na koncový bod selektorů.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Pokud chcete používat nejnovější směrování scénáře v ASP.NET Core 2.2, zadejte [verze kompatibility](xref:mvc/compatibility-version) MVC služby registrace v `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

`EnableEndpointRouting` Možnost určuje, pokud směrování interně používali logiky založené na koncový bod nebo <xref:Microsoft.AspNetCore.Routing.IRouter>– na základě logiky ASP.NET Core 2.1 nebo dřívější. Pokud je verze kompatibility nastavena na verzi 2.2 nebo vyšší, výchozí hodnota je `true`. Nastavte hodnotu na `false` používat předchozí logiku směrování:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Další informace o <xref:Microsoft.AspNetCore.Routing.IRouter>– směrování, přečtěte si téma [ASP.NET Core 2.1 verzi tohoto tématu](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Směrování je zodpovědná za požadavku mapování identifikátorů URI obslužné rutiny trasy a odeslání příchozích požadavků. Trasy jsou definovány v aplikaci a nakonfigurovaná při spuštění aplikace. Trasy můžete volitelně extrakci hodnot z adresy URL, obsaženy v požadavku a tyto hodnoty je pak možné pro zpracování požadavku. Pomocí nakonfigurované trasy z aplikace, směrování je možné k vygenerování adres URL, které se mapují k obslužné rutině trasy.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Chcete-li použít nejnovější směrování scénáře v ASP.NET Core 2.1, zadejte [verze kompatibility](xref:mvc/compatibility-version) MVC služby registrace v `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

::: moniker-end

> [!IMPORTANT]
> Tento dokument popisuje směrování nízké úrovně ASP.NET Core. Informace o směrování ASP.NET Core MVC najdete v tématu <xref:mvc/controllers/routing>. Informace o konvence směrování ve stránky Razor, naleznete v tématu <xref:razor-pages/razor-pages-conventions>.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Základní informace o směrování

Většina aplikací by měl zvolte základní a popisný směrování schéma, tak, že jsou adresy URL čitelný a srozumitelný. Výchozí trasa konvenční `{controller=Home}/{action=Index}/{id?}`:

* Podporuje směrování plán basic a popisný.
* Je vytvoření užitečného počátečního bodu pro aplikace založené na uživatelském rozhraní.

Vývojáři běžně přidat další stručném trasy k oblastem velkým provozem v specializované situacích (třeba blogu a elektronického obchodování koncových bodů) pomocí aplikace [směrováním atributů](xref:mvc/controllers/routing#attribute-routing) nebo vyhrazené konvenční trasy.

Webové rozhraní API používejte směrováním atributů pro model její funkce jako sada prostředků kde operace reprezentovány příkazy HTTP. To znamená, že mnoho operací (například GET, POST) se stejným prostředkem logické bude používat stejnou adresu URL. Směrování atributů poskytuje úroveň ovládacího prvku, který je nezbytný pečlivě návrh rozložení veřejný koncový bod rozhraní API.

Aplikace stránky Razor pomocí konvenční směrování pro obsluhu pojmenovaným prostředkům ve výchozím nastavení *stránky* složky aplikace. Další konvence jsou k dispozici, které umožňují přizpůsobit chování směrování pro stránky Razor. Další informace naleznete v tématu <xref:razor-pages/index> a <xref:razor-pages/razor-pages-conventions>.

Podpora generování adres URL umožňuje aplikaci byly vyvinuty bez pevně kódováno pomocí adres URL pro aplikaci se propojují. Tato podpora umožňuje počínaje základní konfigurace směrování a úprava směrování po aplikace prostředků rozložení je určeno.

::: moniker range=">= aspnetcore-2.2"

Směrování používá *koncové body* (`Endpoint`) pro reprezentaci logické koncové body v aplikaci.

Koncový bod definuje delegáta pro zpracování požadavků a kolekci libovolného metadat. Metadata se vyskytující aspekty používané implementovat na základě zásad a konfigurace, které jsou připojené k každý koncový bod.

Směrování systém má následující vlastnosti:

* Syntaxe šablony trasy se používá k definování tras s parametry tokenizovaná trasy.
* Konfigurace koncového bodu konvenční – vizuální styl a atributu style je povolený.
* `IRouteConstraint` slouží k určení, zda parametr adresy URL obsahuje platnou hodnotu pro daný koncový bod omezení.
* Modely aplikace, jako jsou například stránky MVC a Razor, zaregistrujte všechny své koncové body, které mají předvídatelné provádění scénáře.
* Implementace směrování provádí rozhodnutí o směrování, bez ohledu na to požadovaného v kanálu middlewaru.
* Middleware, který se zobrazí po Middleware směrování, můžete si prohlédnout výsledek směrování Middleware rozhodnutí koncový bod pro daný identifikátor URI požadavku.
* Je možné vytvořit výčet všech koncových bodů v aplikaci kdekoli v kanálu middlewaru.
* Aplikaci můžete použít směrování k vygenerování adres URL (například pro přesměrování nebo odkazy) na základě informací o koncový bod a vyhnout pevně zakódované adresy URL, která pomáhá udržovatelnost.
* Generování adresy URL je založená na adresy, které podporují libovolného rozšíření:

  * Rozhraní API generátor odkaz (`LinkGenerator`) lze vyřešit odkudkoli pomocí [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) k vygenerování adres URL.
  * Pokud není k dispozici prostřednictvím DI, rozhraní API odkazu generátor `IUrlHelper` nabízí metody pro vytvoření adresy URL.

> [!NOTE]
> S vydáním koncový bod směrování v ASP.NET Core 2.2 koncového bodu propojení je omezená na akce MVC/Razor Pages a stránky. Rozšíření koncového bodu propojení funkcí je plánované v budoucích verzích.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Směrování používá *trasy* (implementace <xref:Microsoft.AspNetCore.Routing.IRouter>) na:

* Příchozí požadavky na mapování *trasy obslužné rutiny*.
* Generování adresy URL použité v odpovědi.

Aplikace má ve výchozím nastavení jedné kolekce tras. Když přijde požadavek, trasy v kolekci se zpracovávají v pořadí, že existují v kolekci. Rozhraní se pokusí porovnat adresy URL příchozích žádosti pro trasu v kolekci voláním <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> metodu na každý postup v kolekci. Odpověď může využívat směrování k vygenerování adres URL (například pro přesměrování nebo odkazy) na základě informací o směrování a vyhnout pevně zakódované adresy URL, která pomáhá udržovatelnost.

Směrování systém má následující vlastnosti:

* Syntaxe šablony trasy se používá k definování tras s parametry tokenizovaná trasy.
* Konfigurace koncového bodu konvenční – vizuální styl a atributu style je povolený.
* `IRouteConstraint` slouží k určení, zda parametr adresy URL obsahuje platnou hodnotu pro daný koncový bod omezení.
* Modely aplikace, jako jsou například stránky MVC a Razor, zaregistrujte všechny jejich tras, které mají předvídatelné provádění scénáře.
* Odpověď může využívat směrování k vygenerování adres URL (například pro přesměrování nebo odkazy) na základě informací o směrování a vyhnout pevně zakódované adresy URL, která pomáhá udržovatelnost.
* Generování adresy URL je založená na trasy, které podporují libovolného rozšíření. `IUrlHelper` poskytuje metody pro vytvoření adresy URL.

::: moniker-end

Směrování je připojen k [middleware](xref:fundamentals/middleware/index) profilace podle <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> třídy. [ASP.NET Core MVC](xref:mvc/overview) přidá do kanálu middleware směrování jako součást konfigurace a zpracovává směrování v aplikacích MVC a stránky Razor. Další informace o použití směrování jako samostatné komponenty, najdete v článku [pomocí směrování Middleware](#use-routing-middleware) oddílu.

### <a name="url-matching"></a>Adresa URL odpovídající

::: moniker range=">= aspnetcore-2.2"

Adresa URL odpovídající je proces, ve které směrování odešle příchozí žádost o *koncový bod*. Tento proces je na základě dat v cestě adresy URL, ale je možné rozšířit na zvažte všechna data v žádosti. Schopnost expedovat požadavky k oddělení obslužné rutiny je klíčem k škálování velikosti a složitosti aplikace.

Směrování systému v směrování koncový bod je zodpovědná za všechny dispatching rozhodnutí. Middleware použije zásady na základě vybraného koncového bodu, proto je důležité, že je uvnitř směrování systému k rozhodnutí může mít vliv na odesílání nebo uplatňování zásad zabezpečení.

Při provádění delegáta koncový bod, vlastnosti `RouteContext.RouteData` jsou nastaveny na odpovídající hodnoty, které jsou založené na zpracování požadavku provést doposud.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Adresa URL odpovídající je proces, ve které směrování odešle příchozí požadavek na *obslužná rutina*. Tento proces je na základě dat v cestě adresy URL, ale je možné rozšířit na zvažte všechna data v žádosti. Schopnost expedovat požadavky k oddělení obslužné rutiny je klíčem k škálování velikosti a složitosti aplikace.

Zadejte příchozí požadavky `RouterMiddleware`, který volá <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> metodu na každý postup v pořadí. <xref:Microsoft.AspNetCore.Routing.IRouter> Vybere instanci, jestli se má *zpracování* požadavku tak, že nastavíte [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) na jinou hodnotu null <xref:Microsoft.AspNetCore.Http.RequestDelegate>. Pokud trasa nastaví obslužnou rutinu pro žádosti, směrovat zastaví zpracování a zpracovat požadavek, je vyvolána obslužná rutina. Pokud se žádná obslužná rutina trasy nenajde zpracovat požadavek, middleware předá požadavek na další middleware v kanálu požadavku.

Primární vstup do `RouteAsync` je [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) přidruženého k aktuální žádosti. `RouteContext.Handler` a [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) jsou výstupy nastavit po odpovídající trasy.

Shoda, která volá `RouteAsync` také nastaví vlastnosti `RouteContext.RouteData` na odpovídající hodnoty podle doposud provádí zpracování žádostí.

::: moniker-end

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) je slovník *hodnot trasy* vytvořenými trasy. Tyto hodnoty jsou obvykle určeny tokenizaci adresu URL a je možné přijímat uživatelský vstup nebo další dispatching rozhodnutí v aplikaci.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) je kontejner objektů a dat o další data související s porovnávané trasy. `DataTokens` jsou k dispozici pro podporu přiřazování dat o stavu se každý postup tak, aby aplikace mohly rozhodnutí založené na směrování, které odpovídá. Tyto hodnoty jsou definovány pro vývojáře a proveďte **není** ovlivňují chování směrování žádným způsobem. Kromě toho hodnoty dočasně uložily v `RouteData.DataTokens` může být libovolného typu, rozdíl od `RouteData.Values`, která musí být převeditelný na a z řetězce.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers*) je seznam tras, které účastnila úspěšně odpovídající požadavek. Trasy můžete vnořit do mezi sebou. `Routers` Vlastnost odpovídá cestě prostřednictvím logického stromu tras, z kterých vzniklo shoda. Obecně platí, první položky v `Routers` je kolekce tras a byste měli použít pro generování adresy URL. Poslední položky v `Routers` je obslužná rutina trasy, který odpovídá.

### <a name="url-generation"></a>Generování adresy URL

::: moniker range=">= aspnetcore-2.2"

Generování adresy URL je proces, podle kterých směrování, můžete vytvořit cestu adresy URL na základě sady hodnot trasy. To umožňuje logické rozdělení mezi koncové body a adresy URL, které k nim přístup.

Koncový bod směrování zahrnuje rozhraní API generátor odkaz (`LinkGenerator`). `LinkGenerator` je služba jednotlivý prvek, který můžete získat z DI. Rozhraní API můžete použít mimo kontext provádění požadavku. MVC `IUrlHelper` a scénáře, které spoléhají na `IUrlHelper`, například [pomocných rutin značek](xref:mvc/views/tag-helpers/intro), pomocné rutiny HTML, a [výsledky akce](xref:mvc/controllers/actions), pomocí generátoru odkaz lze zadat odkaz generování možností.

Generátor odkaz je založená na konceptu *adresu* a *adres schémata*. Režim adresy je způsob určení koncových bodů, které by měly být považovány za pro generování odkazů. Názvu trasy a scénáře hodnoty trasy, které mnoho uživatelů znají z MVC/Razor Pages jsou implementovány jako schéma adres.

Generátor odkaz můžete propojit s akce MVC/Razor Pages a stránky pomocí následujících metod rozšíření:

* `GetPathByAction`
* `GetUriByAction`
* `GetPathByPage`
* `GetUriByPage`

Přetížení metody přijímá argumenty, které zahrnují `HttpContext`. Tyto metody jsou funkčně ekvivalentní `Url.Action` a `Url.Page` ale nabízí vyšší flexibilitu a možnosti.

`GetPath*` Metody jsou nejvíc podobný `Url.Action` a `Url.Page` v tom, že se vygenerovat identifikátor URI obsahující absolutní cestu. `GetUri*` Metody vždy generovat absolutní identifikátor URI obsahující schématu a hostitele. Metody, které přijímají `HttpContext` vygenerovat identifikátor URI v kontextu provádění požadavku. Hodnoty okolí trasy, základní cesta adresy URL, schéma a hostitele ze žádosti o provádění se používají, pokud nejsou přepsány.

`LinkGenerator` je volána s adresou. Generování identifikátoru URI dojde ve dvou krocích:

1. Adresa je vázán na seznam koncových bodů, které odpovídají adresu.
1. Každý koncový bod `RoutePattern` je vyhodnocen, dokud nebude nalezen vzor cesty, který odpovídá zadané hodnoty. Výsledný výstup je kombinovat s jinými URI částmi předaná generátoru odkaz a vrácena.

Metody, které poskytuje `LinkGenerator` podporují možnosti generování standardní odkaz pro jakýkoli typ adresy. Nejpohodlnější způsob použití generátoru odkaz je prostřednictvím metody rozšíření, které provádějí operace pro typ konkrétní adresu.

| Metody rozšíření   | Popis                                                         |
| ------------------ | ------------------------------------------------------------------- |
| `GetPathByAddress` | Vygeneruje URI absolutní cestu podle zadané hodnoty. |
| `GetUriByAddress`  | Generuje absolutní identifikátor URI podle zadané hodnoty.             |

> [!WARNING]
> Věnujte pozornost následujícím důsledkům volání `LinkGenerator` metody:
>
> * Použití `GetUri*` metody rozšíření opatrně v konfiguraci aplikace, která neověřuje `Host` záhlaví příchozích požadavků. Pokud `Host` neověří záhlaví příchozích požadavků, vstup nedůvěryhodné požadavku je odeslat zpět klientovi identifikátory URI v zobrazení/stránky. Doporučujeme vám, že všechny aplikace v produkčním prostředí nakonfigurovat svůj server k ověření `Host` záhlaví proti známé platné hodnoty.
>
> * Použití `LinkGenerator` opatrně v middlewaru v kombinaci s `Map` nebo `MapWhen`. `Map*` změní základní cesta provádění požadavku, který má vliv výstup generování odkazů. Všechny `LinkGenerator` rozhraní API umožňují určit základní cesta. Vždy zadejte prázdný základní cestu k vrácení zpět `Map*`uživatele vliv na generování odkazů.

## <a name="differences-from-earlier-versions-of-routing"></a>Rozdíl oproti dřívější verze směrování

Existuje několik rozdílů mezi koncovým bodem směrování v ASP.NET Core 2.2 nebo vyšší a dřívějších verzích směrování v ASP.NET Core:

* Směrování systém koncový bod nepodporuje `IRouter`– na základě rozšíření, včetně dědění z `Route`.

* Směrování koncový bod nepodporuje [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). Použití sady 2.1 [verze kompatibility](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) Chcete-li pokračovat pomocí shimu kompatibility.

* Koncový bod směrování s jiným chováním pro použití malých a velkých generované identifikátory URI, při použití konvenční trasy.

  Vezměte v úvahu následující výchozí šablonou trasy:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Předpokládejme, že vygenerování odkazu na akci s použitím následujícího postupu:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  S `IRouter`– směrování na základě, tento kód vygeneruje identifikátor URI sady `/blog/ReadPost/17`, který respektuje malých a velkých písmen hodnoty zadané trasy. Vytvoří koncový bod směrování v ASP.NET Core 2.2 nebo vyšší `/Blog/ReadPost/17` ("Blogu" je velkými písmeny). Poskytuje koncový bod směrování `IOutboundParameterTransformer` rozhraní, které lze použít k přizpůsobení tohoto chování globálně nebo použít jiné konvence pro mapování adres URL.

  Další informace najdete v tématu [odkaz na parametr transformer](#parameter-transformer-reference) oddílu.

* Generování odkazů, které jsou používány MVC/Razor Pages s konvenčním trasy chová odlišně při pokusu o odkaz na kontroler nebo akce nebo stránka, která neexistuje.

  Vezměte v úvahu následující výchozí šablonou trasy:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Předpokládejme, že vygenerování odkazu na akci s použitím výchozí šablony s následujícími možnostmi:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  S `IRouter`– směrování na základě, výsledek je vždy `/Blog/ReadPost/17`, i když `BlogController` neexistuje nebo nemá `ReadPost` metody akce. Koncový bod směrování v ASP.NET Core 2.2 nebo vyšší vytvoří podle očekávání, `/Blog/ReadPost/17` pokud existuje metoda akce. *Ale směrování koncový bod vytvoří prázdný řetězec Pokud akce neexistuje.* Koncepčně koncový bod směrování nepředpokládá, že koncový bod existuje, pokud neexistuje, akce.

* Generování odkazů *okolí hodnotu zneplatnění algoritmus* při použití s směrování koncový bod chová odlišně.

  *Zrušení okolí hodnotu* je algoritmus, který určuje, jaké hodnoty trasy z aktuálně prováděné požadavku (okolí hodnoty) lze použít v operace generování odkazů. Konvenční směrování vždy neplatné hodnoty dodatečné trasy, při odkazování na různé akce. Směrování atributů neměli toto chování před verzí 2.2 technologie ASP.NET Core. V dřívějších verzích sady ASP.NET Core odkazy na další akci, použijte stejné názvy parametrů trasy výsledkem chyby generování odkazu. V ASP.NET Core 2.2 nebo vyšší obě formy směrování zneplatnit hodnoty při připojování ke další akci.

  Zvažte následující příklad v ASP.NET Core 2.1 nebo dřívější. Při připojování ke jinou akci (nebo jiné stránky), hodnoty trasy je možné využít v nežádoucí způsoby.

  V */Pages/Store/Product.cshtml*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  V */Pages/Login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  Pokud je identifikátor URI `/Store/Product/18` v ASP.NET Core 2.1 nebo starší, odkaz generovaným na stránce Store/informace podle `@Url.Page("/Login")` je `/Login/18`. `id` Hodnotu 18 je znovu použít, i když cíl odkazu je úplně jiné části aplikace. `id` Trasy hodnotu v kontextu `/Login` stránky je pravděpodobně hodnotu ID uživatele není hodnotou ID úložiště produktu.

  V koncovém bodě směrování ASP.NET Core 2.2 nebo vyšší, výsledkem je `/Login`. Ambientní hodnoty nebudou znovu po odkazovaný cíl jinou akci nebo stránky.

* Syntaxe parametru verzemi trasy: Lomítka nejsou kódování při použití double – hvězdičku (`**`) syntaxe parametr pokrývající vše.

  Při generování odkazů směrování systému zakóduje hodnotu zachycené double hvězdičku (`**`) parametr pokrývající vše (například `{**myparametername}`) s výjimkou lomítka. Hvězdička double pokrývající vše se podporuje s `IRouter`– na základě směrování v ASP.NET Core 2.2 nebo vyšší.

  Parametr pokrývající vše syntaxe jednu hvězdičku v předchozích verzích technologie ASP.NET Core (`{*myparametername}`) je nadále podporován a jsou kódovány lomítka.

  | trasy              | Generuje s použitím odkazu<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (lomítko kódováním)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Příklad middlewaru

V následujícím příkladu se využívá middleware `LinkGenerator` uložení rozhraní API pro vytvoření odkazu na metodu akce, která obsahuje seznam produktů. Pomocí generátoru odkaz vložením do třídy a volání `GenerateLink` je k dispozici pro jakoukoli třídu v aplikaci.

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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Generování adresy URL je proces, podle kterých směrování, můžete vytvořit cestu adresy URL na základě sady hodnot trasy. To umožňuje logické rozdělení mezi obslužné rutiny trasy a adresy URL, které k nim přístup.

Generování adresy URL je iterativní probíhá podobně, ale začíná uživatele nebo architekturní kód volání do <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> metody kolekce tras. Každý *trasy* má jeho `GetVirtualPath` metodu volat v pořadí, dokud nebude nenulovým <xref:Microsoft.AspNetCore.Routing.VirtualPathData> je vrácena.

Primární vstupů na `GetVirtualPath` jsou:

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext*)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*)

Trasy primárně používat hodnoty trasy podle `Values` a `AmbientValues` rozhodnout, jestli je možné generovat adresu URL a jaké hodnoty je třeba zahrnout. `AmbientValues` Sada hodnot trasy, které byly vytvořeny z odpovídajících aktuálního požadavku. Naproti tomu `Values` jsou hodnoty trasy, které určují, jak generovat požadovanou adresu URL pro aktuální operaci. `HttpContext` Je k dispozici v případě, že směrování by si měly opatřit služby nebo další data související s aktuálním kontextu.

> [!TIP]
> Představte si, že [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) jako přepsání pro sadu [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). Generování adresy URL se pokusí znovu použít hodnoty trasy z aktuální žádosti k vygenerování adres URL pro odkazů pomocí stejného postupu nebo hodnoty trasy.

Výstup `GetVirtualPath` je `VirtualPathData`. `VirtualPathData` je paralelní z `RouteData`. `VirtualPathData` obsahuje `VirtualPath` pro výstupní adresy URL a některé další vlastnosti, které by měl nastavit trasy.

[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) obsahuje vlastnost *virtuální cesta* vytvářených trasy. V závislosti na potřebách budete muset cesta další zpracování. Pokud chcete vykreslovat vygenerovaná adresa URL ve formátu HTML, předřaďte základní cesty aplikace.

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) je odkaz na trasy, která adresa URL se úspěšně vygeneroval.

[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) vlastnosti je slovník další data související s trasy, která vygenerovala adresa URL. Toto je rovnoběžky [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

::: moniker-end

### <a name="create-routes"></a>Vytvoření tras

::: moniker range="< aspnetcore-2.2"

Směrování poskytuje <xref:Microsoft.AspNetCore.Routing.Route> třídu jako standardní implementace <xref:Microsoft.AspNetCore.Routing.IRouter>. `Route` používá *šablonu trasy* syntaxe pro definování vzory tak, aby odpovídala cestě adresy URL při <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> je volána. `Route` používá stejnou šablonu trasy pro generování adresy URL při `GetVirtualPath` je volána.

::: moniker-end

Většina aplikací vytvářet trasy voláním <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> nebo některou z podobných rozšiřující metody definované v <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Některé z `IRouteBuilder` rozšiřující metody vytvoření instance <xref:Microsoft.AspNetCore.Routing.Route> a přidejte ho do kolekce tras.

::: moniker range=">= aspnetcore-2.2"

`MapRoute` nepřijímá parametr obslužná rutina trasy. `MapRoute` Přidá trasy, které jsou zpracovávány pouze <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Další informace o směrování v aplikaci MVC najdete v tématu <xref:mvc/controllers/routing>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

`MapRoute` nepřijímá parametr obslužná rutina trasy. `MapRoute` Přidá trasy, které jsou zpracovávány pouze <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Je výchozí obslužnou rutinu `IRouter`, a obslužnou rutinu nemusí zpracovávat žádosti. ASP.NET Core MVC je třeba typicky nakonfigurován jako výchozí popisovač, který zpracovává pouze požadavky, které odpovídají k dispozici kontroleru a akce. Další informace o směrování v aplikaci MVC najdete v tématu <xref:mvc/controllers/routing>.

::: moniker-end

Následující příklad kódu je příkladem `MapRoute` volání používá typické definice trasy ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Tato šablona odpovídá cesty adresy URL a extrahuje hodnoty trasy. Například cesta `/Products/Details/17` následující hodnoty trasy generuje: `{ controller = Products, action = Details, id = 17 }`.

Hodnoty trasy jsou určeny rozdělení cestě adresy URL do segmentů a odpovídající každý segment s *trasy parametr* název v šabloně trasy. Jsou pojmenované parametry trasy. Parametry definované uzavřením názvu parametru ve složených závorkách `{ ... }`.

Předchozí šablona může také odpovídat cesty URL `/` a vytvářet hodnoty `{ controller = Home, action = Index }`. K tomu dochází, `{controller}` a `{action}` trasy parametry mají výchozí hodnoty a `id` trasy parametr je nepovinný. Znaménko rovná se (`=`) následován hodnotou po definuje výchozí hodnotu pro parametr název parametru trasy. Otazník (`?`) po definuje volitelný parametr název parametru trasy.

Parametry s výchozí hodnotou směrování *vždy* vygenerování hodnoty trasy, pokud trasa odpovídá. Volitelné parametry není výsledkem hodnota trasy, pokud se žádný odpovídající segment cesty adresy URL. Najdete v článku [trasy referenčními informacemi k šablonám](#route-template-reference) najdete důkladné popis scénáře šablonu trasy a syntaxe.

V následujícím příkladu, definice parametru trasy `{id:int}` definuje [trasy omezení](#route-constraint-reference) pro `id` směrování parametru:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Tato šablona odpovídá cesty adresy URL jako `/Products/Details/17` , ale ne `/Products/Details/Apples`. Implementace omezení trasy <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a hodnoty trasy k ověření je zkontrolovat. V tomto příkladu je hodnota trasy `id` musí být převeditelný na celé číslo. V tématu [trasy omezení referenční](#route-constraint-reference) vysvětlení poskytovaného rámcem omezení trasy.

Další přetížení `MapRoute` přijmout hodnoty pro `constraints`, `dataTokens`, a `defaults`. Typické použití těchto parametrů je předat anonymně typovaných objektů, kde názvy vlastností anonymních typů shody směrovat názvy parametrů.

Následující `MapRoute` příklady vytvořit ekvivalentní trasy:

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
> Vložená syntaxe pro definování omezení a výchozích hodnot může být vhodné pro jednoduché trasy. Existují ale scénáře, jako jsou tokeny dat, nepodporované vložená syntaxe.

Následující příklad ukazuje několik dalších scénářů:

::: moniker range=">= aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Odpovídá předchozí šablonu cesty adresy URL jako `/Blog/All-About-Routing/Introduction` a extrahuje hodnoty `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Směrování výchozí hodnoty pro `controller` a `action` jsou vytvářeny trasy, i když neexistují žádné odpovídající trasy parametry v šabloně. V šabloně postupu lze zadat výchozí hodnoty. `article` Parametr trasa je definován jako *pokrývající vše* podle vzhledu hvězdičku double (`**`) před název parametru trasy. Parametry trasy pokrývající vše zachycení zbývající část cesty URL a můžete také hledat shody prázdný řetězec.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Odpovídá předchozí šablonu cesty adresy URL jako `/Blog/All-About-Routing/Introduction` a extrahuje hodnoty `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Směrování výchozí hodnoty pro `controller` a `action` jsou vytvářeny trasy, i když neexistují žádné odpovídající trasy parametry v šabloně. V šabloně postupu lze zadat výchozí hodnoty. `article` Parametr trasa je definován jako *pokrývající vše* podle vzhledu hvězdičku (`*`) před název parametru trasy. Parametry trasy pokrývající vše zachycení zbývající část cesty URL a můžete také hledat shody prázdný řetězec.

::: moniker-end

Následující příklad přidá tokeny omezení a data trasy:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Odpovídá předchozí šablonu cesty adresy URL jako `/en-US/Products/5` a extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` a tokeny dat `{ locale = en-US }`.

![Tokeny Windows místních hodnot](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generování adresy URL trasy třídy

`Route` Třídy můžete také provádět generování adresy URL kombinováním sadu hodnot trasy s jeho šablonu trasy. Toto je logicky procesu zpětné odpovídajících cestě adresy URL.

> [!TIP]
> Abyste lépe pochopili generování adresy URL, představte si, jakou adresu URL, které chcete generovat a potom rozmyslete si, jak šablonu trasy odpovídají tuto adresu URL. Hodnoty by bylo vytvořeno? Jedná se o ekvivalent hrubý fungování generování adresy URL `Route` třídy.

Následující příklad používá obecné výchozí trasa ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

S hodnotami trasy `{ controller = Products, action = List }`, adresu URL `/Products/List` je generován. Hodnoty trasy jsou substituovány za parametry odpovídající trasy k vytvoření cesty adresy URL. Protože `id` je volitelný parametr trasa, adresa URL se úspěšně vygeneroval bez hodnoty pro `id`.

S hodnotami trasy `{ controller = Home, action = Index }`, adresu URL `/` je generován. Hodnoty zadané trasy odpovídat výchozím hodnotám a segmentů odpovídající výchozí hodnoty jsou bezpečně vynechány.

Obě adresy URL generované zpátečního převodu s následující definice trasy (`/Home/Index` a `/`) vytvářet stejné hodnoty trasy, které byly použity k vytvoření adresy URL.

> [!NOTE]
> Používejte aplikace pomocí ASP.NET Core MVC <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> k vygenerování adres URL, bez volání do směrování přímo.

Další informace o generování adresy URL, najdete v článku [odkaz generování adresy Url](#url-generation-reference) oddílu.

## <a name="use-routing-middleware"></a>Použití směrování middlewaru

Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) v souboru projektu vaší aplikace.

Přidání směrování do kontejneru služby v `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Musí být nakonfigurované trasy v `Startup.Configure` metody. Ukázková aplikace používá následující rozhraní API:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; Odpovídá pouze požadavky HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

V následující tabulce jsou uvedeny odpovědi s danou identifikátory URI.

| Identifikátor URI                    | Odpověď                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Dobrý den! Hodnoty trasy: [operace, vytvořit], [id, 3] |
| `/package/track/-3`    | Dobrý den! Hodnoty trasy: [operace, sledovat], [id, -3] |
| `/package/track/-3/`   | Dobrý den! Hodnoty trasy: [operace, sledovat], [id, -3] |
| `/package/track/`      | Požadavek rozsahu prostřednictvím žádná shoda.              |
| `GET /hello/Joe`       | Dobrý den, Joe!                                          |
| `POST /hello/Joe`      | Požadavek nepropadla, odpovídá pouze HTTP GET. |
| `GET /hello/Joe/Smith` | Požadavek rozsahu prostřednictvím žádná shoda.              |

::: moniker range="< aspnetcore-2.2"

Pokud konfigurujete jednu trasu, zavolejte <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> předávajícího `IRouter` instance. Nebudete muset použít <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

::: moniker-end

Rozhraní framework obsahuje sadu rozšiřujících metod pro vytváření tras (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* `MapDelete`
* `MapGet`
* `MapMiddlewareDelete`
* `MapMiddlewareGet`
* `MapMiddlewarePost`
* `MapMiddlewarePut`
* `MapMiddlewareRoute`
* `MapMiddlewareVerb`
* `MapPost`
* `MapPut`
* `MapRoute`
* `MapVerb`

::: moniker range="< aspnetcore-2.2"

Některé uvedené metody, jako například `MapGet`, požadovat `RequestDelegate`. `RequestDelegate` Slouží jako *obslužná rutina trasy* při trasa odpovídá. Jiné metody v této rodině povolit konfiguraci kanálu middleware pro použití jako obslužná rutina trasy. Pokud `Map*` metoda nepřijme obslužnou rutinu, jako například `MapRoute`, použije <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.

::: moniker-end

`Map[Verb]` Metody omezení trasy, která má v názvu metody akce HTTP pomocí omezení. Viz například <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> a <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Odkaz na šablonu trasy

Tokeny ve složených závorkách (`{ ... }`) definovat *trasy parametry* , které jsou vázány, pokud je nalezen odpovídající trasy. Můžete definovat více než jeden parametr trasa v segmentu směrování, ale musí být odděleny literálovou hodnotou. Například `{controller=Home}{action=Index}` není platné trasy, protože neexistuje žádná hodnota literálu mezi `{controller}` a `{action}`. Tyto parametry trasy musí mít název a může zadat další atributy.

Prostý text než parametry trasy (například `{id}`) a oddělovač cesty `/` text v adrese URL se musí shodovat. Odpovídající text je velká a malá písmena a podle dekódovaný reprezentace cesty adresy URL. Tak, aby odpovídaly oddělovač parametr literálu trasy (`{` nebo `}`), oddělovač řídicí znak opakováním (`{{` nebo `}}`).

Vzory adres URL, které se pokusí zaznamenat název souboru s příponou volitelné mají další aspekty. Představte si třeba šablona `files/{filename}.{ext?}`. Když hodnoty pro oba `filename` a `ext` existuje, naplní se obě hodnoty. Pokud pouze hodnotu `filename` existuje v adrese URL trasy shody, protože koncové tečky (`.`) je volitelný. Následující adresy URL, která odpovídá této trasy:

* `/files/myFile.txt`
* `/files/myFile`

::: moniker range=">= aspnetcore-2.2"

Můžete použít hvězdičku (`*`) nebo dvojitou hvězdičku (`**`) jako předponu pro parametr trasy, aby vytvořit vazbu na celý URI. Toto nastavení se nazývá *pokrývající vše* parametry. Například `blog/{**slug}` odpovídá libovolný identifikátor URI, který začíná `/blog` a nemá žádné hodnoty, které je přiřazeno k `slug` trasy hodnotu. Parametry catch – to všechno můžete také hledat shody prázdný řetězec.

Parametr pokrývající vše řídicí sekvence příslušných znaků při trasy se používá ke generování adresy URL, včetně oddělovač cesty (`/`) znaků. Například trasy `foo/{*path}` trasy hodnotami `{ path = "my/path" }` generuje `foo/my%2Fpath`. Všimněte si uvozený uvozovacím znakem lomítka. Operace round-trip cesta oddělovače, použijte `**` předpona parametru trasy. Trasa `foo/{**path}` s `{ path = "my/path" }` generuje `foo/my/path`.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Můžete použít hvězdičku (`*`) jako předponu pro parametr trasy, aby vytvořit vazbu na celý URI. Jedná se *pokrývající vše* parametru. Například `blog/{*slug}` odpovídá libovolný identifikátor URI, který začíná `/blog` a nemá žádné hodnoty, které je přiřazeno k `slug` trasy hodnotu. Parametry catch – to všechno můžete také hledat shody prázdný řetězec.

Parametr pokrývající vše řídicí sekvence příslušných znaků při trasy se používá ke generování adresy URL, včetně oddělovač cesty (`/`) znaků. Například trasy `foo/{*path}` trasy hodnotami `{ path = "my/path" }` generuje `foo/my%2Fpath`. Všimněte si uvozený uvozovacím znakem lomítka.

::: moniker-end

Mohou mít parametry trasy *výchozí hodnoty* určený specifikuje výchozí hodnotu za název parametru odděleny symbolem rovná se (`=`). Například `{controller=Home}` definuje `Home` jako výchozí hodnota pro `controller`. Výchozí hodnota je použita, pokud je k dispozici v adrese URL pro parametr žádná hodnota. Parametry trasy, která jsou teď volitelné přidáním otazník (`?`) na konec názvu parametru, jak v `id?`. Rozdíl mezi hodnotami nepovinný a výchozí parametry trasy je, že parametr trasa s výchozí hodnotou vždy vytváří hodnotu&mdash;volitelný parametr má hodnotu pouze v případě, že je žádost o adresu URL zadat hodnotu.

Parametry trasy může mít omezení, která musí odpovídat hodnotě trasy vázán z adresy URL. Přidání dvojtečkou (`:`) a název omezení, určuje název parametru trasy *vložené omezení* na parametru trasy. Pokud omezení vyžaduje argumenty, jsou uzavřeny v závorkách (`(...)`) za názvem omezení. Několik vložených omezení je možné zadat tak připojení jiného dvojtečka (`:`) a název omezení.

Název omezení a argumenty jsou předány <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> služby k vytvoření instance <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> použít při zpracování adresy URL. Například šablona trasy `blog/{article:minlength(10)}` Určuje `minlength` omezení s argumentem `10`. Další informace o omezení trasy a seznam omezení poskytovaného rámcem, najdete v článku [trasy referenční omezení](#route-constraint-reference) části.

::: moniker range=">= aspnetcore-2.2"

Parametry trasy mohou mít i transformátory parametrů, které transformují parametr na hodnotu při generování odkazů a odpovídající akce a stránky pro adresy URL. Jako omezení, parametr transformátory jde přidat vložený parametr trasa přidáním dvojtečkou (`:`) a název transformer za název parametru trasy. Například šablona trasy `blog/{article:slugify}` Určuje `slugify` transformátoru. Další informace o parametru transformátory, najdete v článku [odkaz na parametr transformer](#parameter-transformer-reference) oddílu.

::: moniker-end

Následující tabulka ukazuje příklad šablony trasy a jejich chování.

| Šablona trasy                           | Příklad odpovídající identifikátor URI    | Identifikátor URI požadavku&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Odpovídá jen jednu cestu `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Odpovídá a nastaví `Page` k `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Odpovídá a nastaví `Page` k `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mapuje `Products` kontroleru a `List` akce.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapuje `Products` kontroleru a `Details` akce (`id` hodnotu 123). |
| `{controller=Home}/{action=Index}/{id?`} | `/`                     | Mapuje `Home` kontroleru a `Index` – metoda (`id` ignorováno).        |

Pomocí šablony je obecně nejjednodušším přístupem při směrování. Omezení a výchozí hodnoty lze také zadat mimo šablonu trasy.

> [!TIP]
> Povolit [protokolování](xref:fundamentals/logging/index) zobrazíte jak předdefinovaných implementací, jako například směrování `Route`, shodovat s požadavky.

## <a name="reserved-routing-names"></a>Vyhrazené názvy směrování

Následující klíčová slova jsou vyhrazené názvy a nelze jej použít jako názvy tras nebo parametry:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Odkaz na omezení trasy

Omezení trasy spustit, když shoda vedoucí k adresy URL příchozích a cesta URL je tokenizovaného do hodnoty trasy. Omezení trasy obecně zkontrolovat hodnoty trasy spojený přes šablonu trasy a ujistěte se, hodnota Ano/žádné rozhodnutí o, zda se hodnota je přijatelné. Některá omezení trasy použít data mimo hodnota trasy vzít v úvahu, jestli je možné směrovat požadavek. Například <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> můžete přijmout nebo odmítnout žádost založené na jeho příkaz protokolu HTTP. Omezení se používají v směrování požadavků a propojit generace.

> [!WARNING]
> Nepoužívejte omezení pro **ověřování vstupu**. Pokud omezení se používají pro **ověřování vstupu**, neplatný vstup výsledky v *404 - Nenalezeno* odpovědi namísto *400 - Chybný požadavek* s příslušnou chybovou zprávu. Omezení trasy se používají pro **rozlišení** podobné trasy, není k ověření vstupů pro konkrétní trasy.

Následující tabulka ukazuje příklad omezení trasy a jejich očekávané chování.

| omezení | Příklad | Příklad shody | Poznámky |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Odpovídá jakémukoliv celému číslu |
| `bool` | `{active:bool}` | `true`, `FALSE` | Odpovídá `true` nebo `false` (velká a malá písmena) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Odpovídá platný `DateTime` hodnotu (v invariantní jazykové verzi - zobrazí upozornění) |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Odpovídá platný `decimal` hodnotu (v invariantní jazykové verzi - zobrazí upozornění) |
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Odpovídá platný `double` hodnotu (v invariantní jazykové verzi - zobrazí upozornění) |
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Odpovídá platný `float` hodnotu (v invariantní jazykové verzi - zobrazí upozornění) |
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Odpovídá platný `Guid` hodnota |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Odpovídá platný `long` hodnota |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Řetězec musí mít aspoň 4 znaky |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Řetězec musí obsahovat více než 8 znaků |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Řetězec musí mít délku přesně 12 znaků. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Řetězec musí být aspoň 8 a ne více než 16 znaků |
| `min(value)` | `{age:min(18)}` | `19` | Celočíselná hodnota musí být alespoň 18 |
| `max(value)` | `{age:max(120)}` | `91` | Celočíselná hodnota musí být více než 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Celočíselná hodnota musí být alespoň 18, ale ne více než 120 |
| `alpha` | `{name:alpha}` | `Rick` | Řetězec musí obsahovat minimálně jeden abecední znak (`a`-`z`, velká a malá písmena) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Řetězec musí odpovídat regulárnímu výrazu (viz tipy k definování regulárního výrazu) |
| `required` | `{name:required}` | `Rick` | Používá k vynucení, že parametr-hodnota je k dispozici během generování adresy URL |

Více, oddělit středníkem omezení se můžou uplatnit na jeden parametr. Například následující omezení omezuje parametr na celočíselnou hodnotu 1 nebo vyšší:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Směrovat omezení, která Ověřte adresu URL a jsou převedeny na typ CLR (například `int` nebo `DateTime`) vždy používejte neutrální jazykovou verzi. Tato omezení předpokládá, že adresa URL je nepřekládá. Omezení trasy poskytované rozhraním neupravujte hodnoty uložené v hodnot trasy. Všechny hodnoty trasy, které jsou analyzovány z adresy URL jsou uloženy jako řetězce. Například `float` omezení pokusí převést hodnotu trasy na typ float, ale převedená hodnota se používá jenom k ověření, je možné převést na typ plovoucí.

## <a name="regular-expressions"></a>Regulární výrazy

Přidá rozhraní ASP.NET Core `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` regulárního výrazu konstruktoru. Zobrazit <xref:System.Text.RegularExpressions.RegexOptions> popis těchto členů.

Regulární výrazy pomocí oddělovače a tokeny, které jsou podobné těm, které používají služby Směrování a jazyka C#. Tokeny regulární výraz musí být uvozena. Použít regulární výraz `^\d{3}-\d{2}-\d{4}$` ve směrování, musí mít výraz `\` znaků (jedno zpětné lomítko) zadaná v řetězci jako `\\` (dvojité zpětné lomítko) znaky v C# zdrojového souboru, aby bylo možné escape `\` řídicí znak řetězce (Pokud nepoužíváte [doslovný řetězec literálů](/dotnet/csharp/language-reference/keywords/string)). Řídicí znaky oddělovače směrování parametru (`{`, `}`, `[`, `]`), dvojité znaků ve výrazu (`{{`, `}`, `[[`, `]]`). Následující tabulka uvádí regulárního výrazu a verze uvozený uvozovacím znakem.

| Regulární výraz    | Uvozený uvozovacím znakem regulárního výrazu     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Regulární výrazy použité ve směrování často začínají znak stříšky (`^`) znak a odpovídají počáteční pozice tohoto řetězce. Výrazy často končit znak dolaru (`$`) znaků a shoda konec řetězce. `^` a `$` znaků Ujistěte se, že hodnota parametru celého postupu shoda s regulárním výrazem. Bez `^` a `$` znaků, regulární výraz odpovídat jakýkoli podřetězec v rámci řetězce, který je často nežádoucí. Následující tabulka obsahuje příklady a vysvětluje, proč odpovídat nebo selhání tak, aby odpovídaly.

| Výraz   | String    | Shoda | Komentář               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | Dobrý den     | Ano   | shody podřetězců     |
| `[a-z]{2}`   | 123abc456 | Ano   | shody podřetězců     |
| `[a-z]{2}`   | mz        | Ano   | odpovídá výrazu    |
| `[a-z]{2}`   | MZ        | Ano   | nerozlišuje velikost písmen    |
| `^[a-z]{2}$` | Dobrý den     | Ne    | Zobrazit `^` a `$` výše |
| `^[a-z]{2}$` | 123abc456 | Ne    | Zobrazit `^` a `$` výše |

Další informace o syntaxi regulárního výrazu, naleznete v tématu [regulárních výrazech .NET Frameworku](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Chcete-li omezit parametr se známou sadou možných hodnot, použijte regulární výraz. Například `{action:regex(^(list|get|create)$)}` odpovídá pouze `action` trasy hodnotu `list`, `get`, nebo `create`. Pokud předaná do slovníku omezení řetězec `^(list|get|create)$` je ekvivalentní. Omezení, předané ve slovníku omezení (ne vložené v rámci šablony), které neodpovídají jedno z známé omezení jsou také považovány za regulární výrazy.

::: moniker range=">= aspnetcore-2.2"

## <a name="parameter-transformer-reference"></a>Odkaz na parametr transformer

Parametr transformátory:

* Při generování odkazu pro spuštění `Route`.
* Implementace `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* Byli nakonfigurováni pomocí <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Přijmout hodnoty trasy a transformovat ho do novou řetězcovou hodnotu.
* Výsledkem použití transformovanou hodnotou v vytvořený odkaz.

Například vlastní `slugify` transformer parametr vzoru trasy `blog\{article:slugify}` s `Url.Action(new { article = "MyTestArticle" })` generuje `blog\my-test-article`.

Transformer parametr vzoru trasy, nakonfigurovat ji nejdřív pomocí <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> v `Startup.ConfigureServices`:

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Parametr transformátory se rozhraním, slouží k transformaci identifikátor URI, kde se řeší koncový bod. Například technologie ASP.NET Core MVC používá parametr transformátory Transformace hodnoty trasy slouží k přiřazení `area`, `controller`, `action`, a `page`.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Pomocí předchozího postupu akce `SubscriptionManagementController.GetAll()` je nalezena shoda s identifikátorem URI `/subscription-management/get-all`. Parametr transformer nedojde ke změně hodnoty trasy sloužící ke generování odkazu. Například `Url.Action("GetAll", "SubscriptionManagement")` výstupy `/subscription-management/get-all`.

ASP.NET Core nabízí vytváření rozhraní API pro parametr transformátory pomocí generovaného trasy:

* ASP.NET Core MVC má `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` konvence rozhraní API. Tato konvence zadaný parametr transformer platí pro všechny trasy atributů v aplikaci. Parametr transformer transformuje tokeny atribut trasy, jako se nahradí. Další informace najdete v tématu [transformátoru parametr použít k přizpůsobení náhradních tokenů](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Má Razor Pages `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` konvence rozhraní API. Tato konvence zadaný parametr transformer platí pro všechny automaticky zjistí stránky Razor. Parametr transformer transformuje složku a název segmenty souborů tras pro stránky Razor. Další informace najdete v tématu [transformátoru parametr použít k přizpůsobení stránky trasy](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

::: moniker-end

## <a name="url-generation-reference"></a>Odkaz na generování adresy URL

Následující příklad ukazuje, jak ke generování odkazu pro trasu zadaný slovník hodnot trasy a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

`VirtualPath` Vygeneruje na konci předchozí ukázka je `/package/create/123`. Poskytuje slovníku `operation` a `id` hodnot "Sledovat balíček trasy" šablony trasy `package/{operation}/{id}`. Podrobnosti najdete v tématu ukázkový kód v [použití směrování Middleware](#use-routing-middleware) části nebo [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Druhý parametr `VirtualPathContext` konstruktor je kolekce *okolí hodnoty*. Ambientní hodnoty jsou vhodné použít, protože omezují počet hodnot, které vývojář musí zadat v rámci kontextu požadavku. Aktuální hodnoty trasy z aktuální požadavek jsou považovány za okolí hodnoty pro generování odkazů. V aplikaci ASP.NET Core MVC `About` akce `HomeController`, není nutné zadat hodnotu trasy kontroleru propojení `Index` akce&mdash;okolí hodnotu `Home` se používá.

Ambientní hodnoty, které neodpovídají parametru jsou ignorovány. Okolí hodnoty jsou ignorovány, i když explicitně zadaná hodnota přepíše hodnotu okolí. Odpovídající vyvolá zleva doprava v adrese URL.

Explicitně zadanými hodnotami, ale které se neshodují. segment trasy, která jsou přidány do řetězce dotazu. V následující tabulce jsou uvedeny výsledek při použití šablonu trasy `{controller}/{action}/{id?}`.

| Ambientní hodnoty                     | Explicitní hodnoty                        | Výsledek                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| kontroler = "Domů"                | akce = "O"                       | `/Home/About`           |
| kontroler = "Domů"                | kontroler = "Order", akce = "O" | `/Order/About`          |
| kontroler = "Home", color = "Red" | akce = "O"                       | `/Home/About`           |
| kontroler = "Domů"                | akce = "O", barva = "Red"        | `/Home/About?color=Red` |

Pokud trasa má výchozí hodnotu, která neodpovídá instalovanému parametr a explicitně zadat tuto hodnotu, musí odpovídat výchozí hodnota:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Pokud odpovídající hodnoty pro generování odkazů pouze vytvoří odkaz pro tuto trasu `controller` a `action` jsou k dispozici.

## <a name="complex-segments"></a>Komplexní segmenty

Komplexní segmentech (například `[Route("/x{token}y")]`) jsou zpracovány odpovídající nahoru literály zprava doleva bez metody greedy způsobem. Zobrazit [tento kód](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) podrobné vysvětlení toho, jak jsou porovnány komplexní segmenty. [Vzorový kód](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) se nepoužije, ASP.NET Core, ale poskytuje dobré vysvětlení komplexní segmentů.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->
