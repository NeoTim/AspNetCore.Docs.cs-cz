---
title: Směrování k akcím řadiče v ASP.NET jádra
author: rick-anderson
description: Zjistěte, jak ASP.NET Core MVC používá middlewar směrování k přizpůsobení adres URL příchozích požadavků a jejich mapování na akce.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 9f7a26a482cb115697a0a3d7439c14a062677c92
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277129"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Směrování k akcím řadiče v ASP.NET jádra

[Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET řadiče Core používají middleware směrování [tak,](xref:fundamentals/middleware/index) aby odpovídal adresám URL příchozích požadavků a mapoval je na [akce](#action).  Šablony tras:

* Jsou definovány v spouštěcím kódu nebo atributech.
* Popište, jak jsou cesty adres URL spárovány s [akcemi](#action).
* Slouží ke generování adres URL pro odkazy. Generované odkazy jsou obvykle vráceny v odpovědích.

Akce jsou buď [konvenčně směrované](#cr) nebo [atributsměrované](#ar). Umístění trasy na řadič nebo [akci](#action) způsobí, že je směrována do atributu. Další informace naleznete [v tématu Smíšené směrování.](#routing-mixed-ref-label)

Tento dokument:

* Vysvětluje interakce mezi MVC a směrování:
  * Jak typické aplikace MVC využívají funkce směrování.
  * Zahrnuje obojí:
    * [Konvenčně směrování](#cr) se obvykle používá s řadiči a zobrazeními.
    * *Směrování atributů* používané s rest API. Pokud máte primárně zájem o směrování pro REST API, přejděte na [oddíl Směrování atributů pro rest API.](#ar)
  * Rozšířené podrobnosti o směrování naleznete v [tématu Směrování.](xref:fundamentals/routing)
* Odkazuje na výchozí systém směrování přidaný v ASP.NET core 3.0, nazývané směrování koncových bodů. Pro účely kompatibility je možné používat řadiče s předchozí verzí směrování. Pokyny naleznete v [průvodci migrací 2.2-3.0.](xref:migration/22-to-30) Referenční materiál staršího systému směrování naleznete ve [verzi 2.2 tohoto dokumentu.](xref:mvc/controllers/routing?view=aspnetcore-2.2)

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Nastavit konvenční trasu

`Startup.Configure`obvykle má kód podobný následujícímu při použití [konvenčního směrování](#crd):

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Uvnitř volání <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , se používá k vytvoření jedné trasy. Jedna trasa `default` se nazývá trasa. Většina aplikací s ovladači a zobrazeními `default` používá šablonu trasy podobnou trase. REST API by měl používat [směrování atributů](#ar).

Šablona `"{controller=Home}/{action=Index}/{id?}"`trasy :

* Odpovídá cestě URL, jako je`/Products/Details/5`
* Extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` trasy tokenizací cesty. Extrakce hodnot trasy má za následek shodu, pokud má aplikace pojmenovanou `ProductsController` kontroleru a `Details` akci:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`model váže hodnotu `id = 5` pro `id` nastavení `5`parametru na . Další podrobnosti najdete v tématu [Vazby modelu.](xref:mvc/models/model-binding)
* `{controller=Home}`definuje `Home` jako výchozí `controller`.
* `{action=Index}`definuje `Index` jako výchozí `action`.
*  Znak `?` v `{id?}` definuje `id` jako volitelné.
  * Výchozí a volitelné parametry trasy nemusí být v cestě adresy URL pro shodu k dispozici. Podrobný popis syntaxe šablony trasy najdete v tématu [Odkaz na šablonu](xref:fundamentals/routing#route-template-reference) trasy.
* Odpovídá cestě `/`url .
* Vytvoří hodnoty `{ controller = Home, action = Index }`postupu .

Hodnoty pro `controller` `action` a využít výchozí hodnoty. `id`nevytváří hodnotu, protože v cestě URL není žádný odpovídající segment. `/`pouze odpovídá, pokud `HomeController` existuje `Index` a akce:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Pomocí předchozí definice řadiče a `HomeController.Index` šablony trasy je akce spuštěna pro následující cesty adres URL:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

Cesta URL `/` používá výchozí `Home` řadiče `Index` a akci šablony trasy. Cesta URL `/Home` používá výchozí `Index` akci šablony trasy.

Komfortní metoda <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Nahradí:

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Směrování je konfigurováno pomocí middlewaru <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> a. <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> Použití regulátorů:
>
> * Volání <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` uvnitř mapovat [atribut směrované](#ar) řadiče.
> * Volání <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>nebo , mapovat [konvenčně směrované](#cr) řadiče.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Konvenční směrování

Konvenční směrování se používá s ovladači a zobrazeními. Trasa: `default`

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

je příkladem *konvenčního směrování*. Nazývá se *konvenční směrování,* protože vytváří *konvenci* pro cesty adres URL:

* První segment cesty `{controller=Home}`, mapuje na název řadiče.
* Druhý segment `{action=Index}`, mapuje na název [akce.](#action)
* Třetí segment `{id?}` se používá pro `id`volitelné . In `?` `{id?}` je volitelný. `id`se používá k mapování na entitu modelu.

Pomocí `default` této trasy cesta url:

* `/Products/List`mapy k `ProductsController.List` akci.
* `/Blog/Article/17`mapuje `BlogController.Article` a obvykle model `id` váže parametr na 17.

Toto mapování:

* Je založen pouze na **názvech**řadiče a [akcí](#action) .
* Není založen na oborech názvů, umístění zdrojových souborů nebo parametrech metody.

Použití konvenčního směrování s výchozí trasou umožňuje vytvoření aplikace bez nutnosti přijít s novým vzorem adresy URL pro každou akci. U aplikace s akcemi stylu [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) které mají konzistenci pro adresy URL napříč řadiči:

* Pomáhá zjednodušit kód.
* Díky tomu, že je uI předvídatelnější.

> [!WARNING]
> V `id` předchozím kódu je definován jako volitelné šablony trasy. Akce lze provést bez volitelného ID k dispozici jako součást adresy URL. Obecně platí, že když`id` je vynechán z adresy URL:
>
> * `id`je nastavena na `0` podle vazby modelu.
> * V odpovídající databázi nebyla nalezena žádná entita `id == 0`.
>
> [Směrování atributů](#ar) poskytuje jemně odstupňovaný ovládací prvek, aby id požadované pro některé akce a ne pro ostatní. Podle konvence dokumentace obsahuje volitelné `id` parametry, jako když je pravděpodobné, že se zobrazí ve správném použití.

Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné. Výchozí konvenční `{controller=Home}/{action=Index}/{id?}`trasa :

* Podporuje základní a popisné schéma směrování.
* Je užitečným výchozím bodem pro aplikace založené na uznatého.
* Je jediná šablona trasy potřebná pro mnoho webových aplikací uživatelského uživatelského nastavení. U větších webových aplikací uživatelského uživatelského nastavení, další trasa pomocí [oblasti,](#areas) pokud často vše, co je potřeba.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>a: <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>

* Automaticky přiřaďte hodnotu **objednávky** jejich koncovým bodům na základě pořadí, ve které jsou vyvolány.

Směrování koncového bodu v ASP.NET jádrem 3.0 a novějším:

* Nemá pojem trasy.
* Neposkytuje záruky řazení pro provádění rozšiřitelnosti, všechny koncové body jsou zpracovány najednou.

Povolte [protokolování,](xref:fundamentals/logging/index) abyste viděli, jak integrované <xref:Microsoft.AspNetCore.Routing.Route>implementace směrování, například , shodu jsou požadavky na shodu.

[Směrování atributů](#ar) je vysvětleno dále v tomto dokumentu.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Více konvenčních tras

Více [konvenčních tras](#cr) lze `UseEndpoints` přidat uvnitř <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> přidáním další volání a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>. To umožňuje definování více konvencí nebo přidání konvenčních tras, které jsou vyhrazeny pro konkrétní [akci](#action), například:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

Trasa `blog` v předchozím kódu je **vyhrazená konvenční trasa**. Říká se tomu vyhrazená konvenční trasa, protože:

* Používá [konvenční směrování](#cr).
* Je věnován konkrétní [akci](#action).

Protože `controller` `action` a nezobrazují se v `"blog/{*article}"` šabloně trasy jako parametry:

* Mohou mít pouze výchozí `{ controller = "Blog", action = "Article" }`hodnoty .
* Tato trasa se `BlogController.Article`vždy mapuje na akci .

`/Blog`, `/Blog/Article`a `/Blog/{any-string}` jsou jedinými cestami url, které odpovídají trase blogu.

Předchozí příklad:

* `blog`trasa má vyšší prioritu `default` pro shody než trasa, protože je přidána jako první.
* Je a příkladem směrování stylu [slimáka,](https://developer.mozilla.org/docs/Glossary/Slug) kde je typické mít název článku jako součást adresy URL.

> [!WARNING]
> V ASP.NET jádrem 3.0 a novějším směrování ne:
> * Definujte koncept nazývaný *trasa*. `UseRouting`přidá odpovídající trasu do kanálu middlewaru. Middleware `UseRouting` se podívá na sadu koncových bodů definovaných v aplikaci a vybere nejlepší shodu koncového bodu na základě požadavku.
> * Poskytnout záruky o pořadí provádění rozšiřitelnosti jako <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> nebo <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.
>
>Referenční materiál na směrování naleznete v [tématu Směrování.](xref:fundamentals/routing)

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Konvenční pořadí směrování

Konvenční směrování odpovídá pouze kombinaci akce a kontroleru, které jsou definovány aplikací. Cílem je zjednodušit případy, kdy se konvenční trasy překrývají.
Přidání tras <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>pomocí <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>aplikace <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , a automaticky přiřadit hodnotu objednávky jejich koncovým bodům na základě pořadí, ve které jsou vyvolány. Shody z trasy, která se zobrazí dříve, mají vyšší prioritu. Konvenční směrování je závislé na objednávce. Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti. [Vyhrazené konvenční trasy](#dcr) s catch všechny `{*article}` parametry trasy, jako může trasa příliš [chamtivý](xref:fundamentals/routing#greedy), což znamená, že odpovídá adresy URL, které jste chtěli, aby odpovídaly jiné trasy. Chcete-li zabránit chamtivým shodám, vložte chamtivé trasy později do tabulky tras.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Řešení nejednoznačných akcí

Pokud se dva koncové body shodují prostřednictvím směrování, musí směrování provést jednu z následujících akcí:

* Vyberte si nejlepšího kandidáta.
* Vyvolat výjimku.

Příklad:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Předchozí řadič definuje dvě akce, které odpovídají:

* Cesta url`/Products33/Edit/17`
* Údaje `{ controller = Products33, action = Edit, id = 17 }`o trase .

Toto je typický vzor pro řadiče MVC:

* `Edit(int)`zobrazí formulář pro úpravu produktu.
* `Edit(int, Product)`zpracuje zaúčtovaný formulář.

Postup vyřešení správné trasy:

* `Edit(int, Product)`je vybrána, pokud `POST`je požadavek HTTP .
* `Edit(int)`je vybrána, pokud je [sloveso HTTP](#verb) cokoli jiného. `Edit(int)`se obecně `GET`nazývá přes .

V <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, , je k dispozici pro směrování tak, aby jej můžete zvolit na základě metody HTTP požadavku. Dělá `HttpPostAttribute` `Edit(int, Product)` lepší zápas `Edit(int)`než .

Je důležité pochopit roli atributů, `HttpPostAttribute`jako je . Podobné atributy jsou definovány pro ostatní [http slovesa](#verb). V [běžném směrování](#cr)je běžné, že akce používají stejný název akce, když jsou součástí zobrazovaného formuláře, odešle pracovní postup formuláře. Například najdete [v tématu Prozkoumání dvou metod akce úprav](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Pokud směrování nemůže vybrat nejlepšího <xref:System.Reflection.AmbiguousMatchException> kandidáta, je vyvolána, výpis více odpovídajícíkoncové body.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Konvenční názvy tras

Řetězce `"blog"` a `"default"` v následujících příkladech jsou konvenční názvy tras:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Názvy tras dávají cestě logický název. Pojmenovanou trasu lze použít pro generování adresy URL. Použití pojmenované trasy zjednodušuje vytváření adres URL, pokud by řazení tras mohlo zkomplikovat generování adres URL. Názvy tras musí být jedinečné v široké masivu.

Názvy tras:

* Nemají žádný vliv na párování adres URL nebo zpracování požadavků.
* Používají se pouze pro generování adres URL.

Koncept názvu trasy je v směrování reprezentován jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). Termíny **název trasy** a název **koncového bodu**:

* Jsou zaměnitelné.
* Který z nich se používá v dokumentaci a kód závisí na rozhraní API je popsáno.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Směrování atributů pro rest API

REST API by měl použít směrování atributů k modelování funkce aplikace jako sadu prostředků, kde jsou operace reprezentovány [http slovesa](#verb).

Směrování atributů používá sadu atributů k mapování akcí přímo na směrování šablon. Následující `StartUp.Configure` kód je typický pro rozhraní REST API a používá se v další ukázce:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> je volána uvnitř `UseEndpoints` mapovat atribut směrované řadiče.

V následujícím příkladu:

* Používá se `Configure` předchozí metoda.
* `HomeController`odpovídá sadě adres URL podobných tomu, `{controller=Home}/{action=Index}/{id?}` co odpovídá výchozí konvenční trase.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

Akce `HomeController.Index` je spuštěna pro některou `/` `/Home`z `/Home/Index`cest `/Home/Index/3`url , , nebo .

Tento příklad zvýrazní klíčový programovací rozdíl mezi směrováním atributů a [konvenčním směrováním](#cr). Směrování atributů vyžaduje k určení trasy více vstupů. Konvenční výchozí trasa zpracovává trasy stručněji. Směrování atributů však umožňuje a vyžaduje přesné řízení, které šablony tras se vztahují na každou [akci](#action).

Při směrování atributů nehrají názvy názvů řadičů a akcí **žádnou** roli, ve které je akce spárována. Následující příklad odpovídá stejným adresám URL jako v předchozím příkladu:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Následující kód používá token `action` `controller`nahrazení a :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Pro regulátor platí `[Route("[controller]/[action]")]` následující kód:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

V předchozím kódu musí `Index` šablony metody předřadit `/` nebo `~/` k šablonám postupu. Šablony trasy použité na akci, `/` `~/` která začíná nebo nezkombinují se šablonami tras aplikovanými na kontroler.

Informace o výběru šablony trasy najdete v [tématu Priorita šablony trasy.](xref:fundamentals/routing#rtp)

## <a name="reserved-routing-names"></a>Rezervované názvy směrování

Následující klíčová slova jsou vyhrazené názvy parametrů trasy při použití řadičů nebo stránek Razor:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Použití `page` parametru trasy s postupem atributu je běžnou chybou. To má za následek nekonzistentní a matoucí chování s generováním adresy URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Názvy speciálních parametrů jsou používány generování adresy URL k určení, pokud operace generování adresy URL odkazuje na Razor Page nebo řadič.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Šablony sloves PROTOKOLU HTTP

ASP.NET Core má následující šablony sloves PROTOKOLU HTTP:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [To je v pořádku.](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Šablony tras

ASP.NET Core má následující šablony tras:

* Všechny [šablony sloves PROTOKOLU HTTP](#verb) jsou šablony tras.
* [To je v pořádku.](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Směrování atributů s atributy http slovesa

Zvažte následující ovladač:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

V předchozím kódu:

* Každá akce `[HttpGet]` obsahuje atribut, který omezuje párování pouze požadavků HTTP GET.
* Akce `GetProduct` obsahuje `"{id}"` šablonu, `id` proto je `"api/[controller]"` připojen k šabloně na řadiči. Šablona metod `"api/[controller]/"{id}""`je . Proto tato akce odpovídá pouze GET `/api/test2/xyz`požadavky`/api/test2/123``/api/test2/{any string}`pro formulář , , , atd.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* Akce `GetIntProduct` obsahuje `"int/{id:int}")` šablonu. Část `:int` šablony omezuje hodnoty `id` trasy na řetězce, které lze převést na celé číslo. Požadavek GET `/api/test2/int/abc`na :
  * Neodpovídá téhle akci.
  * Vrátí chybu [404 Not Found.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* Akce `GetInt2Product` obsahuje `{id}` v šabloně, ale neomezuje `id` na hodnoty, které lze převést na celé číslo. Požadavek GET `/api/test2/int2/abc`na :
  * Odpovídá této trase.
  * Vazba modelu `abc` se nepodařilo převést na celé číslo. Parametr `id` metody je celé číslo.
  * Vrátí [400 Chybný požadavek,](https://developer.mozilla.org/docs/Web/HTTP/Status/400) `abc` protože vazba modelu se nepodařilo převést na celé číslo.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Směrování atributů <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> může používat <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>atributy, jako jsou například , a <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>. Všechny atributy [slovesa HTTP](#verb) přijímají šablonu trasy. Následující příklad ukazuje dvě akce, které odpovídají stejné šabloně trasy:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Pomocí cesty `/products3`URL :

* Akce `MyProductsController.ListProducts` se spustí, když `GET`je [sloveso HTTP](#verb) .
* Akce `MyProductsController.CreateProduct` se spustí, když `POST`je [sloveso HTTP](#verb) .

Při vytváření rozhraní REST API, je vzácné, že `[Route(...)]` budete muset použít na metodu akce, protože akce přijímá všechny metody HTTP. Je lepší použít konkrétnější [atribut http sloveso,](#verb) abyste byli přesní o tom, co vaše rozhraní API podporuje. Očekává se, že klienti rozhraní REST API budou vědět, jaké cesty a http slovesa jsou mapována na konkrétní logické operace.

REST API by měl použít směrování atributů k modelování funkce aplikace jako sadu prostředků, kde jsou operace reprezentovány http slovesa. To znamená, že mnoho operací, například GET a POST na stejném logickém prostředku, používá stejnou adresu URL. Směrování atributů poskytuje úroveň řízení, která je potřeba pečlivě navrhnout rozložení veřejného koncového bodu rozhraní API.

Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné nastavit parametry požadované jako součást definice šablony trasy. V následujícím příkladu `id` je požadováno jako součást cesty URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Akce: `Products2ApiController.GetProduct(int)`

* Je spuštěn s adresou URL cestu, jako`/products2/3`
* Není spuštěn s adresou `/products2`URL cestu .

Atribut [[Spotřebovává]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) umožňuje akci omezit typy obsahu podporovaných požadavků. Další informace naleznete [v tématu Definování typů obsahu podporovaných požadavků pomocí atributu Spotřebovává](xref:web-api/index#consumes).

 Úplný popis šablon tras a souvisejících možností naleznete v tématu [Směrování.](xref:fundamentals/routing)

Další informace `[ApiController]`naleznete v [tématu ApiController atribut](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Název trasy

Následující kód definuje název postupu `Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Názvy tras lze použít ke generování adresy URL na základě konkrétní trasy. Názvy tras:

* Nemají žádný vliv na chování přiřazování adres URL směrování.
* Používají se pouze pro generování adres URL.

Názvy tras musí být jedinečné pro celou aplikaci.

Porovnejte předchozí kód s konvenční výchozí `id` trasou,`{id?}`která definuje parametr jako volitelný ( ). Možnost přesně určit api má výhody, jako `/products` `/products/5` je například povolení a odeslání do různých akcí.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Kombinování tras atributů

Chcete-li, aby směrování atributů bylo méně opakované, jsou atributy trasy na řadiči kombinovány s atributy trasy u jednotlivých akcí. Všechny šablony tras definované na řadiči jsou předřazeny k směrování šablon akcí. Umístění atributu trasy na řadič provede **všechny** akce v řadiči pomocí směrování atributu.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

V předchozím příkladu:

* Cesta `/products` URL se může shodovat`ProductsApi.ListProducts`
* Cesta `/products/5` url se `ProductsApi.GetProduct(int)`může shodovat .

Obě tyto akce odpovídají `GET` pouze protokolu HTTP, `[HttpGet]` protože jsou označeny atributem.

Šablony trasy použité na akci, `/` `~/` která začíná nebo nezkombinují se šablonami tras aplikovanými na kontroler. Následující příklad odpovídá sadě cest URL podobné výchozí trase.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

Následující tabulka vysvětluje `[Route]` atributy v předchozím kódu:

| Atribut               | Kombinuje s`[Route("Home")]` | Definuje šablonu trasy. |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Ano | `"Home"` |
| `[Route("Index")]` | Ano | `"Home/Index"` |
| `[Route("/")]` | **Ne** | `""` |
| `[Route("About")]` | Ano | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Pořadí postupu atributu

Směrování vytvoří strom a odpovídá všem koncovým bodům současně:

* Položky trasy se chovají, jako by byly umístěny v ideálním pořadí.
* Nejkonkrétnější trasy mají šanci provést před obecnější trasy.

Například trasa atributu jako `blog/search/{topic}` je konkrétnější `blog/{*article}`než trasa atributu jako . Trasa `blog/search/{topic}` má ve výchozím nastavení vyšší prioritu, protože je konkrétnější. Pomocí [konvenčního směrování](#cr)je vývojář zodpovědný za umístění tras v požadovaném pořadí.

Trasy atributů mohou konfigurovat <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> objednávku pomocí vlastnosti. Všechny atributy trasy za předpokladu, [patří](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) `Order` . Trasy jsou zpracovávány podle vzestupného druhu `Order` vlastnosti. Výchozí pořadí `0`je . Nastavení trasy `Order = -1` pomocí spuštění před trasami, které nenastavují pořadí. Nastavení trasy `Order = 1` pomocí spuštění po výchozím pořadí trasy.

**Vzávislosti** `Order`na . Pokud místo url aplikace vyžaduje explicitní hodnoty objednávek pro správné směrování, je pravděpodobně matoucí i pro klienty. Obecně platí, že směrování atributů vybere správnou trasu s párováním adres URL. Pokud výchozí pořadí použité pro generování adresy URL nefunguje, použití názvu trasy jako `Order` přepsání je obvykle jednodušší než použití vlastnosti.

Zvažte následující dva řadiče, které `/home`oba definují párování trasy :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Požadavek `/home` s předchozím kódem vyvolá výjimku podobnou následující:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Přidání `Order` majek do jednoho z atributů trasy vyřeší nejednoznačnost:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

S předchozím kódem `/home` spustí `HomeController.Index` koncový bod. Chcete-li `MyDemoController.MyIndex`se `/home/MyIndex`dostat do , žádost . **Poznámka**:

* Předchozí kód je příklad nebo špatný návrh směrování. To bylo použito `Order` k ilustraci majetku.
* Vlastnost `Order` pouze řeší nejednoznačnost, že šablona nemůže být uzavřeno. Bylo by lepší odstranit `[Route("Home")]` šablonu.

Viz [Konvence tras a aplikací Razor Pages: Pořadí tras](xref:razor-pages/razor-pages-conventions#route-order) pro informace o pořadí tras pomocí Razor Pages.

V některých případech je vrácena chyba HTTP 500 s nejednoznačnými cestami. Pomocí [protokolování](xref:fundamentals/logging/index) můžete zjistit, `AmbiguousMatchException`které koncové body způsobily .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Nahrazení tokenu v šablonách tras [kontroler], [akce], [oblast]

Pro větší pohodlí, atribut trasy podporu token nahrazení parametry vyhrazené trasy ohraničující token uzavřením tokenu v jedné z následujících:

* Čtvercové závorky:`[]`
* Složené závorky:`{}`

Tokeny `[action]`, `[area]`a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu řadiče z akce, kde je trasa definována:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

V předchozím kódu:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Odpovídá`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Odpovídá`/Products0/Edit/{id}`

Nahrazení tokenu dochází jako poslední krok vytváření trasy atributu. Předchozí příklad se chová stejně jako následující kód:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Trasy atributů lze také kombinovat s dědičností. To je silný v kombinaci s token nahrazení. Nahrazení tokenu platí také pro názvy tras definované postupy atributů.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje jedinečný název trasy pro každou akci:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Nahrazení tokenu platí také pro názvy tras definované postupy atributů.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
generuje jedinečný název trasy pro každou akci.

Chcete-li odpovídat oddělovač `[` `]`nahrazení literálu tokenu`[[` `]]`nebo , uniknout opakováním znaku ( nebo ).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Přizpůsobení nahrazení tokenu pomocí transformátoru parametrů

Token nahrazení lze přizpůsobit pomocí parametrtransformátoru. Parametr transformátor <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> implementuje a transformuje hodnotu parametrů. Například vlastní `SlugifyParameterTransformer` parametr transformátor `SubscriptionManagement` změní `subscription-management`hodnotu trasy na :

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

Jedná <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> se o konvence modelu aplikace, která:

* Aplikuje parametr transformátor u všech tras atributů v aplikaci.
* Přizpůsobí hodnoty tokenu postupu atributu při jejich nahrazení.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Předchozí `ListAll` metoda odpovídá `/subscription-management/list-all`.

Je `RouteTokenTransformerConvention` registrován jako možnost `ConfigureServices`v .

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Viz [MDN webové dokumenty na Slimák](https://developer.mozilla.org/docs/Glossary/Slug) pro definici Slimák.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Více tras atributů

Směrování atributů podporuje definování více tras, které dosáhnou stejné akce. Nejběžnější použití tohoto je napodobovat chování výchozí konvenční trasy, jak je znázorněno v následujícím příkladu:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Umístění více atributů trasy na kontroleru znamená, že každý z nich kombinuje s každým z atributů trasy na metody akce:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Implementují všechna omezení `IActionConstraint`trasy [slovesa HTTP](#verb) .

Při více atributy <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> trasy, které implementují jsou umístěny na akci:

* Každé omezení akce se kombinuje se šablonou trasy použitou na řadič.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Použití více tras na akce se může zdát užitečné a výkonné, je lepší zachovat adresu URL aplikace základní a dobře definované. Více tras na akce **používejte pouze** v případě potřeby, například pro podporu stávajících klientů.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Určení volitelných parametrů, výchozích hodnot a omezení postupu atributu

Trasy atributů podporují stejnou vložkovou syntaxi jako konvenční trasy pro určení volitelných parametrů, výchozích hodnot a omezení.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

V předchozím kódu `[HttpPost("product/{id:int}")]` použije omezení postupu. Akce `ProductsController.ShowProduct` je porovnána pouze podle `/product/3`adres URL, jako je . Část `{id:int}` šablony trasy omezuje tento segment pouze na celá čísla.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Podrobný popis syntaxe šablony trasy najdete v tématu [Odkaz na šablonu](xref:fundamentals/routing#route-template-reference) trasy.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Vlastní atributy postupu pomocí iRouteTemplateProvider

Všechny [atributy trasy](#rt) <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>implementovat . ASP.NET Core runtime:

* Vyhledá atributy na třídy kontroleru a metody akce při spuštění aplikace.
* Používá atributy, `IRouteTemplateProvider` které implementují k sestavení počáteční sady tras.

Implementovat `IRouteTemplateProvider` definovat vlastní atributy trasy. Každý `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou trasy, pořadím a názvem:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Předchozí `Get` metoda vrátí `Order = 2, Template = api/MyTestApi`.

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Přizpůsobení tras atributů pomocí modelu aplikace

Aplikační model:

* Je objektový model vytvořen při spuštění.
* Obsahuje všechna metadata používaná ASP.NET Core k směrování a provádění akcí v aplikaci.

Aplikační model obsahuje všechna data shromážděná z atributů postupu. Data z atributů postupu jsou `IRouteTemplateProvider` poskytována implementací. Úmluv:

* Lze zapsat upravit model aplikace přizpůsobit, jak se chová směrování.
* Jsou čteny při spuštění aplikace.

Tato část ukazuje základní příklad přizpůsobení směrování pomocí aplikačního modelu. Následující kód umožňuje trasy zhruba zaokřovány se strukturou složek projektu.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Následující kód zabraňuje `namespace` použití konvence pro řadiče, které jsou směrovány atribut:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Například následující řadič nepoužívá `NamespaceRoutingConvention`:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

Metoda: `NamespaceRoutingConvention.Apply`

* Pokud je atribut směrován, neprovede nic.
* Nastaví šablonu řadičů `namespace`na základě `namespace` , přičemž základna je odebrána.

Lze `NamespaceRoutingConvention` použít v `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Zvažte například následující řadič:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

V předchozím kódu:

* Základna `namespace` `My.Application`je .
* Úplný název předchozího řadiče `My.Application.Admin.Controllers.UsersController`je .
* Nastaví `NamespaceRoutingConvention` šablonu řadičů na `Admin/Controllers/Users/[action]/{id?`.

Lze `NamespaceRoutingConvention` také použít jako atribut na řadiči:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Smíšené směrování: Směrování atributů vs konvenční směrování

ASP.NET aplikace Core mohou kombinovat použití konvenčního směrování a směrování atributů. Je typické používat konvenční trasy pro řadiče obsluhující stránky HTML pro prohlížeče a směrování atributů pro řadiče obsluhující rest API.

Akce jsou buď konvenčně směrovány nebo atribut směrovány. Umístění trasy na řadič nebo akce způsobí, že atribut směrovány. Akce, které definují trasy atributů nelze dosáhnout prostřednictvím konvenční trasy a naopak. **Libovolný** atribut trasy na řadiči provede **všechny** akce v atributu kontroleru směrovány.

Směrování atributů a konvenční směrování používají stejný směrovací modul.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Generování adres URL a okolní hodnoty

Aplikace můžou pomocí funkcí generování adres URL směrování generovat odkazy na adresy URL na akce. Generování adres URL eliminuje pevné kódovací adresy URL, takže kód je robustnější a udržovatelný. Tato část se zaměřuje na funkce generování adres URL poskytované MVC a pouze pokrývají základy fungování generování adres URL. Podrobný popis generování adres URL naleznete v tématu [Směrování.](xref:fundamentals/routing)

Rozhraní <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> je základním prvkem infrastruktury mezi MVC a směrování pro generování adres URL. Instance je `IUrlHelper` k dispozici `Url` prostřednictvím vlastnosti v kontrolecích, zobrazeních a součástech zobrazení.

V následujícím příkladu `IUrlHelper` rozhraní se `Controller.Url` používá prostřednictvím vlastnosti ke generování adresy URL na jinou akci.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Pokud aplikace používá výchozí konvenční trasu, `url` hodnota proměnné je `/UrlGeneration/Destination`řetězec cesty URL . Tato cesta URL je vytvořena směrováním kombinací:

* Hodnoty trasy z aktuálního požadavku, které se nazývají **hodnoty okolí**.
* Hodnoty předané `Url.Action` a nahrazující tyto hodnoty do šablony postupu:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Každý parametr trasy v šabloně trasy má svou hodnotu nahrazenou sladěnými názvy s hodnotami a hodnotami okolí. Parametr trasy, který nemá hodnotu, může:

* Výchozí hodnotu použijte, pokud ji obsahuje.
* Pokud je to volitelné, nechte se přeskočit. Například šablona `id` z `{controller}/{action}/{id?}`trasy .

Generování adresy URL se nezdaří, pokud žádný parametr požadované trasy nemá odpovídající hodnotu. Pokud se generování adresy URL nezdaří pro trasu, další trasa je vyzkoušena, dokud nebyly vyzkoušeny všechny trasy nebo nalezena shoda.

Předchozí příklad `Url.Action` předpokládá konvenční [směrování](#cr). Generování adres URL funguje podobně s [směrováním atributů](#ar), i když koncepty se liší. S konvenčním směrováním:

* Hodnoty trasy se používají k rozbalení šablony.
* Hodnoty trasy `controller` pro `action` a obvykle se zobrazí v této šabloně. To funguje, protože adresy URL odpovídající směrování dodržovat konvence.

Následující příklad používá směrování atributů:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

Akce `Source` v předchozím kódu generuje `custom/url/to/destination`.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>byla přidána v ASP.NET Core 3.0 jako alternativu k `IUrlHelper`. `LinkGenerator`nabízí podobné, ale flexibilnější funkce. Každá metoda `IUrlHelper` má odpovídající rodinu `LinkGenerator` metod na stejně.

### <a name="generating-urls-by-action-name"></a>Generování adres URL podle názvu akce

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)a všechna související přetížení jsou navrženy tak, aby generovaly cílový koncový bod zadáním názvu řadiče a názvu akce.

Při `Url.Action`použití jsou aktuální `controller` hodnoty `action` trasy pro runtime a jsou poskytovány:

* Hodnota `controller` a `action` jsou součástí [hodnot okolí](#ambient) i hodnot. Metoda `Url.Action` vždy používá aktuální `action` hodnoty `controller` a generuje cestu URL, která vede k aktuální akci.

Směrování se pokusí použít hodnoty v okolních hodnotách k vyplnění informací, které nebyly poskytnuty při generování adresy URL. Zvažte trasu `{a}/{b}/{c}/{d}` jako `{ a = Alice, b = Bob, c = Carol, d = David }`s okolními hodnotami :

* Směrování má dostatek informací pro generování adresy URL bez dalších hodnot.
* Směrování má dostatek informací, protože všechny parametry trasy mají hodnotu.

Pokud je `{ d = Donovan }` přidaná hodnota:

* Hodnota `{ d = David }` je ignorována.
* Vygenerovaná `Alice/Bob/Carol/Donovan`cesta URL je .

**Upozornění**: Cesty adres URL jsou hierarchické. V předchozím příkladu, pokud `{ c = Cheryl }` je přidána hodnota:

* Obě hodnoty `{ c = Carol, d = David }` jsou ignorovány.
* Již neexistuje hodnota pro `d` a generování adresy URL se nezdaří.
* Požadované hodnoty `c` a `d` musí být zadány pro generování adresy URL.  

Můžete očekávat, že tento problém `{controller}/{action}/{id?}`s výchozí trasou . Tento problém je v `Url.Action` praxi vzácný, `controller` `action` protože vždy explicitně určuje hodnotu a.

Několik přetížení [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) trvat objekt hodnoty trasy poskytnout hodnoty `controller` pro `action`parametry trasy než a . Objekt hodnot trasy se často `id`používá s . Například, `Url.Action("Buy", "Products", new { id = 17 })`. Objekt hodnot trasy:

* Podle konvence je obvykle objekt anonymního typu.
* Může být `IDictionary<>` nebo [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Všechny další hodnoty trasy, které neodpovídají parametrům trasy, jsou vloženy do řetězce dotazu.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Předchozí kód generuje `/Products/Buy/17?color=red`.

Následující kód generuje absolutní adresu URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Chcete-li vytvořit absolutní adresu URL, použijte jednu z následujících možností:

* Přetížení, který přijímá `protocol`. Například předchozí kód.
* [LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), který ve výchozím nastavení generuje absolutní identifikátory URI.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Generovat adresy URL podle trasy

Předchozí kód demonstroval generování adresy URL předáním v řadiči a název akce. `IUrlHelper`poskytuje také [url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) rodina metod. Tyto metody jsou podobné [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nekopírují aktuální hodnoty `action` a `controller` hodnoty postupu. Nejběžnější použití `Url.RouteUrl`:

* Určuje název trasy pro generování adresy URL.
* Obecně neurčuje název řadiče nebo akce.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Následující soubor Razor generuje odkaz HTML `Destination_Route`na :

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Generování adres URL v HTML a Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>poskytuje <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) a [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) pro generování `<form>` a `<a>` prvky v uvedeném pořadí. Tyto metody používají metodu [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) ke generování adresy URL a přijímají podobné argumenty. `Url.RouteUrl` Společníci `HtmlHelper` `Html.BeginRouteForm` pro `Html.RouteLink` jsou a které mají podobné funkce.

TagHelpers generovat adresy URL `form` prostřednictvím TagHelper a `<a>` TagHelper. Obě tyto `IUrlHelper` použití pro jejich realizaci. Další informace naleznete [v tématu Tag Helpers ve formulářích.](xref:mvc/views/working-with-forms)

Vnitřní zobrazení, `IUrlHelper` je k `Url` dispozici prostřednictvím vlastnosti pro všechny ad-hoc URL generace, které nejsou zahrnuty výše.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Generování adresy URL ve výsledcích akce

Předchozí příklady ukázaly `IUrlHelper` použití v kontroleru. Nejběžnější použití v řadiči je generovat adresu URL jako součást výsledku akce.

A <xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy poskytují komfortní metody pro výsledky akce, které odkazují na jinou akci. Jedním z typických použití je přesměrování po přijetí vstupu uživatele:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Akce výsledky factory metody, jako <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> je například a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> postupujte podobný vzor metody na `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Zvláštní případ pro vyhrazené konvenční trasy

[Konvenční trasa](#cr) může používat speciální druh definice trasy nazývanou [vyhrazená konvenční trasa](#dcr). V následujícím příkladu je `blog` trasa s názvem vyhrazená konvenční trasa:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Pomocí předchozích definic tras `Url.Action("Index", "Home")` vygeneruje `/` cestu `default` URL pomocí trasy, ale proč? Můžete uhodnout, `{ controller = Home, action = Index }` že hodnoty trasy by `blog`stačily ke generování `/blog?action=Index&controller=Home`adresy URL pomocí a výsledkem by bylo .

[Vyhrazené konvenční trasy](#dcr) spoléhají na zvláštní chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla příliš hladová s [generováním](xref:fundamentals/routing#greedy) adresy URL. V tomto případě jsou `{ controller = Blog, action = Article }`výchozí hodnoty `controller` `action` a jako parametr trasy se nezobrazují ani se nezobrazují. Při směrování provádí generování adresy URL, zadaná hodnota musí odpovídat výchozí hodnoty. Generování adresy `blog` URL pomocí `{ controller = Home, action = Index }` selže, `{ controller = Blog, action = Article }`protože hodnoty se neshodují . Směrování pak přejde `default`zpět vyzkoušet , který je úspěšný.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Oblasti

[Oblasti](xref:mvc/controllers/areas) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatná:

* Směrování oboru názvů pro akce řadiče.
* Struktura složek pro zobrazení.

Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé oblasti. Použití oblastí vytvoří hierarchii pro účely směrování přidáním `controller` `action`dalšího parametru trasy `area` do a . Tato část popisuje, jak směrování spolupracuje s oblastmi. Podrobnosti o tom, jak se oblasti používají s pohledy, najdete v článku [Oblasti.](xref:mvc/controllers/areas)

Následující příklad konfiguruje mvc pro `area` použití výchozí `area` `Blog`konvenční trasy a trasu pro pojmenované :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> je volána `"blog_route"`k vytvoření . Druhý parametr `"Blog"`, je název oblasti.

Při porovnávání cesty `/Manage/Users/AddUser`URL `"blog_route"` , jako je `{ area = Blog, controller = Users, action = AddUser }`trasa, vygeneruje hodnoty trasy . Hodnota `area` postupu je vytvořena výchozí `area`hodnotou pro aplikace . Trasa vytvořená pomocí `MapAreaControllerRoute` je ekvivalentní následujícímu:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute`vytvoří trasu pomocí výchozí hodnoty `area` i omezení pro použití zadaný `Blog`název oblasti, v tomto případě . Výchozí hodnota zajišťuje, že trasa `{ area = Blog, ... }`vždy vytváří , `{ area = Blog, ... }` omezení vyžaduje hodnotu pro generování adresy URL.

Konvenční směrování je závislé na objednávce. Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.

V předchozím příkladu odpovídají `{ area = Blog, controller = Users, action = AddUser }` hodnoty trasy následující akci:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Atribut [[Oblast]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) je to, co označuje řadič jako součást oblasti. Tento ovladač je `Blog` v oblasti. Řadiče bez `[Area]` atributu nejsou členy žádné oblasti a `area` **neshodují** se, pokud je hodnota trasy poskytnuta směrováním. V následujícím příkladu může hodnoty `{ area = Blog, controller = Users, action = AddUser }`trasy odpovídat pouze prvnímu uvedenému řadiči .

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Obor názvů každého řadiče je zde zobrazen pro úplnost. Pokud předchozí řadiče používá stejný obor názvů, bude generována chyba kompilátoru. Obory názvů tříd nemají žádný vliv na směrování MVC.

První dva řadiče jsou členy oblastí a shodují se pouze v `area` případě, že jejich název oblasti je zajišťován hodnotou trasy. Třetí řadič není členem žádné oblasti a může se shodovat pouze v případě, že směrováním neposkytuje žádnou hodnotu pro. `area`

<a name="aa"></a>

Pokud jde o odpovídající *žádnou hodnotu*, absence `area` hodnoty `area` je stejná, jako kdyby hodnota pro byly null nebo prázdný řetězec.

Při provádění akce uvnitř oblasti je hodnota `area` trasy pro k dispozici jako [okolní hodnota](#ambient) pro směrování, která se má použít pro generování adresy URL. To znamená, že ve výchozím nastavení oblasti jednat *sticky* pro generování adresy URL, jak ukazuje následující ukázka.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Následující kód generuje adresu `/Zebra/Users/AddUser`URL na adresu :

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Definice akce

Veřejné metody na řadiči, s výjimkou těch s [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) atribut, jsou akce.

## <a name="sample-code"></a>Ukázka kódu

 * Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) je součástí [ukázkového stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) a slouží k zobrazení informací o směrování.
* [Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) [(jak stáhnout)](xref:index#how-to-download-a-sample)

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC používá middleware směrování [tak,](xref:fundamentals/middleware/index) aby odpovídal adresám URL příchozích požadavků a mapoval je na akce. Trasy jsou definovány v kódu spuštění nebo atributy. Trasy popisují, jak by měly být cesty adres URL přizpůsobeny akcím. Trasy se také používají ke generování adres URL (pro odkazy) rozesílaných v odpovědích.

Akce jsou buď konvenčně směrovány nebo atribut směrovány. Umístění trasy na řadič nebo akce způsobí, že atribut směrovány. Další informace naleznete [v tématu Smíšené směrování.](#routing-mixed-ref-label)

Tento dokument vysvětlí interakce mezi MVC a směrováním a způsob, jakým typické aplikace MVC využívají funkce směrování. Podrobnosti o rozšířeném směrování naleznete v [tématu Směrování.](xref:fundamentals/routing)

## <a name="setting-up-routing-middleware"></a>Nastavení middlewaru směrování

V *metodě Configure* se může zobrazit kód podobný:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Uvnitř volání `UseMvc`do `MapRoute` , se používá k vytvoření jedné trasy, `default` kterou budeme označovat jako trasu. Většina aplikací MVC bude používat trasu `default` se šablonou podobnou trase.

Šablona `"{controller=Home}/{action=Index}/{id?}"` trasy může odpovídat `/Products/Details/5` cestě URL jako `{ controller = Products, action = Details, id = 5 }` a extrahuje hodnoty trasy tokenizací cesty. MVC se pokusí vyhledat `ProductsController` řadič s `Details`názvem a spustit akci :

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Všimněte si, že v tomto příkladu by vazba modelu použít hodnotu `id = 5` k nastavení parametru `id` `5` při vyvolání této akce. Další podrobnosti najdete v [části Vazby modelu.](../models/model-binding.md)

Použití `default` trasy:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Šablona trasy:

* `{controller=Home}`definuje `Home` jako výchozí`controller`

* `{action=Index}`definuje `Index` jako výchozí`action`

* `{id?}`definuje `id` jako volitelné

Výchozí a volitelné parametry trasy nemusí být v cestě adresy URL pro shodu k dispozici. Podrobný popis syntaxe šablony trasy najdete v tématu [Odkaz na šablonu](xref:fundamentals/routing#route-template-reference) trasy.

`"{controller=Home}/{action=Index}/{id?}"`může odpovídat `/` cestě URL a `{ controller = Home, action = Index }`vytvoří hodnoty trasy . Hodnoty pro `controller` `action` výchozí hodnoty a jejich `id` využití nevytvářejí hodnotu, protože v cestě url není žádný odpovídající segment. MVC by použít tyto hodnoty `HomeController` `Index` trasy k výběru a akce:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Pomocí této definice řadiče `HomeController.Index` a šablony trasy by byla akce provedena pro některou z následujících cest adresy URL:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Komfortní metoda `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Lze použít k nahrazení:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`a `UseMvcWithDefaultRoute` přidejte `RouterMiddleware` instanci kanálu middleware. MVC nespolupracuje přímo s middleware a používá směrování pro zpracování požadavků. MVC je připojen k trase `MvcRouteHandler`prostřednictvím instance . Kód uvnitř `UseMvc` je podobný následujícímu:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`nedefinuje přímo žádné trasy, přidá zástupný symbol do kolekce `attribute` tras pro trasu. Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a také podporuje směrování atributů.  `UseMvc`a všechny jeho varianty přidávají zástupný symbol pro trasu atributu – `UseMvc`směrování atributů je vždy k dispozici bez ohledu na to, jak nakonfigurujete . `UseMvcWithDefaultRoute`definuje výchozí trasu a podporuje směrování atributů. Oddíl [Směrování atributů](#attribute-routing-ref-label) obsahuje další podrobnosti o směrování atributů.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Konvenční směrování

Trasa: `default`

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Předchozí kód je příkladem konvenčního směrování. Tento styl se nazývá konvenční směrování, protože vytváří *konvenci* pro cesty adres URL:

* První segment cesty se mapuje na název řadiče.
* Druhý mapuje na název akce.
* Třetí segment se používá `id`pro volitelné . `id`mapuje na entitu modelu.

Pomocí `default` této trasy se `/Products/List` cesta `ProductsController.List` url mapuje na akci a `/Blog/Article/17` mapuje na `BlogController.Article`. Toto mapování je založeno **pouze** na názvech řadiče a akcí a není založeno na oborech názvů, umístěních zdrojových souborů nebo parametrech metody.

> [!TIP]
> Použití konvenčního směrování s výchozí trasou umožňuje rychle sestavit aplikaci, aniž byste museli přijít s novým vzorem adresy URL pro každou akci, kterou definujete. U aplikace s akcemi stylu CRUD může konzistence adres URL napříč ovladači zjednodušit váš kód a učinit vaše ui předvídatelnější.

> [!WARNING]
> Šablona `id` trasy je definována jako volitelná, což znamená, že vaše akce lze provést bez ID poskytnutého jako součást adresy URL. Obvykle co se `id` stane, pokud je vynechán z adresy `0` URL je, že bude nastavena na model `id == 0`vazby a v důsledku toho žádná entita bude nalezena v databázi odpovídající . Směrování atributů vám může poskytnout jemně odstupňovanou kontrolu, aby id bylo vyžadováno pro některé akce a ne pro jiné. Podle konvence dokumentace bude obsahovat `id` volitelné parametry, jako když je pravděpodobné, že se zobrazí ve správném použití.

## <a name="multiple-routes"></a>Více tras

Do ní můžete přidat `UseMvc` více tras `MapRoute`přidáním dalších volání do . To umožňuje definovat více konvencí nebo přidat konvenční trasy, které jsou vyhrazeny pro konkrétní akci, například:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Trasa `blog` je zde *vyhrazená konvenční trasa*, což znamená, že používá konvenční směrovací systém, ale je určena pro konkrétní akci. Vzhledem k tomu, `controller` že se v šabloně trasy nezobrazují a `action` nezobrazují se jako parametry, mohou mít pouze výchozí hodnoty, a proto bude tato trasa vždy mapována na akci `BlogController.Article`.

Trasy v kolekci tras jsou objednány a budou zpracovány v pořadí, v jakém jsou přidány. Takže v tomto `blog` příkladu bude trasa vyzkoušena před trasou. `default`

> [!NOTE]
> *Vyhrazené konvenční trasy* často používají parametry `{*article}` trasy **catch-all,** jako je zachycení zbývající části cesty URL. To může způsobit, že trasa bude příliš chamtivá, což znamená, že odpovídá adresám URL, které jste zamýšleli, aby byly spárovány s jinými trasami. Chcete-li tento problém vyřešit, vložte "chamtivé" trasy později do tabulky tras.

### <a name="fallback"></a>Záložní volba

V rámci zpracování požadavků MVC ověří, že hodnoty postupu lze použít k nalezení řadiče a akce ve vaší aplikaci. Pokud hodnoty trasy neodpovídají akci, nebude trasa považována za shodu a další trasa bude vyzkoušena. Tento postup se nazývá *záložní*a má zjednodušit případy, kdy se konvenční trasy překrývají.

### <a name="disambiguating-actions"></a>Nejednoznačná opatření

Když dvě akce odpovídají prostřednictvím směrování, MVC musí disambiguate vybrat 'nejlepší' kandidáta nebo jinak vyvolat výjimku. Příklad:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Tento řadič definuje dvě akce, které `/Products/Edit/17` by `{ controller = Products, action = Edit, id = 17 }`odpovídaly cestě url a datům trasy . Toto je typický vzor pro řadiče MVC, kde `Edit(int)` `Edit(int, Product)` se zobrazuje formulář pro úpravu produktu a zpracovává zaúčtovaný formulář. Chcete-li to možné MVC `Edit(int, Product)` bude muset zvolit, kdy je požadavek HTTP `POST` a `Edit(int)` když http sloveso je něco jiného.

`HttpPostAttribute` ( `[HttpPost]` ) je `IActionConstraint` implementace, která umožní pouze akci, která má `POST`být vybrána, pokud je sloveso HTTP . Přítomnost `IActionConstraint` dělá `Edit(int, Product)` 'lepší' zápas než `Edit(int)`, `Edit(int, Product)` takže bude souzen jako první.

Budete muset psát pouze `IActionConstraint` vlastní implementace ve specializovaných scénářích, ale je důležité pochopit `HttpPostAttribute` roli atributů, jako je - podobné atributy jsou definovány pro ostatní slovesa HTTP. V běžném směrování je běžné, že akce používají stejný název `show form -> submit form` akce, když jsou součástí pracovního postupu. Pohodlí tohoto vzoru bude více zřejmé po kontrole [principiu IActionConstraint](#understanding-iactionconstraint) části.

Pokud se více tras shoduje a MVC nemůže najít "nejlepší" `AmbiguousActionException`trasu, vyvolá .

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Názvy tras

Řetězce `"blog"` a `"default"` v následujících příkladech jsou názvy tras:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Názvy tras dávají trase logický název, takže pojmenovanou trasu lze použít pro generování adresy URL. To značně zjednodušuje vytváření adres URL, když by řazení tras mohlo zkomplikovat generování adres URL. Názvy tras musí být jedinečné pro celou aplikaci.

Názvy tras nemají žádný vliv na párování adres URL nebo zpracování požadavků. používají se pouze pro generování adres URL. [Směrování](xref:fundamentals/routing) má podrobnější informace o generování adres URL, včetně generování adres URL v pomocných souběhem specifických pro MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Směrování atributů

Směrování atributů používá sadu atributů k mapování akcí přímo na směrování šablon. V následujícím příkladu se `app.UseMvc();` `Configure` používá v metodě a není předán žádný postup. Bude `HomeController` odpovídat sadě adres URL podobných tomu, co by odpovídalo výchozí trase: `{controller=Home}/{action=Index}/{id?}`

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

Akce `HomeController.Index()` bude provedena pro libovolnou cestu `/`url `/Home`, `/Home/Index`nebo .

> [!NOTE]
> Tento příklad zvýrazní klíčový programovací rozdíl mezi směrováním atributů a konvenčním směrováním. Směrování atributů vyžaduje více vstupů k určení trasy; konvenční výchozí trasa zpracovává trasy stručněji. Směrování atributů však umožňuje (a vyžaduje) přesné řízení, které šablony tras se vztahují na každou akci.

Při směrování atributů nehrají názvy názvů řadičů a akcí **žádnou** roli ve vybrané akci. Tento příklad bude odpovídat stejným adresám URL jako v předchozím příkladu.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Výše uvedené šablony tras nedefinují `action`parametry trasy pro , `area`, a `controller`. Ve skutečnosti tyto parametry trasy nejsou povoleny v atributových trasách. Vzhledem k tomu, že šablona trasy je již přidružena k akci, nemá smysl analyzovat název akce z adresy URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Směrování atributů s atributy Http[Verb]

Směrování atributů může také `Http[Verb]` využít atributy, jako `HttpPostAttribute`je například . Všechny tyto atributy mohou přijmout šablonu trasy. Tento příklad ukazuje dvě akce, které odpovídají stejné šabloně trasy:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Pro cestu URL, jako `/products` je `ProductsApi.ListProducts` akce, bude `GET` provedena, když je a `ProductsApi.CreateProduct` bude `POST`provedeno sloveso HTTP, když je sloveso HTTP . Nejprve směrovat směr atributu odpovídá adrese URL se sadou šablon tras definovaných atributy trasy. Jakmile se šablona `IActionConstraint` trasy shoduje, použijí se omezení k určení, které akce lze provést.

> [!TIP]
> Při vytváření rozhraní REST API, je vzácné, `[Route(...)]` že budete chtít použít na metodu akce jako akce bude akce přijímat všechny metody HTTP. Je lepší použít konkrétnější, `Http*Verb*Attributes` abyste byli přesní o tom, co vaše rozhraní API podporuje. Očekává se, že klienti rozhraní REST API budou vědět, jaké cesty a http slovesa jsou mapována na konkrétní logické operace.

Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné nastavit parametry požadované jako součást definice šablony trasy. V tomto `id` příkladu je požadováno jako součást cesty URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Akce `ProductsApi.GetProduct(int)` bude provedena pro cestu URL, jako `/products/3` je, `/products`ale ne pro cestu URL, jako je . Úplný popis šablon tras a souvisejících možností naleznete v tématu [Směrování.](xref:fundamentals/routing)

## <a name="route-name"></a>Název postupu

Následující kód definuje *název* postupu `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Názvy tras lze použít ke generování adresy URL na základě konkrétní trasy. Názvy tras nemají žádný vliv na chování směrování při porovnávání adres URL a používají se pouze pro generování adres URL. Názvy tras musí být jedinečné pro celou aplikaci.

> [!NOTE]
> Porovnejte to s konvenční *výchozí trasou* `id` `{id?}`, která definuje parametr jako volitelný ( ). Tato schopnost přesně určit, že api má `/products` `/products/5` výhody, jako je například povolení a odeslání do různých akcí.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Kombinování tras

Chcete-li, aby směrování atributů bylo méně opakované, jsou atributy trasy na řadiči kombinovány s atributy trasy u jednotlivých akcí. Všechny šablony tras definované na řadiči jsou předřazeny k směrování šablon akcí. Umístění atributu trasy na řadič provede **všechny** akce v řadiči pomocí směrování atributu.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

V tomto příkladu `/products` se `ProductsApi.ListProducts`může cesta `/products/5` url `ProductsApi.GetProduct(int)`shodovat a cesta adresy URL se může shodovat . Obě tyto akce odpovídají `GET` pouze protokolu HTTP, `HttpGetAttribute`protože jsou označeny písmenem .

Šablony trasy použité na akci, `/` `~/` která začíná nebo nezkombinují se šablonami tras aplikovanými na kontroler. Tento příklad odpovídá sadě cest URL podobné *výchozí trase*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Řazení tras atributů

Na rozdíl od konvenčních tras, které se provádějí v definovaném pořadí, směrování atributů vytvoří strom a porovná všechny trasy současně. To se chová jako-pokud položky trasy byly umístěny v ideálním pořadí; nejkonkrétnější trasy mají šanci provést před obecnějšími trasami.

Například trasa `blog/search/{topic}` jako je konkrétnější než `blog/{*article}`trasa jako . Logicky řečeno `blog/search/{topic}` trasa 'běží' první, ve výchozím nastavení, protože to je jediný rozumný řazení. Pomocí konvenčního směrování je vývojář zodpovědný za umístění tras v požadovaném pořadí.

Trasy atributů můžete nakonfigurovat `Order` pořadí, pomocí vlastnosti všech atributů trasy za předpokladu, že rozhraní. Trasy jsou zpracovávány podle vzestupného druhu `Order` vlastnosti. Výchozí pořadí `0`je . Nastavení trasy `Order = -1` pomocí bude spuštěno před trasami, které nenastavují objednávku. Nastavení trasy `Order = 1` pomocí bude spuštěno po výchozím pořadí trasy.

> [!TIP]
> Vzávislosti `Order`na . Pokud vaše URL-space vyžaduje explicitní hodnoty objednávky směrovat správně, pak je to pravděpodobně matoucí i pro klienty. Obecně atribut směrování vybere správný postup s URL odpovídající. Pokud výchozí pořadí použité pro generování adresy URL nefunguje, použití názvu trasy `Order` jako přepsání je obvykle jednodušší než použití vlastnosti.

Razor Pages směrování a MVC řadič směrování sdílet implementaci. Informace o pořadí tras v tématech Razor Pages jsou k dispozici na [trase Razor Pages a konvencích aplikací: Pořadí tras](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Nahrazení tokenu v šablonách tras ([kontrolor], [akce], [oblast])

Pro větší pohodlí atribut trasy podporují *nahrazení tokenu* ohraničující token v čtvercových závorkách (`[`, `]`). Tokeny `[action]`, `[area]`a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu řadiče z akce, kde je trasa definována. V následujícím příkladu akce odpovídají adresám URL, jak je popsáno v komentářích:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Nahrazení tokenu dochází jako poslední krok vytváření trasy atributu. Výše uvedený příklad se bude chovat stejně jako následující kód:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Trasy atributů lze také kombinovat s dědičností. To je obzvláště silný v kombinaci s token nahrazení.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Nahrazení tokenu platí také pro názvy tras definované postupy atributů. `[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje jedinečný název trasy pro každou akci.

Chcete-li odpovídat oddělovač `[` `]`nahrazení literálu tokenu`[[` `]]`nebo , uniknout opakováním znaku ( nebo ).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Přizpůsobení nahrazení tokenu pomocí transformátoru parametrů

Token nahrazení lze přizpůsobit pomocí parametrtransformátoru. Parametr transformátor `IOutboundParameterTransformer` implementuje a transformuje hodnotu parametrů. Například vlastní `SlugifyParameterTransformer` parametr transformátor `SubscriptionManagement` změní `subscription-management`hodnotu trasy na .

Jedná `RouteTokenTransformerConvention` se o konvence modelu aplikace, která:

* Aplikuje parametr transformátor u všech tras atributů v aplikaci.
* Přizpůsobí hodnoty tokenu postupu atributu při jejich nahrazení.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

Je `RouteTokenTransformerConvention` registrován jako možnost `ConfigureServices`v .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Více tras

Směrování atributů podporuje definování více tras, které dosáhnou stejné akce. Nejběžnější použití tohoto je napodobovat chování *výchozí konvenční trasy,* jak je znázorněno v následujícím příkladu:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Uvedení více atributů trasy na kontroleru znamená, že každý z nich bude kombinovat s každým z atributů trasy na metody akce.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Pokud je do akce `IActionConstraint`umístěno více atributů trasy (které implementují), každé omezení akce se zkombinuje se šablonou trasy z atributu, který ji definoval.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Při použití více tras na akce se může zdát silný, je lepší, aby vaše aplikace URL prostor jednoduchý a dobře definovaný. Více tras na akce používejte pouze v případě potřeby, například pro podporu stávajících klientů.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Určení volitelných parametrů, výchozích hodnot a omezení postupu atributu

Trasy atributů podporují stejnou vložkovou syntaxi jako konvenční trasy pro určení volitelných parametrů, výchozích hodnot a omezení.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Podrobný popis syntaxe šablony trasy najdete v tématu [Odkaz na šablonu](xref:fundamentals/routing#route-template-reference) trasy.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Vlastní atributy trasy pomocí`IRouteTemplateProvider`

Rozhraní implementují `[Route(...)]` `IRouteTemplateProvider` všechny atributy trasy `[HttpGet(...)]` uvedené v rámci ( , , atd.). MVC hledá atributy na třídy kontroleru a metody akce při `IRouteTemplateProvider` spuštění aplikace a používá ty, které implementují k vytvoření počáteční sadu tras.

Můžete implementovat `IRouteTemplateProvider` definovat vlastní atributy trasy. Každý `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou trasy, pořadím a názvem:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Atribut z výše uvedeného `Template` příkladu automaticky nastaví na, `"api/[controller]"` kdy `[MyApiController]` je použita.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Přizpůsobení tras atributů pomocí modelu aplikace

*Aplikační model* je objektový model vytvořený při spuštění se všemi metadaty používanými mvc ke směrování a provádění vašich akcí. Aplikační *model* obsahuje všechna data shromážděná z `IRouteTemplateProvider`atributů trasy (přes). Můžete napsat *konvence* upravit model aplikace při spuštění přizpůsobit, jak se chová směrování. Tato část ukazuje jednoduchý příklad přizpůsobení směrování pomocí aplikačního modelu.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Smíšené směrování: Směrování atributů vs konvenční směrování

Aplikace MVC mohou kombinovat použití konvenčního směrování a směrování atributů. Je typické používat konvenční trasy pro řadiče obsluhující stránky HTML pro prohlížeče a směrování atributů pro řadiče obsluhující rest API.

Akce jsou buď konvenčně směrovány nebo atribut směrovány. Umístění trasy na řadič nebo akce způsobí, že atribut směrovány. Akce, které definují trasy atributů nelze dosáhnout prostřednictvím konvenční trasy a naopak. **Libovolný** atribut trasy na řadiči provede všechny akce v atributu kontroleru směrovány.

> [!NOTE]
> Odlišuje dva typy systémů směrování proces použitý poté, co adresa URL odpovídá šabloně trasy. V konvenčním směrování se hodnoty trasy ze shody používají k výběru akce a kontroleru z vyhledávací tabulky všech konvenčních směrovaných akcí. V směrování atributů je každá šablona již přidružena k akci a není potřeba žádné další vyhledávání.

## <a name="complex-segments"></a>Složité segmenty

Složité segmenty (například `[Route("/dog{token}cat")]`), jsou zpracovány porovnáním literály zprava doleva nechamtivým způsobem. Popis [naleznete ve zdrojovém kódu.](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) Další informace naleznete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Generování adresy URL

Aplikace MVC mohou pomocí funkcí generování adres URL směrování generovat odkazy na adresy URL na akce. Generování adres URL eliminuje pevné kódování adres URL, takže váš kód robustnější a udržovatelné. Tato část se zaměřuje na funkce generování adres URL poskytované MVC a bude se týkat pouze základy fungování generování adres URL. Podrobný popis generování adres URL naleznete v tématu [Směrování.](xref:fundamentals/routing)

Rozhraní `IUrlHelper` je základní část infrastruktury mezi MVC a směrování pro generování adres URL. Najdete instanci `IUrlHelper` k dispozici `Url` prostřednictvím vlastnosti v kontrolerně, zobrazení a komponenty zobrazení.

V tomto příkladu `IUrlHelper` rozhraní se `Controller.Url` používá prostřednictvím vlastnosti ke generování adresy URL na jinou akci.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Pokud aplikace používá výchozí konvenční trasu, `url` bude hodnotou proměnné `/UrlGeneration/Destination`řetězec cesty URL . Tato cesta URL je vytvořena směrováním kombinací hodnot trasy z aktuálního požadavku `Url.Action` (hodnoty okolí) s hodnotami předanými a nahrazením těchto hodnot do šablony postupu:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Každý parametr trasy v šabloně trasy má svou hodnotu nahrazenou sladěnými názvy s hodnotami a hodnotami okolí. Parametr trasy, který nemá hodnotu, může použít výchozí hodnotu, pokud ji má, nebo být přeskočen, pokud je volitelný (jako v tomto příkladu). `id` Generování adresy URL se nezdaří, pokud žádný parametr požadované trasy nemá odpovídající hodnotu. Pokud se generování adresy URL nezdaří pro trasu, další trasa je vyzkoušena, dokud nebyly vyzkoušeny všechny trasy nebo nalezena shoda.

Příklad `Url.Action` výše předpokládá konvenční směrování, ale generování adres URL funguje podobně s směrováním atributů, i když koncepty se liší. Při konvenčním směrování se hodnoty postupu používají k rozbalení `controller` `action` šablony a hodnoty postupu pro tuto šablonu a obvykle se v ní zobrazují – funguje to proto, že adresy URL odpovídající směrování dodržují *konvence*. V směrování atributů se `controller` `action` hodnoty postupu pro šablonu a nejsou v ní nesmí zobrazovat – místo toho se používají k vyhledání šablony, která se má použít.

Tento příklad používá směrování atributů:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC vytvoří vyhledávací tabulku všech směrovaných akcí atributu `controller` `action` a bude odpovídat hodnotám a, které mají být vyvoleny, aby se šablona trasy použila pro generování adresy URL. Ve výše uvedeném vzorku `custom/url/to/destination` je generován.

### <a name="generating-urls-by-action-name"></a>Generování adres URL podle názvu akce

`Url.Action` (`IUrlHelper` . `Action`) a všechna související přetížení jsou založena na této myšlence, že chcete určit, na co odkazujete zadáním názvu řadiče a názvu akce.

> [!NOTE]
> Při `Url.Action`použití jsou aktuální `controller` hodnoty `action` trasy pro vás zadány a jsou určeny `controller` pro vás - hodnota a `action` jsou součástí hodnot *okolí* **i** *hodnot*. Metoda `Url.Action`, vždy používá aktuální `action` `controller` hodnoty a a bude generovat cestu URL, která vede k aktuální akci.

Směrování se pokusí použít hodnoty v okolních hodnotách k vyplnění informací, které jste neposkytli při generování adresy URL. Pomocí trasy `{a}/{b}/{c}/{d}` jako a `{ a = Alice, b = Bob, c = Carol, d = David }`okolní hodnoty , směrování má dostatek informací pro generování URL bez dalších hodnot - protože všechny parametry trasy mají hodnotu. Pokud jste přidali hodnotu `{ d = Donovan }`, bude hodnota `{ d = David }` ignorována a `Alice/Bob/Carol/Donovan`vygenerovaná cesta url bude .

> [!WARNING]
> Cesty adres URL jsou hierarchické. Ve výše uvedeném příkladu, `{ c = Cheryl }`pokud jste `{ c = Carol, d = David }` přidali hodnotu , budou obě hodnoty ignorovány. V tomto případě již nemáme `d` hodnotu pro a generování adresy URL se nezdaří. Budete muset zadat požadovanou `c` hodnotu a `d`.  Můžete očekávat, že tento problém s`{controller}/{action}/{id?}`výchozí trasu ( ) - ale `Url.Action` zřídka narazíte `controller` `action` na toto chování v praxi, jak bude vždy explicitně zadat a hodnotu.

Delší přetížení `Url.Action` také trvat další *hodnoty trasy* objektu poskytnout `controller` hodnoty `action`pro parametry trasy než a . Nejčastěji se zobrazí toto `id` `Url.Action("Buy", "Products", new { id = 17 })`používá s like . Podle konvence je objekt *hodnot trasy* obvykle objektem anonymního `IDictionary<>` typu, ale může se také jedná o prostý *starý objekt .NET*. Všechny další hodnoty trasy, které neodpovídají parametrům trasy, jsou vloženy do řetězce dotazu.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Chcete-li vytvořit absolutní adresu URL, `protocol`použijte přetížení, které přijímá :`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generování adres URL podle trasy

Výše uvedený kód demonstroval generování adresy URL předáním v řadiči a název akce. `IUrlHelper`také poskytuje `Url.RouteUrl` rodinu metod. Tyto metody jsou `Url.Action`podobné , ale nekopírují `action` `controller` aktuální hodnoty a hodnoty postupu. Nejběžnější použití je zadat název trasy pro použití určité trasy pro generování adresy URL, obecně *bez* zadání názvu řadiče nebo akce.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generování adres URL v HTML

`IHtmlHelper`poskytuje `HtmlHelper` metody `Html.BeginForm` `Html.ActionLink` a `<form>` vytvářet a `<a>` prvky. Tyto metody `Url.Action` používají metodu ke generování adresy URL a přijímají podobné argumenty. `Url.RouteUrl` Společníci `HtmlHelper` `Html.BeginRouteForm` pro `Html.RouteLink` jsou a které mají podobné funkce.

TagHelpers generovat adresy URL `form` prostřednictvím TagHelper a `<a>` TagHelper. Obě tyto `IUrlHelper` použití pro jejich realizaci. Další informace naleznete [v tématu Práce s formuláři.](../views/working-with-forms.md)

Vnitřní zobrazení, `IUrlHelper` je k `Url` dispozici prostřednictvím vlastnosti pro všechny ad-hoc URL generace, které nejsou zahrnuty výše.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generování adres URL ve výsledcích akce

Výše uvedené příklady `IUrlHelper` ukázaly použití v řadiči, zatímco nejběžnější použití v řadiči je generovat adresu URL jako součást výsledku akce.

A `ControllerBase` `Controller` základní třídy poskytují komfortní metody pro výsledky akce, které odkazují na jinou akci. Jedním z typických použití je přesměrování po přijetí vstupu uživatele.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Výsledky akce factory metody postupujte podobný `IUrlHelper`vzor metody na .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Zvláštní případ pro vyhrazené konvenční trasy

Konvenční trasa může používat speciální druh definice trasy nazývanou *vyhrazená konvenční trasa*. V níže uvedeném příkladu je trasa s názvem `blog` vyhrazená konvenční trasa.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Pomocí těchto definic `Url.Action("Index", "Home")` tras vygeneruje `/` cestu `default` URL s trasou, ale proč? Můžete uhodnout, `{ controller = Home, action = Index }` že hodnoty trasy by `blog`stačily ke generování `/blog?action=Index&controller=Home`adresy URL pomocí a výsledkem by bylo .

Vyhrazené konvenční trasy spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla "příliš hladová" s generováním adresy URL. V tomto případě jsou `{ controller = Blog, action = Article }`výchozí hodnoty `controller` `action` a jako parametr trasy se nezobrazují ani se nezobrazují. Při směrování provádí generování adresy URL, zadaná hodnota musí odpovídat výchozí hodnoty. Generování adresy `blog` URL pomocí `{ controller = Home, action = Index }` se nezdaří, protože hodnoty se neshodují `{ controller = Blog, action = Article }`. Směrování pak přejde `default`zpět vyzkoušet , který je úspěšný.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Oblasti

[Oblasti](areas.md) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatný obor názvů směrování (pro akce řadiče) a strukturu složek (pro zobrazení). Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem - pokud mají různé *oblasti*. Použití oblastí vytvoří hierarchii pro účely směrování přidáním `controller` `action`dalšího parametru trasy `area` do a . Tato část bude pojednávat o tom, jak směrování spolupracuje s oblastmi – podrobnosti o tom, jak se oblasti používají se zobrazeními, najdete v článku [Oblasti.](areas.md)

Následující příklad konfiguruje mvc pro použití výchozí konvenční `Blog`trasy a trasy *oblasti* pro oblast s názvem :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Při porovnávání cesty `/Manage/Users/AddUser`URL , jako je `{ area = Blog, controller = Users, action = AddUser }`první trasa, vytvoří hodnoty trasy . Hodnota `area` postupu je vytvořena výchozí `area`hodnotou pro aplikaci `MapAreaRoute` , ve skutečnosti je trasa vytvořená ekvivalentní následujícímu:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`vytvoří trasu pomocí výchozí hodnoty `area` i omezení pro použití zadaný `Blog`název oblasti, v tomto případě . Výchozí hodnota zajišťuje, že trasa `{ area = Blog, ... }`vždy vytváří , `{ area = Blog, ... }` omezení vyžaduje hodnotu pro generování adresy URL.

> [!TIP]
> Konvenční směrování je závislé na objednávce. Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce tras, protože jsou konkrétnější než trasy bez oblasti.

Pomocí výše uvedeného příkladu by hodnoty trasy odpovídaly následující akci:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

To `AreaAttribute` je to, co označuje regulátor jako součást oblasti, říkáme, že tento regulátor je v `Blog` této oblasti. Kontroloři `[Area]` bez atributu nejsou členy žádné oblasti a `area` **nebudou** odpovídat, pokud je hodnota postupu poskytnuta směrováním. V následujícím příkladu může hodnoty `{ area = Blog, controller = Users, action = AddUser }`trasy odpovídat pouze prvnímu uvedenému řadiči .

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Obor názvů každého řadiče je zde zobrazen pro úplnost - jinak řadiče by měly konflikt názvů a generovaly chybu kompilátoru. Obory názvů tříd nemají žádný vliv na směrování MVC.

První dva řadiče jsou členy oblastí a shodují se pouze v `area` případě, že jejich název oblasti je zajišťován hodnotou trasy. Třetí řadič není členem žádné oblasti a může se shodovat pouze v případě, že směrováním neposkytuje žádnou hodnotu pro. `area`

> [!NOTE]
> Pokud jde o odpovídající *žádnou hodnotu*, absence `area` hodnoty `area` je stejná, jako kdyby hodnota pro byly null nebo prázdný řetězec.

Při provádění akce uvnitř oblasti bude hodnota `area` trasy pro k dispozici jako *okolní hodnota* pro směrování, která se použije pro generování adresy URL. To znamená, že ve výchozím nastavení oblasti jednat *sticky* pro generování adresy URL, jak ukazuje následující ukázka.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Principy omezení iaction

> [!NOTE]
> Tato část je podrobné prolomení na vnitřní rozhraní a jak MVC vybere akci k provedení. Typická aplikace nebude potřebovat vlastní`IActionConstraint`

Pravděpodobně jste již `IActionConstraint` použili, i když nejste obeznámeni s rozhraním. Atribut `[HttpGet]` a `[Http-VERB]` podobné atributy implementovat `IActionConstraint` s cílem omezit provádění metody akce.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Za předpokladu výchozí konvenční `/Products/Edit` trasy by `{ controller = Products, action = Edit }`cesta url vytvořila hodnoty , které by odpovídaly **oběma** zde zobrazených akcím. V `IActionConstraint` terminologii bychom řekli, že obě tyto akce jsou považovány za kandidáty - protože obě odpovídají údajům o trase.

Při `HttpGetAttribute` spuštění, bude říkat, že *Edit()* je shoda pro *GET* a není shoda pro jiné sloveso HTTP. Akce `Edit(...)` nemá definována žádná omezení, a proto bude odpovídat libovolnému slovesu HTTP. Takže za `POST` předpokladu, že - pouze `Edit(...)` zápasy. Ale pro `GET` obě akce může ještě zápas - `IActionConstraint` nicméně, akce s je vždy považován za *lepší* než akce bez. Takže `Edit()` protože `[HttpGet]` je to považováno za konkrétnější a bude vybráno, pokud se obě akce mohou shodovat.

Koncepčně `IActionConstraint` je forma *přetížení*, ale místo přetížení metody se stejným názvem, je přetížení mezi akcemi, které odpovídají stejné adrese URL. Směrování atributů `IActionConstraint` také používá a může mít za následek akce z různých řadičů oba považovány za kandidáty.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementace omezení akce IActionConstraint

Nejjednodušší způsob, jak `IActionConstraint` implementovat je vytvořit třídu odvozenou od `System.Attribute` a umístit ji na vaše akce a řadiče. MVC automaticky zjišťuje všechny, `IActionConstraint` které jsou použity jako atributy. Model aplikace můžete použít omezení a to to je pravděpodobně nejflexibilnější přístup, protože umožňuje metaprogram, jak jsou použity.

V následujícím příkladu omezení vybere akci na základě *kódu země* z dat trasy. [Úplná ukázka na GitHubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Jste zodpovědní za `Accept` implementaci metody a výběru "Objednávka" pro omezení spustit. V tomto případě `Accept` se `true` metoda vrátí k označení akce `country` je shoda, když hodnota trasy odpovídá. To se liší `RouteValueAttribute` od v tom, že umožňuje záložní k akci bez atributu. Ukázka ukazuje, že `en-US` pokud definujete akci, kód země jako `fr-FR` se vrátí k `[CountrySpecific(...)]` obecnějšímu řadiči, který se nepoužije.

Vlastnost `Order` rozhodne, ve *které fázi* je omezení součástí. Omezení akce jsou spuštěna `Order`ve skupinách na základě . Například všechny atributy framework za předpokladu, `Order` http metody použít stejnou hodnotu tak, aby byly spuštěny ve stejné fázi. Můžete mít tolik fází, kolik potřebujete k implementaci požadovaných zásad.

> [!TIP]
> Chcete-li rozhodnout `Order` o hodnotě pro přemýšlet o tom, zda vaše omezení by měla být použita před metodami HTTP. Nižší čísla spustit jako první.

::: moniker-end
