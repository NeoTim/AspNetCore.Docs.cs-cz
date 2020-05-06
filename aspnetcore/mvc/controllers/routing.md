---
title: Směrování na akce kontroleru v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core MVC používá middleware směrování k párování adres URL příchozích požadavků a jejich mapování na akce.
ms.author: riande
ms.date: 3/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/routing
ms.openlocfilehash: 4208ef8fb7a9b10621f214f79679ff8d7fd83996
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775021"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Směrování na akce kontroleru v ASP.NET Core

Služba [Ryan Nowak](https://github.com/rynowak), [Kirka Larkin](https://twitter.com/serpent5)a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Řadiče ASP.NET Core používají [middleware](xref:fundamentals/middleware/index) směrování ke spárování adres URL příchozích požadavků a jejich mapování na [Akce](#action).  Šablony tras:

* Jsou definovány ve spouštěcím kódu nebo atributech.
* Popisuje, jak se shodují cesty URL k [akcím](#action).
* Slouží ke generování adres URL pro odkazy. Vygenerované odkazy jsou obvykle vraceny v odpovědích.

Akce jsou směrovány buď podle [konvence](#cr) , nebo podle [atributů](#ar). Umístěním trasy do kontroleru nebo [Akce](#action) se nastaví směrování atributů. Další informace najdete v tématu [smíšená směrování](#routing-mixed-ref-label) .

Tento dokument:

* Vysvětluje interakce mezi MVC a směrováním:
  * Jak typické aplikace MVC využívají funkce směrování.
  * Pokrývá obojí:
    * [Směrování](#cr) se obvykle používá s řadiči a zobrazeními.
    * *Směrování atributů* používané s rozhraními REST API. Pokud se primárně zajímáte o směrování pro rozhraní REST API, přejděte na oddíl [Směrování atributů pro rozhraní REST API](#ar) .
  * Podrobnosti o rozšířeném směrování najdete v tématu [Směrování](xref:fundamentals/routing) .
* Odkazuje na výchozí systém směrování přidaný v ASP.NET Core 3,0, který se označuje jako směrování koncového bodu. Pro účely kompatibility je možné použít řadiče s předchozí verzí směrování. Pokyny najdete v [příručce k migraci 2.2 – 3.0](xref:migration/22-to-30) . Referenční materiály ke staršímu systému směrování najdete v [tomto dokumentu ve verzi 2,2](xref:mvc/controllers/routing?view=aspnetcore-2.2) .

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Nastavení konvenční trasy

`Startup.Configure`obvykle má při použití [konvenčního směrování](#crd)kód podobný následujícímu:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Uvnitř volání metody <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> se používá k vytvoření jedné trasy. Jedna trasa má název `default` Route. Většina aplikací s řadiči a zobrazeními používá šablonu směrování podobnou `default` trase. Rozhraní REST API by měly používat [Směrování atributů](#ar).

Šablona `"{controller=Home}/{action=Index}/{id?}"`trasy:

* Odpovídá cestě URL jako`/Products/Details/5`
* Extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` tras tím, že tokenizací cestu. Výsledkem extrakce hodnot tras je shoda, pokud má aplikace kontroler s názvem `ProductsController` a `Details` akci:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`model váže hodnotu `id = 5` pro nastavení `id` parametru. `5` Další podrobnosti najdete v tématu [vazba modelu](xref:mvc/models/model-binding) .
* `{controller=Home}`definuje `Home` jako výchozí `controller`.
* `{action=Index}`definuje `Index` jako výchozí `action`.
*  `?` Znak v `{id?}` definuje `id` jako volitelné.
  * Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici. Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .
* Odpovídá cestě `/`URL.
* Vytvoří hodnoty `{ controller = Home, action = Index }`trasy.

Hodnoty pro `controller` a `action` využívají výchozí hodnoty. `id`nevytváří hodnotu, protože v cestě URL není žádný odpovídající segment. `/`odpovídá pouze v `HomeController` případě, že existuje `Index` akce a:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Pomocí předchozí definice kontroleru a šablony trasy se `HomeController.Index` akce spustí pro následující cesty URL:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

Cesta `/` URL používá výchozí `Home` řadiče a `Index` akci šablony směrování. Cesta `/Home` URL používá výchozí `Index` akci šablony směrování.

Způsob <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>usnadnění:

```csharp
endpoints.MapDefaultControllerRoute();
```

LPRMON

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Směrování je nakonfigurované pomocí <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> middlewaru a <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> . Použití řadičů:
>
> * Zavolejte <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> uvnitř `UseEndpoints` pro mapování řadičů [směrovaných na atributy](#ar) .
> * Pro <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> mapování <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>konvence, která je [směrována](#cr) na řadiče, zavolejte nebo.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Konvenční směrování

Konvenční směrování se používá s řadiči a zobrazeními. `default` Trasa:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

je příkladem *konvenčního směrování*. Nazývá se *konvenční směrování* , protože vytváří *konvenci* pro cesty URL:

* První segment cesty, `{controller=Home}`, mapuje na název kontroleru.
* Druhý segment, `{action=Index}`, mapuje na název [Akce](#action) .
* Třetí segment `{id?}` je použit pro volitelné `id`. `?` V `{id?}` nástroji je volitelné. `id`slouží k mapování na entitu modelu.

Pomocí této `default` trasy adresa URL:

* `/Products/List`provede mapování na `ProductsController.List` akci.
* `/Blog/Article/17`mapuje na `BlogController.Article` model a obvykle model váže `id` parametr na 17.

Toto mapování:

* Je založena **pouze**na názvech kontroléru a [Akce](#action) .
* Není založen na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.

Použití konvenčního směrování s výchozí trasou umožňuje vytvoření aplikace bez nutnosti sestavovat nový vzor adresy URL pro každou akci. Pro aplikaci s akcemi stylu [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) s konzistencí pro adresy URL napříč řadiči:

* Pomáhá zjednodušit kód.
* Provede více předvídatelného uživatelského rozhraní.

> [!WARNING]
> `id` V předchozím kódu je šablona trasy definována jako volitelná. Akce se můžou provádět bez volitelného ID, které jste zadali jako součást adresy URL. Obecně platí,`id` že pokud je VYNECHÁNA adresa URL:
>
> * `id`je nastaven na `0` základě vazby modelu.
> * V porovnání `id == 0`s databází nebyla nalezena žádná entita.
>
> [Směrování atributů](#ar) poskytuje jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní. V dokumentaci podle konvence obsahuje volitelné parametry, `id` jako když se pravděpodobně budou zobrazovat ve správném použití.

Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné. Výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}`:

* Podporuje základní a popisné schéma směrování.
* Je užitečným výchozím bodem pro aplikace založené na uživatelském rozhraní.
* Je jedinou šablonou směrování, která je nutná pro mnoho webových aplikací uživatelského rozhraní. U větších webových aplikací uživatelského rozhraní je další trasa s použitím [oblastí](#areas) , pokud je to potřeba.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>a <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :

* Automatické přiřazení hodnoty **objednávky** ke svým koncovým bodům podle pořadí, ve kterém jsou vyvolány.

Směrování koncových bodů v ASP.NET Core 3,0 a novějším:

* Nemá koncept tras.
* Neposkytuje zaručené řazení pro provádění rozšiřitelnosti, všechny koncové body jsou zpracovávány současně.

Povolte [protokolování](xref:fundamentals/logging/index) , abyste viděli <xref:Microsoft.AspNetCore.Routing.Route>, jak integrované implementace směrování, například, odpovídají požadavkům.

[Směrování atributů](#ar) je vysvětleno dále v tomto dokumentu.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Několik konvenčních tras

V rámci `UseEndpoints` lze přidat více [konvenčních tras](#cr) přidáním dalších volání do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>. To umožňuje definovat více konvencí nebo přidávat konvenční trasy, které jsou vyhrazeny určité [akci](#action), například:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

`blog` Trasa v předchozím kódu je **vyhrazená konvenční trasa**. Nazývá se vyhrazená konvenční trasa z těchto důvodů:

* Používá [konvenční směrování](#cr).
* Je vyhrazený pro konkrétní [akci](#action).

`action` Protože `controller` se nezobrazuje v šabloně `"blog/{*article}"` trasy jako parametry:

* Můžou mít jenom výchozí hodnoty `{ controller = "Blog", action = "Article" }`.
* Tato trasa se vždy mapuje na akci `BlogController.Article`.

`/Blog`, `/Blog/Article`a `/Blog/{any-string}` jsou jediné cesty URL, které odpovídají trase na blogu.

Předchozí příklad:

* `blog`trasa má vyšší prioritu pro shody, než je `default` trasa, protože je přidána jako první.
* Je a příkladem směrování stylu [popisu](https://developer.mozilla.org/docs/Glossary/Slug) , kde je typický název článku jako součást adresy URL.

> [!WARNING]
> V ASP.NET Core 3,0 a novějších směrování:
> * Definujte koncept nazvaný *trase*. `UseRouting`Přidá směrování do kanálu middlewaru. `UseRouting` Middleware prohlíží sadu koncových bodů definovaných v aplikaci a vybere nejlepší shodu koncového bodu na základě požadavku.
> * Poskytněte záruky týkající se pořadí spouštění rozšiřitelnosti, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> například <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>nebo.
>
>Přečtěte si téma [Směrování](xref:fundamentals/routing) pro referenční materiál při směrování.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Konvenční pořadí směrování

Konvenční směrování odpovídá pouze kombinaci akcí a kontrolérů, které jsou definovány aplikací. Cílem je zjednodušit případy, kdy se konvenční trasy překrývají.
Přidání tras pomocí <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatické přiřazení hodnoty objednávky ke svým koncovým bodům na základě pořadí, ve kterém jsou vyvolány. Shody z trasy, která se zobrazí dříve, mají vyšší prioritu. Konvenční směrování je závislé na pořadí. Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti. [Vyhrazené konvenční trasy](#dcr) se zachycením všech parametrů tras, `{*article}` jako je například, mohou vytvořit trasu příliš [hladce](xref:fundamentals/routing#greedy), což znamená, že odpovídají adresám URL, které mají být porovnány s jinými trasami. Tyto hladové trasy umístěte později do směrovací tabulky, aby se zabránilo hladce porovnávání.

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Řešení nejednoznačných akcí

Pokud se dva koncové body shodují přes směrování, musí směrování provést jednu z následujících akcí:

* Vyberte nejlepší kandidáta.
* Vyvolejte výjimku.

Příklad:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Předchozí kontroler definuje dvě akce, které se shodují:

* Cesta URL`/Products33/Edit/17`
* Směrování dat `{ controller = Products33, action = Edit, id = 17 }`.

Toto je typický vzor pro řadiče MVC:

* `Edit(int)`zobrazí formulář pro úpravu produktu.
* `Edit(int, Product)`zpracuje publikovaný formulář.

Postup při řešení správné trasy:

* `Edit(int, Product)`je vybrána, pokud je požadavek HTTP `POST`.
* `Edit(int)`je vybrána, pokud je [příkaz HTTP](#verb) cokoliv jiného. `Edit(int)`obvykle se volá prostřednictvím `GET`.

<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>Služba poskytuje směrování, aby se mohla zvolit na základě metody http `[HttpPost]`žádosti. Díky tomu je lepší shoda než `Edit(int)` `HttpPostAttribute` `Edit(int, Product)`

Je důležité pochopit role, jako je například `HttpPostAttribute`. Podobné atributy jsou definovány pro jiné [příkazy HTTP](#verb). V případě [konvenčního směrování](#cr)je běžné, že akce používají stejný název akce, když jsou součástí formuláře zobrazení, odeslání pracovního postupu formuláře. Příklad naleznete v tématu [prostudování dvou metod Edit Action](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Pokud směrování nemůže zvolit nejlepší kandidáta, je <xref:System.Reflection.AmbiguousMatchException> vyvolána výjimka, která obsahuje seznam více odpovídajících koncových bodů.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Názvy konvenčních tras

Řetězce `"blog"` a `"default"` v následujících příkladech jsou konvenční názvy tras:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Názvy tras udávají logický název trasy. Pojmenovanou trasu lze použít pro generování adresy URL. Použití pojmenované trasy zjednodušuje vytváření adresy URL, když řazení tras může způsobit komplikované generování adresy URL. Názvy tras musí být jedinečné aplikace v širším rozsahu.

Názvy tras:

* Nemusíte mít žádný vliv na adresu URL ani manipulaci s požadavky.
* Jsou používány pouze pro generování adresy URL.

Koncept názvu trasy je reprezentován ve směrování jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). Název **Směrování** a název **koncového bodu**:

* Jsou zaměnitelné.
* Který je používán v dokumentaci a kód závisí na popisovaném rozhraní API.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Směrování atributů pro rozhraní REST API

Rozhraní REST API by měly používat směrování atributů k modelování funkčnosti aplikace jako sady prostředků, ve kterých jsou operace reprezentované [příkazy HTTP](#verb).

Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování. Následující `StartUp.Configure` kód je typický pro REST API a používá se v další ukázce:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> je volána uvnitř `UseEndpoints` pro mapování řadičů směrovaných na atribut.

V následujícím příkladu:

* Předchozí `Configure` metoda se používá.
* `HomeController`vyhledá shodu se sadou adres URL podobných tomu, `{controller=Home}/{action=Index}/{id?}` co výchozí konvenční trasa odpovídá.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

`HomeController.Index` Akce se `/`spustí pro všechny cesty URL, `/Home`, `/Home/Index`nebo. `/Home/Index/3`

V tomto příkladu se zvýrazní klíčový rozdíl mezi směrováním atributů a [konvenčním směrováním](#cr). Směrování atributů vyžaduje více vstupu pro určení trasy. Konvenční výchozí trasa zpracovává trasy více stručně. Směrování atributů ale umožňuje a vyžaduje přesnou kontrolu nad tím, které šablony směrování se vztahují na každou [akci](#action).

Při směrování atributů název kontroleru a názvy akcí nehrají **žádnou** roli, v rámci které se akce shodují. V následujícím příkladu se shodují stejné adresy URL jako v předchozím příkladu:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Následující kód používá nahrazení tokenu pro `action` a `controller`:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Následující kód platí `[Route("[controller]/[action]")]` pro kontroler:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

V předchozím kódu musí šablony `Index` metody předřadit `/` nebo `~/` do šablon směrování. Šablony směrování použité pro akci, která začíná `/` nebo `~/` není kombinována s šablonami směrování použitými pro kontroler.

Informace o výběru šablony směrování najdete v tématu [Priorita šablony směrování](xref:fundamentals/routing#rtp) .

## <a name="reserved-routing-names"></a>Názvy rezervovaných směrování

Následující klíčová slova jsou rezervované názvy parametrů tras při použití řadičů nebo Razor Pages:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Použití `page` jako parametr trasy se směrováním atributů je běžná chyba. Výsledkem je nekonzistentní a matoucí chování s generováním adresy URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Speciální názvy parametrů jsou používány generováním adresy URL k určení, zda operace generování adresy URL odkazuje na stránku Razor nebo na kontroler.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Šablony příkazů HTTP

ASP.NET Core má následující šablony příkazů HTTP:

* [HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HTTPPOST](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Šablony směrování

ASP.NET Core má následující šablony tras:

* Všechny [šablony operací HTTP](#verb) jsou šablony směrování.
* [Cestě](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Směrování atributů s atributy příkazu http

Vezměte v úvahu následující kontroler:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

V předchozím kódu:

* Každá akce obsahuje `[HttpGet]` atribut, který omezuje porovnání pouze s požadavky HTTP GET.
* Tato `GetProduct` akce zahrnuje `"{id}"` šablonu, proto `id` je připojená k `"api/[controller]"` šabloně na řadiči. Šablona metod je `"api/[controller]/"{id}""`. Proto tato akce odpovídá pouze požadavkům get pro formulář `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`atd.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* `GetIntProduct` Akce obsahuje `"int/{id:int}")` šablonu. `:int` Část šablony omezuje hodnoty `id` směrování na řetězce, které lze převést na celé číslo. Požadavek GET na `/api/test2/int/abc`:
  * Neodpovídá této akci.
  * Vrátí chybu typu 404, která [nebyla nalezena](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* `GetInt2Product` Akce obsahuje `{id}` v šabloně, ale neomezí `id` na hodnoty, které lze převést na celé číslo. Požadavek GET na `/api/test2/int2/abc`:
  * Odpovídá této trase.
  * Vazbu modelu se nepodařilo převést `abc` na celé číslo. `id` Parametr metody je celé číslo.
  * Vrátí [chybný požadavek 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , protože vazba modelu selhala při`abc` převodu na celé číslo.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Směrování atributů může používat <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> atributy jako <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>a. <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> Všechny atributy [příkazu http](#verb) přijímají šablonu směrování. Následující příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Pomocí cesty `/products3`URL:

* `MyProductsController.ListProducts` Akce se spustí, když je `GET` [příkaz HTTP](#verb) .
* `MyProductsController.CreateProduct` Akce se spustí, když je `POST` [příkaz HTTP](#verb) .

Při sestavování REST API je zřídka nutné použít `[Route(...)]` metodu akce, protože akce akceptuje všechny metody HTTP. Je lepší použít konkrétnější [atribut příkazu http](#verb) , abyste mohli přesně zjistit, co vaše rozhraní API podporuje. Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.

Rozhraní REST API by měly používat směrování atributů k modelování funkčnosti aplikace jako sady prostředků, ve kterých jsou operace reprezentované příkazy HTTP. To znamená, že mnohé operace, například GET a POST u stejného logického prostředku, používají stejnou adresu URL. Směrování atributů poskytuje úroveň řízení, která je nutná k pečlivému návrhu rozložení veřejného koncového bodu rozhraní API.

Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy. V následujícím příkladu `id` je vyžadována jako součást cesty URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

`Products2ApiController.GetProduct(int)` Akce:

* Se spouští s cestou URL jako`/products2/3`
* Není spuštěn s cestou `/products2`URL.

Atribut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) ] umožňuje akci omezit podporované typy obsahu požadavků. Další informace najdete v tématu [Definování podporovaných typů obsahu požadavků pomocí atributu spotřebes](xref:web-api/index#consumes).

 Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .

Další informace o `[ApiController]`naleznete v tématu [atribut ApiController](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Název trasy

Následující kód definuje název trasy `Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Názvy tras se dají použít k vygenerování adresy URL na základě konkrétní trasy. Názvy tras:

* Nemá žádný vliv na chování směrování, které odpovídá adrese URL.
* Používá se pouze pro generování adresy URL.

Názvy tras musí být jedinečné aplikace v rozsahu.

Na rozdíl od předchozího kódu s konvenční výchozí trasou, která definuje `id` parametr jako volitelné (`{id?}`). Možnost přesně zadat rozhraní API má výhody, jako je povolení `/products` a `/products/5` odeslání do různých akcí.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Kombinování tras atributů

Aby bylo směrování atributů méně opakované, jsou atributy směrování na kontroleru kombinovány s atributy směrování na jednotlivých akcích. Každá šablona trasy definovaná na řadiči je předá směrování šablon na akcích. Umístěním atributu směrování na řadiči se vytvoří **všechny** akce v řadiči použít směrování atributů.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

V předchozím příkladu:

* Cesta `/products` URL se může shodovat.`ProductsApi.ListProducts`
* Cesta `/products/5` URL se může shodovat `ProductsApi.GetProduct(int)`.

Obě tyto akce odpovídají pouze HTTP `GET` , protože jsou označeny `[HttpGet]` atributem.

Šablony směrování použité pro akci, která začíná `/` nebo `~/` není kombinována s šablonami směrování použitými pro kontroler. Následující příklad odpovídá sadě cest URL podobně jako výchozí trasa.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

Následující tabulka popisuje `[Route]` atributy v předchozím kódu:

| Atribut               | Kombinuje s`[Route("Home")]` | Definuje šablonu směrování. |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Ano | `"Home"` |
| `[Route("Index")]` | Ano | `"Home/Index"` |
| `[Route("/")]` | **Ne** | `""` |
| `[Route("About")]` | Ano | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Pořadí směrování atributu

Směrování sestaví strom a porovnává se všemi koncovými body současně:

* Položky směrování se chovají stejně, jako kdyby byly umístěny v ideálním pořadí.
* Nejvíce konkrétní trasy mají možnost provést před obecnější trasy.

Například trasa atributu jako `blog/search/{topic}` je konkrétnější než trasa atributu jako. `blog/{*article}` Ve `blog/search/{topic}` výchozím nastavení tras má vyšší prioritu, protože je konkrétnější. Pomocí [konvenčního směrování](#cr)zodpovídá vývojář za umístění tras v požadovaném pořadí.

Trasy atributů mohou konfigurovat objednávku pomocí <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> vlastnosti. Součástí jsou všechny [atributy tras](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) , které poskytuje `Order` rozhraní. Trasy jsou zpracovávány podle vzestupného řazení `Order` vlastnosti. Výchozí pořadí je `0`. Nastavování trasy `Order = -1` pomocí spuštění před trasami, které nenastaví objednávku. Nastavení trasy pomocí `Order = 1` spuštění po výchozím řazení směrování.

**Nepoužívejte** v závislosti `Order`. Pokud je místo na adrese URL aplikace nutné správně směrovat hodnoty pořadí, je pravděpodobné, že budou i u klientů matoucí. Obecně platí, že směrování atributů vybere správnou trasu s odpovídající adresou URL. Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití `Order` vlastnosti.

Vezměte v úvahu následující dva řadiče, které definují obě trasy `/home`:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Požadavek `/home` s předchozím kódem vyvolá výjimku, která je podobná následující:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Přidání `Order` do jednoho z atributů trasy řeší nejednoznačnost:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

S předchozím kódem `/home` spustí `HomeController.Index` koncový bod. Pro získání `MyDemoController.MyIndex`žádosti `/home/MyIndex`. **Poznámka**:

* Předchozí kód je příkladem nebo nekvalitní návrh směrování. Byla použita k ilustraci `Order` vlastnosti.
* `Order` Vlastnost pouze vyřeší nejednoznačnost, tuto šablonu nelze spárovat. Je lepší odebrat `[Route("Home")]` šablonu.

Podívejte [ Razor se na stránky směrování a konvence aplikace: pořadí tras](xref:razor-pages/razor-pages-conventions#route-order) pro informace o Razor pořadí směrování se stránkami.

V některých případech se k chybě HTTP 500 vrátí nejednoznačné trasy. Pomocí [protokolování](xref:fundamentals/logging/index) zjistíte, které koncové body `AmbiguousMatchException`způsobily.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Nahrazení tokenu v šablonách směrování [Controller], [akce], [oblast]

Pro usnadnění práce, trasy atributů podporují nahrazení tokenů pro rezervované parametry tras uzavřením tokenu v jedné z následujících možností:

* Hranaté závorky:`[]`
* Složené závorky:`{}`

Tokeny `[action]`, `[area]`a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

V předchozím kódu:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Vyhovují`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Vyhovují`/Products0/Edit/{id}`

Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů. Předchozí příklad se chová stejně jako následující kód:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Trasy atributů je také možné kombinovat s dědičností. Toto je výkonné v kombinaci s náhradou tokenu. Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`vygeneruje jedinečný název trasy pro každou akci:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
vygeneruje jedinečný název trasy pro každou akci.

Chcete-li spárovat oddělovač nahrazení tokenu `[` literálu `]`nebo, zastavte ho opakováním`[[` znaku `]]`(nebo).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Použití transformátoru parametrů k přizpůsobení náhrady tokenu

Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů. Parametr Transformer implementuje <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> a transformuje hodnotu parametrů. Například vlastní `SlugifyParameterTransformer` parametr Transformer změní hodnotu `SubscriptionManagement` trasy na: `subscription-management`

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> Je konvence aplikačního modelu, která:

* Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.
* Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Předchozí `ListAll` metoda odpovídá `/subscription-management/list-all`.

`RouteTokenTransformerConvention` Je zaregistrován jako možnost v `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Definice popisu najdete v tématu [MDN web docs on](https://developer.mozilla.org/docs/Glossary/Slug) .

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Trasy s více atributy

Směrování atributů podporuje definování více tras, které dosáhnou stejné akce. Nejběžnějším využitím je napodobení chování výchozí konvenční trasy, jak je znázorněno v následujícím příkladu:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Vložení více atributů trasy na řadič znamená, že každý z nich kombinuje s každým atributem směrování na metodách akcí:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Všechna omezení trasy [příkazů http](#verb) implementují `IActionConstraint`.

Pokud je pro akci umístěno <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> více atributů tras, které implementují:

* Každé omezení akce kombinuje se šablonou směrování použitou pro kontroler.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Použití více tras na akcích může vypadat jako užitečné a výkonné, takže je lepší zachovat místo na adrese URL vaší aplikace základní a dobře definovaný. V případě potřeby použijte více tras **na akcích** , například pro podporu stávajících klientů.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Určení volitelných parametrů trasy atributu, výchozích hodnot a omezení

Trasy atributů podporují stejnou vloženou syntaxi jako konvenční trasy, aby bylo možné zadat volitelné parametry, výchozí hodnoty a omezení.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

V předchozím kódu `[HttpPost("product/{id:int}")]` platí omezení trasy. Tato `ProductsController.ShowProduct` akce odpovídá pouze cestou URL jako `/product/3`. Část `{id:int}` šablony trasy omezuje segment na pouze celá čísla.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Vlastní atributy směrování pomocí IRouteTemplateProvider

Všechny [atributy směrování](#rt) implementují <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>. Modul runtime ASP.NET Core:

* Vyhledá atributy tříd kontroleru a metody akcí při spuštění aplikace.
* Používá atributy, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.

Implementujte `IRouteTemplateProvider` pro definování vlastních atributů směrování. Každá `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Předchozí `Get` metoda vrátí `Order = 2, Template = api/MyTestApi`.

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Použití aplikačního modelu k přizpůsobení tras atributů

Aplikační model:

* Je objektovým modelem vytvořeným při spuštění.
* Obsahuje všechna metadata používaná ASP.NET Core ke směrování a provádění akcí v aplikaci.

Aplikační model zahrnuje všechna data získaná z atributů směrování. Data z atributů trasy jsou k dispozici v `IRouteTemplateProvider` implementaci. Úmluvy

* Lze zapsat pro úpravu modelu aplikace, aby bylo možné přizpůsobit způsob, jakým se směrování chová.
* Jsou čteny při spuštění aplikace.

V této části najdete základní příklad přizpůsobení směrování pomocí aplikačního modelu. Následující kód vytvoří trasy přibližně v souladu se strukturou složek v projektu.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Následující kód brání použití `namespace` úmluvy na řadičích, které jsou směrovány atributem:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Například následující kontroler nepoužívá `NamespaceRoutingConvention`:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

`NamespaceRoutingConvention.Apply` Metoda:

* Neprovede žádnou akci, pokud je řadič směrován do atributu.
* Nastaví šablonu řadičů založenou na `namespace`, se základním `namespace` odebráním.

`NamespaceRoutingConvention` Lze použít v `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Zvažte například následující kontroler:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

V předchozím kódu:

* Základem `namespace` je `My.Application`.
* Úplný název předchozího kontroleru je `My.Application.Admin.Controllers.UsersController`.
* `NamespaceRoutingConvention` Nastaví šablonu Controllers na `Admin/Controllers/Users/[action]/{id?`.

`NamespaceRoutingConvention` Lze také použít jako atribut na řadiči:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Kombinované směrování: směrování atributů vs. konvenční směrování

Aplikace ASP.NET Core můžou kombinovat použití konvenčního směrování a směrování atributů. Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.

Akce jsou směrovány buď podle konvence nebo směrovaného atributu. Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT. Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak. **Libovolný** atribut směrování v řadiči provádí směrování **všech** akcí v atributu kontroleru.

Směrování atributů a konvenční směrování používají stejný modul směrování.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Generování adresy URL a okolní hodnoty

Aplikace můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL. Generování adres URL eliminuje adresy URL zakódujeme, takže je kód robustnější a udržovatelný. Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a jenom základní informace o tom, jak generování adresy URL funguje. Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .

<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> Rozhraní je základní prvek infrastruktury mezi MVC a směrováním pro generování adresy URL. Instance `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnosti v řadičích, zobrazeních a zobrazení komponent.

V následujícím příkladu se `IUrlHelper` rozhraní používá přes `Controller.Url` vlastnost k vygenerování adresy URL jiné akci.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Pokud aplikace používá výchozí konvenční trasu, hodnota `url` proměnné je řetězec `/UrlGeneration/Destination`cesty adresy URL. Tato cesta URL se vytvoří směrováním kombinováním:

* Hodnoty tras z aktuální žádosti, které se nazývají **okolní hodnoty**.
* Hodnoty předané do `Url.Action` a nahrazování těchto hodnot do šablony trasy:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami. Parametr trasy, který nemá hodnotu, může:

* Pokud má jednu z těchto hodnot, použijte výchozí hodnotu.
* Přeskočeno, pokud je volitelné. Například `id` ze šablony `{controller}/{action}/{id?}`trasy.

Generování adresy URL se nepovede, pokud libovolný požadovaný parametr trasy nemá odpovídající hodnotu. Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.

Předchozí příklad `Url.Action` předpokládá [konvenční směrování](#cr). Generování adresy URL funguje podobně jako [Směrování atributů](#ar), i když se koncepty liší. S konvenčním směrováním:

* Hodnoty tras slouží k rozbalení šablony.
* Hodnoty směrování pro `controller` a `action` obvykle se zobrazí v této šabloně. To se hodí, protože adresy URL, které odpovídají směrování, dodržují konvenci.

Následující příklad používá směrování atributů:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

`Source` Akce v předchozím kódu generuje `custom/url/to/destination`.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>byl přidán v ASP.NET Core 3,0 jako alternativa k `IUrlHelper`. `LinkGenerator`nabízí podobné, ale flexibilnější funkce. Každá metoda on `IUrlHelper` má také odpovídající rodinu metod `LinkGenerator` .

### <a name="generating-urls-by-action-name"></a>Generování adres URL podle názvu akce

[Adresa URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)a všechny související přetížení jsou navržené tak, aby vygenerovaly cílový koncový bod zadáním názvu kontroleru a názvu akce.

Při použití `Url.Action`nástroje jsou aktuální hodnoty trasy pro `controller` a `action` poskytovány modulem runtime:

* Hodnota `controller` a `action` je součástí obou [okolních hodnot](#ambient) a hodnot. Metoda `Url.Action` vždy používá aktuální hodnoty `action` a `controller` a generuje cestu adresy URL, která směruje na aktuální akci.

Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které nebyly poskytnuty při generování adresy URL. Vezměte v úvahu trasu jako `{a}/{b}/{c}/{d}` u `{ a = Alice, b = Bob, c = Carol, d = David }`okolních hodnot:

* Směrování má dostatek informací pro vygenerování adresy URL bez dalších hodnot.
* Směrování má dostatek informací, protože všechny parametry tras mají hodnotu.

Pokud se přidá `{ d = Donovan }` hodnota:

* Hodnota `{ d = David }` se ignoruje.
* Vygenerovaná cesta URL je `Alice/Bob/Carol/Donovan`.

**Upozornění**: cesty URL jsou hierarchické. V předchozím příkladu, pokud je přidána hodnota `{ c = Cheryl }` :

* Obě hodnoty `{ c = Carol, d = David }` jsou ignorovány.
* Již není hodnota `d` a generování adresy URL se nezdařilo.
* Aby bylo možné vygenerovat adresu URL, je nutné zadat požadované hodnoty `c` a `d` .  

Můžete očekávat, že se tento problém bude narazit u `{controller}/{action}/{id?}`výchozí trasy. Tento problém je v praxi zřídka, `Url.Action` protože vždy explicitně určuje `controller` hodnotu `action` a.

Několik přetížení [URL. akce](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) přebírají objekt hodnot směrování, aby poskytovala hodnoty pro parametry směrování jiné než `controller` a. `action` Objekt hodnoty trasy se často používá s `id`. Například, `Url.Action("Buy", "Products", new { id = 17 })`. Objekt hodnot směrování:

* Podle konvence je obvykle objekt anonymního typu.
* Může to být `IDictionary<>` nebo [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Předchozí kód generuje `/Products/Buy/17?color=red`.

Následující kód vygeneruje absolutní adresu URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Chcete-li vytvořit absolutní adresu URL, použijte jednu z následujících možností:

* Přetížení, které přijímá `protocol`. Například předchozí kód.
* [LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), který ve výchozím nastavení generuje absolutní identifikátory URI.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Generovat adresy URL podle směrování

Předchozí kód ukázal generování adresy URL předáním do kontroleru a názvu akce. `IUrlHelper`také poskytuje [adresu URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) řady metod. Tyto metody jsou podobné jako [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nekopírují aktuální hodnoty `action` a `controller` do hodnot tras. Nejběžnější využití `Url.RouteUrl`:

* Určuje název trasy pro vygenerování adresy URL.
* Obecně neurčuje kontrolér nebo název akce.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Následující Razor soubor GENERUJE odkaz HTML na `Destination_Route`:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Generování adres URL ve formátu HTML aRazor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>poskytuje <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) a [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) ke generování `<form>` a `<a>` prvkům v tomto pořadí. Tyto metody používají metodu [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) pro VYGENEROVÁNÍ adresy URL a přijímající podobné argumenty. `Url.RouteUrl` Doprovodníci pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` mají podobné funkce.

TagHelpers generuje adresy URL prostřednictvím `form` taghelperu a `<a>` taghelperu. Oba tyto použití `IUrlHelper` při jejich implementaci. Další informace najdete v tématu věnovaném [pomocníkům značek ve formulářích](xref:mvc/views/working-with-forms) .

Uvnitř zobrazení `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnosti pro jakoukoli generaci adres URL ad hoc, která není pokrytá výše.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Generování adresy URL ve výsledcích akce

Předchozí příklady ukázaly použití `IUrlHelper` v kontroleru. Nejběžnějším využitím v kontroleru je vygenerování adresy URL jako součást výsledku akce.

Základní <xref:Microsoft.AspNetCore.Mvc.ControllerBase> třídy <xref:Microsoft.AspNetCore.Mvc.Controller> a poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci. Jedním z typických použití je přesměrování po přijetí vstupu uživatele:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Akce pro metody vytváření výsledků, jako <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> je <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> například a následují podobně jako metody v `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Zvláštní případ pro vyhrazené konvenční trasy

[Konvenční směrování](#cr) může používat speciální druh definice trasy, která se označuje jako [vyhrazená konvenční trasa](#dcr). V následujícím příkladu je trasa s názvem `blog` vyhrazená konvenční trasa:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Když použijete předchozí definice tras `Url.Action("Index", "Home")` , vygeneruje cestu `/` URL pomocí `default` trasy, ale proč? Můžete odhadnout, že hodnoty `{ controller = Home, action = Index }` tras by byly dostatečné pro VYGENEROVÁNÍ adresy URL `blog`pomocí a výsledek by byl `/blog?action=Index&controller=Home`.

[Vyhrazené konvenční trasy](#dcr) spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby byla trasa příliš [hladkou](xref:fundamentals/routing#greedy) při generování adresy URL. V tomto případě výchozí hodnoty jsou `{ controller = Blog, action = Article }`a `controller` ani `action` se neobjeví jako parametr trasy. Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám. Generování adresy URL `blog` pomocí neproběhne úspěšně `{ controller = Home, action = Index }` , protože `{ controller = Blog, action = Article }`hodnoty se neshodují. Směrování pak vrátí zpět k akci `default`, která bude úspěšná.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Oblasti

[Oblasti](xref:mvc/controllers/areas) představují funkci MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného:

* Obor názvů směrování pro akce kontroleru.
* Struktura složek pro zobrazení.

Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé oblasti. Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru směrování `area` do `controller` a. `action` Tato část popisuje, jak směrování komunikuje s oblastmi. Podrobnosti o tom, jak se používají oblasti se zobrazeními, najdete v části [oblasti](xref:mvc/controllers/areas) .

Následující příklad nakonfiguruje MVC tak, aby používala výchozí konvenční trasu a `area` trasu pro `area` název: `Blog`

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> je volána k vytvoření `"blog_route"`. Druhým parametrem `"Blog"`je název oblasti.

Při porovnání cesty URL jako `/Manage/Users/AddUser`se v `"blog_route"` trase vygeneruje hodnoty `{ area = Blog, controller = Users, action = AddUser }`trasy. Hodnota `area` trasy je vytvořena výchozí hodnotou pro `area`. Trasa, kterou vytvořil `MapAreaControllerRoute` , je ekvivalentní následujícímu:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute`Vytvoří trasu s použitím výchozí hodnoty a omezení pro `area` použití poskytnutého názvu oblasti v tomto případě. `Blog` Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }`, omezení vyžaduje hodnotu `{ area = Blog, ... }` pro generování adresy URL.

Konvenční směrování je závislé na pořadí. Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.

Pomocí předchozího příkladu se hodnoty `{ area = Blog, controller = Users, action = AddUser }` tras shodují s touto akcí:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Atribut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) je tím, že označuje kontroler jako součást oblasti. Tento kontroler je v `Blog` oblasti. Řadiče bez `[Area]` atributu nejsou členy žádné oblasti a **neodpovídají,** Pokud je hodnota `area` směrování poskytována směrováním. V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám `{ area = Blog, controller = Users, action = AddUser }`tras.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Pro úplnost se zobrazí obor názvů každého kontroleru. Pokud předchozí řadiče používají stejný obor názvů, vygeneruje se chyba kompilátoru. Obory názvů třídy nemají žádný vliv na směrování MVC.

První dva řadiče jsou členy oblastí a odpovídají pouze v případě, že je jejich název příslušné oblasti poskytovaný hodnotou `area` trasy. Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že směrováním neposkytuje žádnou hodnotu `area` .

<a name="aa"></a>

V souvislosti s neshodnou *hodnotou*je absence `area` hodnoty stejná, jako kdyby hodnota `area` null nebo prázdný řetězec.

Při provádění akce uvnitř oblasti `area` je hodnota trasy dostupná jako [ambientní hodnota](#ambient) pro směrování, která se má použít pro generování adresy URL. To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Následující kód vygeneruje adresu URL pro `/Zebra/Users/AddUser`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Definice akce

Veřejné metody na řadiči, s výjimkou atributu [neaction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , jsou akce.

## <a name="sample-code"></a>Ukázka kódu

 * Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) je obsažena v [ukázce stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) a slouží k zobrazení informací o směrování.
* [Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([Jak stáhnout](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC používá [middleware](xref:fundamentals/middleware/index) směrování ke spárování adres URL příchozích požadavků a jejich mapování na akce. Trasy jsou definovány ve spouštěcím kódu nebo v atributech. Trasy popisují, jak by měly být cesty URL odpovídat akcím. Trasy se také používají ke generování adres URL (pro odkazy) odesílaných v odpovědích.

Akce jsou směrovány buď podle konvence nebo směrovaného atributu. Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT. Další informace najdete v tématu [smíšená směrování](#routing-mixed-ref-label) .

Tento dokument vysvětluje interakce mezi MVC a směrováními a jak běžné aplikace MVC využívají funkce směrování. Podrobnosti o pokročilém směrování najdete v tématu [Směrování](xref:fundamentals/routing) .

## <a name="setting-up-routing-middleware"></a>Nastavení middlewaru směrování

V metodě *Konfigurace* se může zobrazit kód podobný tomuto:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Uvnitř volání metody `UseMvc` `MapRoute` se používá k vytvoření jedné trasy, na kterou odkazujeme jako na `default` trase. Většina aplikací MVC bude používat trasu se šablonou podobnou této `default` trase.

Šablona `"{controller=Home}/{action=Index}/{id?}"` trasy se může shodovat s cestou adresy URL `/Products/Details/5` , jako je, a extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` tras tím, že tokenizací cestu. MVC se pokusí vyhledat kontroler s názvem `ProductsController` a spustit akci `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Všimněte si, že v tomto příkladu vazba modelu použije hodnotu `id = 5` pro nastavení `id` parametru `5` při vyvolání této akce. Další podrobnosti najdete v tématu [vazba modelu](../models/model-binding.md) .

Pomocí `default` trasy:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Šablona trasy:

* `{controller=Home}`definuje `Home` jako výchozí.`controller`

* `{action=Index}`definuje `Index` jako výchozí.`action`

* `{id?}`definuje `id` jako volitelné.

Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici. Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .

`"{controller=Home}/{action=Index}/{id?}"`může odpovídat cestě `/` URL a vytvoří hodnoty `{ controller = Home, action = Index }`tras. Hodnoty pro `controller` a `action` využívají výchozí hodnoty nevytvářejí hodnotu, `id` protože v cestě URL není žádný odpovídající segment. MVC by tyto hodnoty trasy použily k výběru `HomeController` akce `Index` a:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Pomocí této definice kontroleru a šablony trasy se `HomeController.Index` akce spustí pro libovolnou z následujících cest URL:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Způsob `UseMvcWithDefaultRoute`usnadnění:

```csharp
app.UseMvcWithDefaultRoute();
```

Dá se použít k nahrazení:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`a `UseMvcWithDefaultRoute` přidejte instanci `RouterMiddleware` do kanálu middlewaru. MVC nekomunikuje přímo se middlewarem a používá směrování pro zpracování požadavků. MVC je připojeno k trasám prostřednictvím instance `MvcRouteHandler`. Kód uvnitř `UseMvc` je podobný následujícímu:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`přímo nedefinuje žádné trasy, přidá zástupný symbol do kolekce směrování pro `attribute` trasu. Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a zároveň podporuje směrování atributů.  `UseMvc`a všechny jeho variace přidávají zástupný symbol pro atribut směrování atributu směrování je vždycky dostupný bez ohledu na to, jak nakonfigurujete `UseMvc`. `UseMvcWithDefaultRoute`definuje výchozí trasu a podporuje směrování atributů. Oddíl [Směrování atributů](#attribute-routing-ref-label) obsahuje další podrobnosti o směrování atributů.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Konvenční směrování

`default` Trasa:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Předchozí kód je příkladem konvenčního směrování. Tento styl se nazývá konvenční směrování, protože vytváří *konvenci* pro cesty URL:

* První segment cesty se mapuje na název kontroleru.
* Druhý se mapuje na název akce.
* Třetí segment se používá pro volitelné `id`. `id`provede mapování na entitu modelu.

Pomocí této `default` trasy se `/Products/List` cesta URL mapuje `ProductsController.List` na akci a `/Blog/Article/17` mapuje se na. `BlogController.Article` Toto mapování je založené **pouze** na názvech kontroléru a akce a není založené na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.

> [!TIP]
> Použití konvenčního směrování s výchozí trasou vám umožní rychle vytvořit aplikaci, aniž by se musela používat nový vzor adresy URL pro každou akci, kterou definujete. Pro aplikace s akcemi stylu CRUD, které mají konzistenci pro adresy URL napříč řadiči, může zjednodušit váš kód a zajistit větší předvídatelné uživatelské rozhraní.

> [!WARNING]
> Šablona `id` trasy je definována jako volitelná, což znamená, že vaše akce mohou být provedeny bez ID, které je součástí adresy URL. K tomu, co se `id` stane, když se z adresy URL vynechá, znamená to `0` , že se nastaví na základě vazby modelů, a v důsledku toho se v porovnání `id == 0`databáze nenajde žádná entita. Směrování atributů vám může poskytnout jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní. Podle konvence bude dokumentace obsahovat volitelné parametry, `id` jako když se budou pravděpodobně zobrazovat ve správném použití.

## <a name="multiple-routes"></a>Více tras

Do můžete přidat více tras v `UseMvc` rámci přidáním dalších volání do `MapRoute`. V takovém případě můžete definovat více konvencí nebo přidat konvenční trasy, které jsou vyhrazeny určité akci, například:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`blog` Trasa je *vyhrazená konvenční trasa*, což znamená, že používá konvenční směrovací systém, ale je vyhrazená pro konkrétní akci. Vzhledem k tomu, že se nezobrazuje v šabloně směrování jako parametry, můžou mít jenom výchozí hodnoty, takže tato trasa se vždycky namapuje na akci `BlogController.Article` `controller` `action`

Trasy v kolekci tras jsou seřazené a budou zpracovávány v pořadí, v jakém byly přidány. V tomto příkladu bude `blog` trasa vyzkoušena před `default` trasou.

> [!NOTE]
> *Vyhrazené konvenční trasy* často používají **zachycení všech** parametrů tras, jako `{*article}` je zachycení zbývající části cesty URL. To může vést k tomu, že trasa "je příliš hlade", což znamená, že odpovídá adresám URL, které mají odpovídat jiným trasám. Pokud to chcete vyřešit, umístěte trasy "hladce" do směrovací tabulky později.

### <a name="fallback"></a>Záložní volba

V rámci zpracování žádosti MVC ověří, že hodnoty trasy lze použít k vyhledání kontroleru a akce v aplikaci. Pokud se hodnoty trasy neshodují s akcí, trasa není považována za shodnou a bude se opakovat další trasa. Označuje se jako *záložní*a je určený pro zjednodušení případů, kdy se konvenční trasy překrývají.

### <a name="disambiguating-actions"></a>Nejednoznačnost akcí

Pokud se dvě akce shodují přes směrování, MVC musí nejednoznačně vybrat kandidáta na nejlepší nebo jinak vyvolat výjimku. Příklad:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Tento kontroler definuje dvě akce, které by odpovídaly `/Products/Edit/17` cestě URL a `{ controller = Products, action = Edit, id = 17 }`datům směrování. Toto je typický vzor pro řadiče MVC, kde `Edit(int)` se zobrazuje formulář pro úpravu produktu a `Edit(int, Product)` zpracovává se tam publikovaný formulář. Aby tato možnost MVC mohla být v případě, `Edit(int, Product)` že je požadavek HTTP, a `POST` `Edit(int)` Pokud je příkaz HTTP cokoli jiného, musí si vybrat, když je žádost http.

Rozhraní `HttpPostAttribute` ( `[HttpPost]` ) je implementací `IActionConstraint` , která umožňuje, aby byla akce vybrána pouze v případě, že je `POST`příkaz HTTP. Přítomnost `IActionConstraint` `Edit(int, Product)` a "lepší" se shoduje s tím `Edit(int)`, že se `Edit(int, Product)` bude zkoušet jako první.

Budete muset psát pouze vlastní `IActionConstraint` implementace ve specializovaných scénářích, ale je důležité pochopit, že role `HttpPostAttribute` podobných atributů je definována pro jiné příkazy HTTP. V případě konvenčního směrování je běžné, že akce budou používat stejný název akce, i když jsou součástí `show form -> submit form` pracovního postupu. Pohodlí tohoto vzoru se po kontrole části [porozumění IActionConstraint](#understanding-iactionconstraint) projeví.

Pokud se více tras shodují a MVC nemůže najít nejlepší trasu, vyvolá výjimku `AmbiguousActionException`.

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

Názvy tras směrují logický název tak, aby se pojmenovaná trasa dala použít pro generování adresy URL. Tím se značně zjednodušuje vytváření adres URL, když řazení tras může způsobit komplikované generování adresy URL. Názvy tras musí být jedinečné aplikace v rozsahu.

Názvy tras nemají žádný vliv na adresy URL, které by odpovídaly ani manipulaci s požadavky. používají se pouze pro generování adresy URL. [Směrování](xref:fundamentals/routing) obsahuje podrobnější informace o generování adresy URL včetně generování adresy URL v pomocníkech specifických pro MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Směrování atributů

Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování. V následujícím příkladu `app.UseMvc();` se používá v `Configure` metodě a není předána žádná trasa. `HomeController` Bude odpovídat sadě adres URL podobně jako výchozí trasa `{controller=Home}/{action=Index}/{id?}` , která odpovídá:

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

`HomeController.Index()` Akce se spustí pro všechny cesty `/`URL, `/Home`nebo `/Home/Index`.

> [!NOTE]
> V tomto příkladu se zvýrazní klíčový rozdíl mezi směrováním atributů a konvenčním směrováním. Směrování atributů vyžaduje více vstupu pro určení trasy; konvenční výchozí trasa zpracovává trasy více stručně. Směrování atributů ale povoluje (a vyžaduje) přesnou kontrolu nad tím, které šablony směrování se vztahují na každou akci.

Při směrování atributů název kontroleru a názvy akcí nehraje **žádná** role, ve které je vybrána akce. Tento příklad bude odpovídat stejným adresám URL jako v předchozím příkladu.

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
> Výše uvedené šablony tras nedefinují parametry směrování pro `action`, `area`a `controller`. Ve skutečnosti nejsou tyto parametry trasy v trasách atributů povoleny. Vzhledem k tomu, že šablona trasy je již k akci přidružena, by nedošlo k tomu, že by bylo vhodné analyzovat název akce z adresy URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Směrování atributů pomocí atributů http [příkaz]

Směrování atributů může také používat `Http[Verb]` atributy, jako je například. `HttpPostAttribute` Všechny tyto atributy mohou přijmout šablonu směrování. Tento příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:

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

V případě cesty URL, `/products` jako `ProductsApi.ListProducts` je akce, se spustí, když je `GET` příkaz http `ProductsApi.CreateProduct` a spustí se, když je `POST`příkaz HTTP. Směrování atributů první odpovídá adrese URL oproti sadě šablon směrování definovaných atributy směrování. Po porovnání šablony trasy se použijí `IActionConstraint` omezení pro určení akcí, které lze provést.

> [!TIP]
> Při sestavování REST API je zřídka pravděpodobné, že budete chtít použít `[Route(...)]` metodu akce, protože akce bude přijímat všechny metody HTTP. Je lepší použít `Http*Verb*Attributes` konkrétnější a přesnější informace o tom, co vaše rozhraní API podporuje. Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.

Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy. V tomto příkladu `id` je vyžadována jako součást cesty URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

`ProductsApi.GetProduct(int)` Akce se spustí pro cestu URL, například `/products/3` ne pro cestu URL `/products`. Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .

## <a name="route-name"></a>Název trasy

Následující kód definuje *název trasy* `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Názvy tras se dají použít k vygenerování adresy URL na základě konkrétní trasy. Názvy tras nemají žádný vliv na adresu URL, která odpovídá chování směrování a používají se jenom pro generování adresy URL. Názvy tras musí být jedinečné aplikace v rozsahu.

> [!NOTE]
> Na rozdíl od konvenční *výchozí trasy*definuje `id` parametr jako volitelné (`{id?}`). Tato možnost přesného určení rozhraní API má výhody, jako je `/products` povolení `/products/5` a odeslání do různých akcí.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Kombinování tras

Aby bylo směrování atributů méně opakované, jsou atributy směrování na kontroleru kombinovány s atributy směrování na jednotlivých akcích. Každá šablona trasy definovaná na řadiči je předá směrování šablon na akcích. Umístěním atributu směrování na řadiči se vytvoří **všechny** akce v řadiči použít směrování atributů.

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

`/products` V tomto příkladu se cesta URL může shodovat `ProductsApi.ListProducts`a cesta `/products/5` k adrese URL se může `ProductsApi.GetProduct(int)`shodovat. Obě tyto akce odpovídají pouze HTTP `GET` , protože jsou označeny pomocí. `HttpGetAttribute`

Šablony směrování použité pro akci, která začíná `/` nebo `~/` není kombinována s šablonami směrování použitými pro kontroler. Tento příklad odpovídá sadě cest URL podobně jako *výchozí trasa*.

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

### <a name="ordering-attribute-routes"></a>Směrování atributů řazení

Na rozdíl od konvenčních tras, které se spouštějí v definovaném pořadí, směrování atributů vytvoří strom a vyhledá současně všechny trasy. To se chová jako – Pokud byly položky trasy umístěny v ideálním pořadí; nejvíce konkrétní trasy mají možnost provést před obecnější trasy.

Například trasa, jako `blog/search/{topic}` je konkrétnější, než trasa, jako `blog/{*article}`je třeba. Logicky mluví nejprve `blog/search/{topic}` Route "běh", protože se jedná o jediné řazení rozumné. Pomocí konvenčního směrování zodpovídá vývojář za umístění tras v požadovaném pořadí.

Trasy atributů mohou konfigurovat objednávku pomocí `Order` vlastnosti všech rozhraní, které poskytují atributy směrování. Trasy jsou zpracovávány podle vzestupného řazení `Order` vlastnosti. Výchozí pořadí je `0`. Nastavení trasy, která `Order = -1` se používá, se spustí před trasami, které nenastaví objednávku. Nastavování trasy `Order = 1` pomocí se spustí po výchozím řazení směrování.

> [!TIP]
> Nepoužívejte v závislosti `Order`. Pokud vaše adresa URL vyžaduje explicitní směrování hodnot pořadí, je pravděpodobné, že budou i u klientů matoucí. V části Obecné směrování atributů výběr správné trasy s odpovídající adresou URL. Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití `Order` vlastnosti.

RazorSměrování stránek a směrování kontroléru MVC sdílí implementaci. Informace o pořadí směrování v tématech Razor stránky jsou k dispozici na [ Razor stránkách směrování a konvence aplikace: pořadí směrování](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Nahrazení tokenu v šablonách směrování ([Controller], [Action], [oblast])

Pro usnadnění pohodlí trasy atributů podporují *Nahrazení tokenu* uzavřením tokenu do hranatých závorek`[`( `]`,). Tokeny `[action]`, `[area]`a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována. V následujícím příkladu akce odpovídají cestám adresy URL, jak je popsáno v komentářích:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů. Výše uvedený příklad se chová stejně jako následující kód:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Trasy atributů je také možné kombinovat s dědičností. To je zvlášť výkonné v kombinaci s náhradou tokenu.

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

Nahrazení tokenu platí také pro názvy tras definované směrováními atributů. `[Route("[controller]/[action]", Name="[controller]_[action]")]`vygeneruje jedinečný název trasy pro každou akci.

Chcete-li spárovat oddělovač nahrazení tokenu `[` literálu `]`nebo, zastavte ho opakováním`[[` znaku `]]`(nebo).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Použití transformátoru parametrů k přizpůsobení náhrady tokenu

Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů. Parametr Transformer implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů. Například vlastní `SlugifyParameterTransformer` parametr Transformer změní hodnotu `SubscriptionManagement` Route na. `subscription-management`

`RouteTokenTransformerConvention` Je konvence aplikačního modelu, která:

* Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.
* Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` Je zaregistrován jako možnost v `ConfigureServices`.

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

Směrování atributů podporuje definování více tras, které dosáhnou stejné akce. Nejběžnějším využitím je napodobení chování *výchozí konvenční trasy* , jak je znázorněno v následujícím příkladu:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Vložení více atributů trasy na řadič znamená, že každá z nich bude kombinována s každým z atributů tras na metodách akcí.

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

Pokud jsou na akci umístěny různé atributy `IActionConstraint`trasy (implementující), pak každé omezení akce kombinuje s šablonou směrování z atributu, který ji definoval.

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
> Zatímco používání více tras na akcích může vypadat jako účinné, je lepší zachovat jednoduché a jasně definované místo URL vaší aplikace. V případě potřeby použijte více tras na akcích, například pro podporu stávajících klientů.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Určení volitelných parametrů trasy atributu, výchozích hodnot a omezení

Trasy atributů podporují stejnou vloženou syntaxi jako konvenční trasy, aby bylo možné zadat volitelné parametry, výchozí hodnoty a omezení.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Vlastní atributy směrování pomocí`IRouteTemplateProvider`

Všechny atributy směrování, které jsou k dispozici v `[Route(...)]`rozhraní `[HttpGet(...)]` (, atd.) implementují `IRouteTemplateProvider` rozhraní. MVC vyhledává atributy tříd kontroleru a metody akcí při spuštění aplikace a používá ty, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.

Můžete implementovat `IRouteTemplateProvider` k definování vlastních atributů tras. Každá `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Atribut z výše uvedeného příkladu automaticky nastaví `Template` na hodnotu `"api/[controller]"` when při `[MyApiController]` použití.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Přizpůsobení tras atributů pomocí aplikačního modelu

*Aplikační model* je objektový model vytvořený při spuštění se všemi metadaty, které MVC používá ke směrování a provádění akcí. *Aplikační model* zahrnuje všechna data získaná z atributů trasy (prostřednictvím `IRouteTemplateProvider`). Můžete napsat *konvence* pro úpravu aplikačního modelu v době spuštění, abyste mohli přizpůsobit způsob, jakým se směrování chová. V této části se dozvíte jednoduchý příklad přizpůsobení směrování pomocí aplikačního modelu.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Kombinované směrování: směrování atributů vs. konvenční směrování

Aplikace MVC můžou kombinovat použití konvenčního směrování a směrování atributů. Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.

Akce jsou směrovány buď podle konvence nebo směrovaného atributu. Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT. Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak. **Libovolný** atribut směrování v řadiči provádí směrování všech akcí v atributu kontroleru.

> [!NOTE]
> Způsob, jakým se rozlišují dva typy systémů směrování, je proces, který se použije po tom, co adresa URL odpovídá šabloně směrování. V části konvenční směrování se při výběru akce a kontroleru z vyhledávací tabulky všech konvenčních akcí směrování používají hodnoty tras z shody. V směrování atributů je Každá šablona již přidružena k akci a není nutné žádné další vyhledávání.

## <a name="complex-segments"></a>Komplexní segmenty

Komplexní segmenty (například `[Route("/dog{token}cat")]`) jsou zpracovávány porovnáním koncových literálů zprava doleva nehladým způsobem. Popis najdete [ve zdrojovém kódu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) . Další informace najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Generování adresy URL

Aplikace MVC můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL. Generování adres URL eliminuje adresy URL zakódujeme, takže je váš kód robustnější a udržovatelný. Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a bude se týkat jenom základních informací o tom, jak generování adresy URL funguje. Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .

`IUrlHelper` Rozhraní je základní částí infrastruktury mezi MVC a směrováním pro generování adresy URL. `IUrlHelper` K dispozici je instance dostupná prostřednictvím `Url` vlastnosti v části řadiče, zobrazení a zobrazení komponent.

V tomto příkladu se `IUrlHelper` rozhraní používá přes `Controller.Url` vlastnost k vygenerování adresy URL jiné akci.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Pokud aplikace používá výchozí konvenční trasu, hodnota `url` proměnné bude řetězec `/UrlGeneration/Destination`cesty adresy URL. Tato cesta URL se vytvoří směrováním kombinováním hodnot tras z aktuální žádosti (okolní hodnoty) s hodnotami předanými do `Url.Action` a nahrazením těchto hodnot do šablony trasy:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami. Parametr trasy, který nemá hodnotu, může používat výchozí hodnotu, pokud má jednu, nebo může být vynechána, pokud je volitelná (jako v případě `id` v tomto příkladu). Generování adresy URL selže, pokud žádný požadovaný parametr trasy nemá odpovídající hodnotu. Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.

`Url.Action` Výše uvedený příklad předpokládá konvenční směrování, ale generování adresy URL funguje podobně jako při směrování atributů, i když se koncepty liší. Při použití konvenčního směrování se hodnoty tras používají k rozšíření šablony a hodnoty tras pro `controller` a `action` obvykle se v této šabloně zobrazují – to funguje, protože adresy URL, které odpovídají směrování, dodržují *konvence*. V případě směrování atributů se v šabloně nemůžou zobrazovat hodnoty tras pro `controller` a `action` , které se používají k vyhledání šablony, která se má použít.

V tomto příkladu se používá směrování atributů:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC sestaví vyhledávací tabulku všech akcí směrovaného atributu a bude odpovídat `controller` hodnotám a `action` vybrat šablonu směrování, která se má použít pro generování adresy URL. V ukázce výše `custom/url/to/destination` je vygenerována.

### <a name="generating-urls-by-action-name"></a>Generování adres URL podle názvu akce

`Url.Action` (`IUrlHelper` . `Action`) a všechna související přetížení jsou založena na této myšlence, se kterou chcete určit, k čemu se odkazujete zadáním názvu kontroleru a názvu akce.

> [!NOTE]
> Při použití `Url.Action`aplikace jsou aktuální hodnoty trasy pro `controller` a `action` určeny `controller` pro vás – hodnota a `action` jsou součástí obou *okolních hodnot* **a** *hodnot*. Metoda `Url.Action`vždy používá aktuální hodnoty `action` a `controller` a generuje cestu URL, která směruje na aktuální akci.

Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které jste nezadali při generování adresy URL. Směrování má dostatek informací `{a}/{b}/{c}/{d}` , aby vygenerovala adresu URL bez dalších hodnot, a to s využitím trasy jako a okolních hodnot `{ a = Alice, b = Bob, c = Carol, d = David }`– protože všechny parametry tras mají hodnotu. Pokud jste přidali hodnotu `{ d = Donovan }`, hodnota `{ d = David }` by byla ignorována a vygenerovaná cesta URL bude. `Alice/Bob/Carol/Donovan`

> [!WARNING]
> Cesty URL jsou hierarchické. Pokud jste v předchozím příkladu přidali hodnotu `{ c = Cheryl }`, obě hodnoty `{ c = Carol, d = David }` budou ignorovány. V tomto případě už nemusíte mít hodnotu `d` a generování adresy URL selže. Je nutné zadat požadovanou hodnotu `c` a. `d`  Můžete očekávat, že se tento problém bude nacházet s výchozí`{controller}/{action}/{id?}`trasou (), ale v praxi se toto chování bude `Url.Action` chovat zřídka, jako vždycky `controller` explicitně `action` zadáte hodnotu a.

Delší přetížení `Url.Action` také přijímají další objekt *hodnoty trasy* , který poskytuje hodnoty pro parametry směrování jiné než `controller` a. `action` Nejčastěji se to zobrazí s `id` podobným způsobem. `Url.Action("Buy", "Products", new { id = 17 })` Podle konvence objekt *hodnoty trasy* je obvykle objektem anonymního typu, ale může to být také objekt `IDictionary<>` typu "nebo" v *podobě prostého starého objektu .NET*. Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Chcete-li vytvořit absolutní adresu URL, použijte přetížení, které `protocol`přijímá:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generování adres URL podle směrování

Výše uvedený kód ukázal generování adresy URL předáním do kontroleru a názvu akce. `IUrlHelper`poskytuje také `Url.RouteUrl` rodinu metod. Tyto metody jsou podobné `Url.Action`, ale nekopírují aktuální hodnoty `action` a `controller` do hodnot tras. Nejběžnějším využitím je zadání názvu trasy pro použití konkrétní trasy pro vygenerování adresy URL, a to obecně *bez* zadání kontroleru nebo názvu akce.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generování adres URL v HTML

`IHtmlHelper``HtmlHelper` poskytuje `Html.BeginForm` metody `Html.ActionLink` a ke generování `<form>` a `<a>` prvkům v uvedeném pořadí. Tyto metody používají `Url.Action` metodu pro VYGENEROVÁNÍ adresy URL a přijímající podobné argumenty. `Url.RouteUrl` Doprovodníci pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` mají podobné funkce.

TagHelpers generuje adresy URL prostřednictvím `form` taghelperu a `<a>` taghelperu. Oba tyto použití `IUrlHelper` při jejich implementaci. Další informace najdete v tématu [práce s formuláři](../views/working-with-forms.md) .

Uvnitř zobrazení `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnosti pro jakoukoli generaci adres URL ad hoc, která není pokrytá výše.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generování adres URL ve výsledcích akcí

Výše uvedené příklady ukazují použití `IUrlHelper` v kontroleru, zatímco nejběžnější využití v řadiči je VYGENEROVÁNÍ adresy URL jako součást výsledku akce.

Základní `ControllerBase` třídy `Controller` a poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci. Jedním z typických použití je přesměrování po přijetí vstupu uživatele.

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

Metody pro vytváření výsledků pro zpracování akcí dodrží podobný vzor pro metody `IUrlHelper`na.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Zvláštní případ pro vyhrazené konvenční trasy

Konvenční směrování může používat speciální druh definice trasy, která se označuje jako *vyhrazená konvenční trasa*. V následujícím příkladu je trasa s názvem `blog` vyhrazená konvenční trasa.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Pomocí těchto definic `Url.Action("Index", "Home")` tras se vygeneruje cesta `/` URL s `default` trasou, ale proč? Můžete odhadnout, že hodnoty `{ controller = Home, action = Index }` tras by byly dostatečné pro VYGENEROVÁNÍ adresy URL `blog`pomocí a výsledek by byl `/blog?action=Index&controller=Home`.

Vyhrazené konvenční trasy spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla "příliš hlade" s generováním adresy URL. V tomto případě výchozí hodnoty jsou `{ controller = Blog, action = Article }`a `controller` ani `action` se neobjeví jako parametr trasy. Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám. Generování adresy URL `blog` pomocí se nezdaří, protože `{ controller = Home, action = Index }` se hodnoty `{ controller = Blog, action = Article }`neshodují. Směrování pak vrátí zpět k akci `default`, která bude úspěšná.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Oblasti

[Oblasti](areas.md) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů směrování (pro akce kontroleru) a struktury složek (pro zobrazení). Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé *oblasti*. Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru směrování `area` do `controller` a. `action` V této části se dozvíte, jak směrování komunikuje s oblastmi – viz [oblasti](areas.md) pro podrobnosti o použití oblastí se zobrazeními.

V následujícím příkladu je nakonfiguruje MVC na použití výchozí konvenční trasy a *trasy oblasti* pro oblast s názvem `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Při porovnání cesty URL jako `/Manage/Users/AddUser`se v první trase vytvoří hodnoty `{ area = Blog, controller = Users, action = AddUser }`trasy. Hodnota `area` trasy je vytvořena výchozí hodnotou pro `area`, ve skutečnosti je trasa vytvořená pomocí `MapAreaRoute` následujícího ekvivalentu:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`Vytvoří trasu s použitím výchozí hodnoty a omezení pro `area` použití poskytnutého názvu oblasti v tomto případě. `Blog` Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }`, omezení vyžaduje hodnotu `{ area = Blog, ... }` pro generování adresy URL.

> [!TIP]
> Konvenční směrování je závislé na pořadí. Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.

Pomocí výše uvedeného příkladu budou hodnoty směrování odpovídat následující akci:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

V `AreaAttribute` této části je uvedeno, že kontroler je v rámci oblasti, říkáme, že tento kontroler je `Blog` v oblasti. Řadiče bez `[Area]` atributu nejsou členy žádné oblasti **a nebudou se shodovat s** tím, kdy je hodnota `area` směrování poskytována směrováním. V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám `{ area = Blog, controller = Users, action = AddUser }`tras.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Obor názvů každého kontroleru se tady zobrazuje pro úplnost – jinak by řadiče vyžadovaly konflikt názvů a generovaly chybu kompilátoru. Obory názvů třídy nemají žádný vliv na směrování MVC.

První dva řadiče jsou členy oblastí a odpovídají pouze v případě, že je jejich název příslušné oblasti poskytovaný hodnotou `area` trasy. Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že směrováním neposkytuje žádnou hodnotu `area` .

> [!NOTE]
> V souvislosti s neshodnou *hodnotou*je absence `area` hodnoty stejná, jako kdyby hodnota `area` null nebo prázdný řetězec.

Při provádění akce uvnitř oblasti bude hodnota trasy pro `area` směrování k dispozici jako *ambientní hodnota* pro směrování, která se má použít pro generování adresy URL. To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Principy IActionConstraint

> [!NOTE]
> Tato část je hlubokým podrobněem interních platforem a způsobu, jakým MVC vybírá akci, která se má provést. Typická aplikace nebude potřebovat vlastní.`IActionConstraint`

Pravděpodobně jste již použili `IActionConstraint` , i když nejste obeznámeni s rozhraním. `[HttpGet]` Atribut a podobné `[Http-VERB]` atributy implementují `IActionConstraint` , aby bylo možné omezit provádění metody akce.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Za předpokladu, že výchozí konvenční trasa, `/Products/Edit` cesta URL by vytvořila hodnoty `{ controller = Products, action = Edit }`, které by odpovídaly **oběma** akcím, které jsou zde uvedeny. V `IActionConstraint` terminologii bychom znamenali, že obě tyto akce se považují za kandidáty, protože obě odpovídají datům směrování.

Když se `HttpGetAttribute` spustí, znamená to, že *Edit ()* je odpovídající pro *Get* a neshoduje se s žádným jiným příkazem http. `Edit(...)` Akce nemá definovány žádné omezení, a proto bude odpovídat jakémukoli příkazu http. Proto za předpokladu `POST` , `Edit(...)` že odpovídají pouze. V případě, že `GET` obě akce stále můžou souhlasit, ale akce s `IActionConstraint` je vždy považována za *lepší* , než je akce bez. Proto je `Edit()` to `[HttpGet]` proto, že se považuje za konkrétnější a vybere se, pokud se obě akce můžou shodovat.

Koncepčně `IActionConstraint` je forma *přetížení*, ale místo přetížení metod se stejným názvem, je přetížení mezi akcemi, které odpovídají stejné adrese URL. Směrování atributů používá `IActionConstraint` také a může mít za následek, že se z různých řadičů považují za kandidáty.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementace IActionConstraint

Nejjednodušší způsob, jak implementovat, `IActionConstraint` je vytvořit třídu odvozenou z `System.Attribute` a umístit ji na vaše akce a řadiče. MVC bude automaticky zjišťovat všechny `IActionConstraint` , které jsou aplikovány jako atributy. Pomocí modelu aplikace můžete použít omezení, což je pravděpodobně nejpružnější přístup, protože umožňuje Metaprogram způsob jejich použití.

V následujícím příkladu omezení zvolí akci na základě *kódu země* z údajů o trasách. [Úplná ukázka na GitHubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

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

Zodpovídáte za implementaci `Accept` metody a výběru možnosti Order, aby bylo omezení spuštěno. V tomto případě se `Accept` metoda vrátí `true` k označení, že akce odpovídá hodnotě `country` trasy. To se liší od a `RouteValueAttribute` v tom, že umožňuje přechod na akci bez atributu. Ukázka ukazuje, že pokud zadáte `en-US` akci, směrové číslo země jako `fr-FR` se vrátí k obecnější kontroleru, který se `[CountrySpecific(...)]` nepoužívá.

Vlastnost určuje, ve které fázi je omezení součástí. *stage* `Order` Omezení akcí se spouští ve skupinách na základě `Order`. Například všechny atributy, které poskytuje rozhraní HTTP, používají stejnou `Order` hodnotu tak, aby běžely ve stejné fázi. Můžete mít tolik fází, kolik potřebujete k implementaci požadovaných zásad.

> [!TIP]
> Chcete-li se rozhodnout o `Order` hodnotě pro zamyslení nad tím, zda by mělo být vaše omezení použito před metodami http. Nižší čísla se spouštějí jako první.

::: moniker-end
