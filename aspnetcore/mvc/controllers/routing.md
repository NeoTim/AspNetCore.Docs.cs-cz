---
title: Směrování na akce kontroleru v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ASP.NET Core MVC používá směrování Middleware podle adresy URL příchozích událostí požadavků a jejich namapování na akce.
ms.author: riande
ms.date: 01/24/2019
uid: mvc/controllers/routing
ms.openlocfilehash: b4d5cd3add3fda6b70873eb5cce1dcee651f9185
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087507"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Směrování na akce kontroleru v ASP.NET Core

Podle [Ryanem Nowak](https://github.com/rynowak) a [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core MVC používá směrování [middleware](xref:fundamentals/middleware/index) podle adresy URL příchozích událostí požadavků a jejich namapování na akce. Směrování je definováno v kódu při spuštění nebo atributy. Postupy popisují, jak cesty adresy URL by měla odpovídat akce. Trasy se také používají k vygenerování adres URL (pro odkazy), odeslání odpovědi.

Akce jsou buď konvenčně směrovat nebo atribut směrovat. Umístění trasy na kontroler nebo akce umožňuje směrovat atribut. Zobrazit [smíšené směrování](#routing-mixed-ref-label) Další informace.

Tento dokument vysvětluje interakce mezi MVC a směrování a jak typické zpřístupnění aplikace MVC pomocí funkce směrování. Zobrazit [směrování](xref:fundamentals/routing) podrobnosti o pokročilé směrování.

## <a name="setting-up-routing-middleware"></a>Nastavení směrování middlewaru

Ve vaší *konfigurovat* metoda může se zobrazit podobný kód:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Uvnitř volání `UseMvc`, `MapRoute` slouží k vytvoření jedné trasy, které budete označujeme jako `default` trasy. Většina aplikací MVC použije trasu pomocí šablony podobně jako `default` trasy.

Šablona trasy `"{controller=Home}/{action=Index}/{id?}"` odpovídá cesty adresy URL jako `/Products/Details/5` extrahuje hodnoty trasy `{ controller = Products, action = Details, id = 5 }` podle tokenizaci cestu. MVC se pokusí najít kontroler s názvem `ProductsController` a spustit akci `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Všimněte si, že v tomto příkladu by vazby modelu použijte hodnotu `id = 5` nastavit `id` parametr `5` při vyvolání této akce. Zobrazit [vazby modelu](../models/model-binding.md) další podrobnosti.

Použití `default` trasy:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Šablona trasy:

* `{controller=Home}` definuje `Home` jako výchozí `controller`

* `{action=Index}` definuje `Index` jako výchozí `action`

* `{id?}` definuje `id` jako volitelné

Výchozí a volitelné trasy parametry nemusí být k dispozici v cestě adresy URL pro shodu. V tématu [trasy referenčními informacemi k šablonám](../../fundamentals/routing.md#route-template-reference) podrobný popis syntaxe šablon trasy.

`"{controller=Home}/{action=Index}/{id?}"` odpovídá cestě adresy URL `/` a hodnoty trasy `{ controller = Home, action = Index }`. Hodnoty pro `controller` a `action` provést pomocí výchozích hodnot `id` nevytvoří hodnotu, protože neexistuje žádný odpovídající segment v cestě adresy URL. MVC využije tyto hodnoty trasy k výběru `HomeController` a `Index` akce:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Pomocí tohoto kontroleru definice a šablonu trasy `HomeController.Index` akce by byl proveden pro některý z následujících cest URL:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Metoda pohodlí `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Je možné nahradit:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` a `UseMvcWithDefaultRoute` přidat instanci `RouterMiddleware` do kanálu middlewaru. MVC nebude pracovat přímo s middlewarem a používá směrování pro zpracování požadavků. MVC je připojené k trasám prostřednictvím instance `MvcRouteHandler`. Kód uvnitř `UseMvc` je podobný následujícímu:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` přímo nedefinuje všechny trasy, se přidá do kolekce tras pro zástupný symbol `attribute` trasy. Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a také podporuje směrováním atributů.  `UseMvc` a všechny jeho variant přidá zástupný symbol pro atribut trasy – směrování atributů je vždy k dispozici bez ohledu na to, jak nakonfigurovat `UseMvc`. `UseMvcWithDefaultRoute` Definuje výchozí trasu a podporuje směrováním atributů. [Směrováním atributů](#attribute-routing-ref-label) část obsahuje další podrobnosti o směrování atributů.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Konvenční směrování

`default` Trasy:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Příkladem je *konvenční směrování*. Označujeme je jako tento styl *konvenční směrování* protože navazuje *konvence* pro cesty adresy URL:

* první segment cesty se mapuje na název řadiče

* Druhá mapuje na název akce.

* třetí segmentu se používá pro volitelný `id` slouží k mapování na modelu entity

Použití této funkce `default` trasy, URL path `/Products/List` mapuje `ProductsController.List` akce, a `/Blog/Article/17` mapuje na `BlogController.Article`. Toto mapování je podle názvu kontroleru a akce **pouze** a není založena na obory názvů, umístění zdrojových souborů nebo parametry metody.

> [!TIP]
> Použití konvenční směrování s výchozí trasa umožňuje rychle vytvářet aplikace bez nutnosti a Navrhněte nové vzor adresy URL pro každou akci, kterou definujete. Aplikace s akcemi CRUD styl s konzistence adres URL ve vašich řadičů může pomoct zjednodušit kód a ujistěte se, uživatelské rozhraní předvídatelnější.

> [!WARNING]
> `id` Je definován jako volitelné šablonu trasy, což znamená, že vaše akce můžete provést bez ID zadané jako část adresy URL. Obvykle co se stane, pokud `id` vynecháte z adresy URL je, že bude nastavena `0` navázáním modelu a jako výsledek žádná entita se nenašel v odpovídající databázi `id == 0`. Směrování atributů vám může velice přesně kontrolovat, aby je vyžadováno pro některé akce a nikoli pro jiné ID. Podle konvence zahrne do dokumentace, jako jsou volitelné parametry `id` když že se pravděpodobně se zobrazí v správné použití.

## <a name="multiple-routes"></a>Několik tras

Můžete přidat několik tras uvnitř `UseMvc` tak, že přidáte další volání `MapRoute`. To umožňuje definovat více konvence, nebo přidat konvenční trasy, které jsou vyhrazeny pro určité akce, jako například:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`blog` Trasy tady *vyhrazené konvenční trasy*, to znamená, že používá konvenční směrování systému, ale je vyhrazen pro určité akce. Protože `controller` a `action` nejsou zobrazeny v šabloně trasy jako parametry, můžou mít jenom výchozí hodnoty a proto se tato trasa vždy mapují na akci `BlogController.Article`.

Trasy v kolekci tras jsou seřazené a budou zpracovány v pořadí, ve kterém se přidají. Ano, v tomto příkladu `blog` trasy, vyzkouší se před `default` trasy.

> [!NOTE]
> *Vyhrazené konvenční trasy* často používají parametry trasy pokrývající vše jako `{*article}` zachycení zbývající část cesty URL. Díky tomu se trasy 'příliš greedy' to znamená, že se shoduje adresy URL, které je určený k porovnání s jiným trasám. Umístěte "greedy" trasy později ve směrovací tabulce tento problém vyřešit.

### <a name="fallback"></a>Použití náhradní lokality

V rámci zpracování žádosti se bude ověřovat MVC, hodnoty trasy je možné najít kontroleru a akce ve vaší aplikaci. Pokud se hodnoty trasy neshodují akci pak není trasy považovány za shodné a vyzkouší se další trasy. Tento postup se nazývá *záložní*, a je určena ke zjednodušení případy, kdy konvenční trasy překrývat.

### <a name="disambiguating-actions"></a>Odstraňování akce

Když dvě akce odpovídají prostřednictvím směrování, musí na tlačítko "nejlepší" Release candidate, jinak se vyvolat výjimku rozlišení MVC. Příklad:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Tento kontroler definuje dvě akce, která by odpovídala cestě adresy URL `/Products/Edit/17` a směrovat data `{ controller = Products, action = Edit, id = 17 }`. Toto je typický vzor pro kontrolery MVC kde `Edit(int)` ukazuje formulář pro úpravy produktu, a `Edit(int, Product)` zpracovává odeslaného formuláře. Aby to bylo možné vytvořit zvolte MVC třeba `Edit(int, Product)` při požadavku HTTP `POST` a `Edit(int)` po cokoli, je příkazem HTTP příkaz.

`HttpPostAttribute` ( `[HttpPost]` ) Je implementace `IActionConstraint` akce k volbě, když je příkazem HTTP příkaz, který vám umožní pouze `POST`. Přítomnost `IActionConstraint` díky `Edit(int, Product)` shodovat lepší než `Edit(int)`, takže `Edit(int, Product)` vyzkouší se napřed.

Je potřeba pouze napsat vlastní `IActionConstraint` implementace specializované scénáře, ale je důležité pochopit role atributů, jako je `HttpPostAttribute` – podobně jako atributy jsou definovány pro jiné příkazy HTTP. V konvenční směrování je běžné akce, které používají stejný název akce v případě, že se součást `show form -> submit form` pracovního postupu. Výhodou tohoto modelu se stane po kontrole zřetelnější [Principy IActionConstraint](#understanding-iactionconstraint) oddílu.

Pokud několik tras odpovídá a MVC nelze najít "nejlepší" směrování, vyvolá výjimku `AmbiguousActionException`.

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

Názvy tras poskytují trasy logický název tak, aby pojmenovanou trasu lze použít pro generování adresy URL. To výrazně zjednodušuje vytvoření adresy URL při řazení trasy dokonce vytvářet složité generování adresy URL. Názvy tras musí být jedinečný pro celou aplikaci.

Názvy tras mít žádný vliv na adrese URL odpovídající nebo zpracování požadavků. slouží pouze pro generování adresy URL. [Směrování](xref:fundamentals/routing) obsahuje podrobnější informace o generování adresy URL včetně generování adresy URL v pomocné rutiny specifické pro MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Směrování atributů

Směrování atributů používá sadu atributů mapování akcí přímo do šablon trasy. V následujícím příkladu `app.UseMvc();` je používán `Configure` je předán metodě a žádná trasa. `HomeController` Bude odpovídat sadu podobný postupu výchozí adresy URL `{controller=Home}/{action=Index}/{id?}` odpovídají:

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

`HomeController.Index()` Akce se provede pro všechny cesty adresy URL `/`, `/Home`, nebo `/Home/Index`.

> [!NOTE]
> Tento příklad ukazuje klíčové programovací rozdíl mezi konvenční směrování a směrování atributů. Směrování atributů vyžaduje další vstup k určení postupu; konvenční výchozí trasu zpracovává více stručně trasy. Ale směrováním atributů umožňuje (a vyžaduje) přesné řízení se vztahuje na každou akci šablon trasy.

Se směrováním, názvu kontroleru a akce názvy atributů Přehrát **žádné** role, ve které je vybrané akce. V tomto příkladu bude odpovídat stejné adresy URL jako v předchozím příkladu.

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
> Výše uvedené šablony trasy nebudete definovat parametry trasy pro `action`, `area`, a `controller`. Ve skutečnosti nejsou povolené tyto parametry trasy v atribut trasy. Vzhledem k tomu, že šablona trasy je již spojen s akcí, to by nedávalo smysl parsovat název akce z adresy URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Atribut, směrování pomocí protokolu Http [příkaz] atributy

Směrování atributů lze také nastavit využívání `Http[Verb]` atributy, jako `HttpPostAttribute`. Všechny tyto atributy můžete přijmout šablonu trasy. Tento příklad ukazuje dvě akce, které odpovídají stejnou šablonu trasy:

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

Pro cestu adresy URL jako `/products` `ProductsApi.ListProducts` akce se provede, když je příkazem HTTP příkaz `GET` a `ProductsApi.CreateProduct` se provede, když je příkazem HTTP příkaz `POST`. Nejprve směrováním atributů odpovídá adrese URL vůči sadu šablon trasy definované atributy trasy. Jakmile odpovídá šablonu trasy `IActionConstraint` omezení se použijí k určení akce, které mohou být provedeny.

> [!TIP]
> Při sestavování rozhraní REST API, není obvyklé, že budete chtít použít `[Route(...)]` na metodu akce. Je vhodnější použít další konkrétní `Http*Verb*Attributes` abychom byli přesní o tom, co vaše rozhraní API podporuje. Očekává se, že klienti rozhraní REST API vědět, co cesty a příkazy HTTP se mapují na konkrétní logické operace.

Protože trasa atributu, platí pro konkrétní akci, je snadné vytvořit parametry požadované jako součást definice šablony trasy. V tomto příkladu `id` je vyžadován jako součást cesty URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

`ProductsApi.GetProduct(int)` Akce se provede pro cestu adresy URL jako `/products/3` , ale ne pro cestu adresy URL jako `/products`. Zobrazit [směrování](../../fundamentals/routing.md) úplný popis šablony trasy a související možnosti.

## <a name="route-name"></a>Název trasy

Následující kód definuje *trasy název* z `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Názvy tras lze použít ke generování adresy URL na základě konkrétní trasy. Názvy tras mít vliv na porovnávání chování směrování adres URL a používají pouze pro generování adresy URL. Názvy tras musí být jedinečný pro celou aplikaci.

> [!NOTE]
> Rozdíl oproti to běžné *výchozí trasa*, která definuje `id` jako volitelný parametr (`{id?}`). Tato možnost přesně určit rozhraní API má výhody, jako je například povolení `/products` a `/products/5` odeslat na různé akce.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Kombinování trasy

Chcete-li směrováním atributů méně opakované, atributů tras na řadiči spolu se atributy trasy na jednotlivé akce. Šablony směrování na akce, které jsou před žádné šablony trasy definované v kontroleru. Atribut trasy umístění na kontroleru díky **všechny** akce v kontroleru pomocí směrování atributů.

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

V tomto příkladu cesty URL `/products` odpovídá `ProductsApi.ListProducts`a cesta URL `/products/5` odpovídá `ProductsApi.GetProduct(int)`. Obě tyto akce HTTP odpovídá pouze `GET` vzhledem k tomu, že upravené pomocí `HttpGetAttribute`.

Směrovat šablony u akce, která začínají `/` nebo `~/` není spojit se použijí pro kontroler, šablon trasy. Tento příklad porovná sadu cest URL podobně jako *trasy výchozí*.

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

### <a name="ordering-attribute-routes"></a>Pořadí trasy atributů

Na rozdíl od běžných trasy, které se spustí v zadaném pořadí směrování atributů sestavení stromu a současně odpovídá všechny trasy. To se chová jako-li položky trasy byly umístěny do ideální má za výsledek řazení; nejspecifičtější trasy mít možnost spustit před obecnější trasy.

Třeba jako trasu `blog/search/{topic}` je konkrétnější než trasa jako `blog/{*article}`. Logicky mluvený `blog/search/{topic}` trasy "", nejprve ve výchozím nastavení spustí, protože se jedná pouze rozumné řazení. Použití konvenční směrování, vývojář je odpovědná za umístění trasy do požadovaného pořadí.

Atribut trasy můžete nakonfigurovat pořadí, pomocí `Order` vlastnost všechny atributy rámec poskytovaný trasy. Trasy se zpracovávají podle vzestupném ze na `Order` vlastnost. Výchozí pořadí je `0`. Nastavení směrování pomocí `Order = -1` bude spuštěn před trasy, které nemají nastavený objednávky. Nastavení směrování pomocí `Order = 1` se spustí po změně pořadí výchozí trasy.

> [!TIP]
> Vyhněte se v závislosti na `Order`. Pokud váš prostor adresy URL vyžaduje explicitní seřazení hodnot pro směrování správně, je pravděpodobně matoucí také klientům. Směrování atributů obecně bude vyberte správné směrování s odpovídajícími adresy URL. Pokud nefunguje výchozí pořadí použili pro generování adresy URL, pomocí názvu trasy, je obvykle jednodušší než použití přepsání `Order` vlastnost.

Stránky Razor směrování a směrování sdílení řadiče MVC implementace. Informace o pořadí trasy v tématech pro stránky Razor je k dispozici na [trasy a aplikační konvence pro stránky Razor: Pořadí trasy](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Token nahrazení v šablonách tras ([kontroler], [action] [Oblast])

Pro usnadnění práce trasy atributů podporují *token nahrazení* uzavřením token do hranatých závorek (`[`, `]`). Tokeny `[action]`, `[area]`, a `[controller]` se nahradí hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je definován trasy. V následujícím příkladu akce odpovídat cesty adresy URL, jak je popsáno v komentářích:

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Vyvolá se v posledním kroku vytváření trasy atributů náhradních tokenů. Výše uvedeném příkladu se bude chovat stejně jako následující kód:

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Atribut trasy můžete také kombinovat s dědičnosti. To je zvláště efektivní, v kombinaci s náhradních tokenů.

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

Nahrazování tokenů platí také pro názvy tras definován atribut trasy. `[Route("[controller]/[action]", Name="[controller]_[action]")]` vygeneruje název jedinečný trasa pro každou akci.

Tak, aby odpovídaly oddělovač literální nahrazení tokenu `[` nebo `]`, řídicí znak opakováním (`[[` nebo `]]`).

::: moniker range=">= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Použití transformeru parametr k přizpůsobení náhradních tokenů

Použití transformeru parametr, je možné přizpůsobit náhradních tokenů. Parametr transformer implementuje `IOutboundParameterTransformer` a transformuje hodnoty parametrů. Například vlastní `SlugifyParameterTransformer` parametr transformer změny `SubscriptionManagement` trasy hodnota, která má `subscription-management`.

`RouteTokenTransformerConvention` Je vytváření modelu aplikace, které:

* Parametr transformer platí pro všechny trasy atributů v aplikaci.
* Přizpůsobí atribut token hodnoty trasy jako se nahradí.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` Je zaregistrovaný jako možnost v `ConfigureServices`.

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

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Několik tras

Atribut směrování podporuje určení několik tras, které jsou poskytovány stejnou akci. Nejběžnější použití tohoto objektu je tak, aby napodoboval chování *výchozí trasa konvenční* jak je znázorněno v následujícím příkladu:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Uvedení několika atributů tras na řadiči znamená, že každé z nich bude kombinovat s každého z atributů trasy na metody akce.

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

Po několika atributů tras (, které implementují `IActionConstraint`) jsou umístěné na akci, pak každá akce omezení spojuje se šablona trasy z atributu, který je definován.

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
> Při použití více tras na akce se může zdát výkonné, je lepší byly místo adresy URL vaší aplikace, jednoduché a dobře definovaný. Použijte několik tras na akce, pouze v případě potřeby, například k podpoře existujících klientů.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Určení atribut trasy volitelné parametry, výchozí hodnoty a omezení

Atribut trasy podporují stejné vložená syntaxe jako konvenční trasy a určit volitelné parametry, výchozí hodnoty a omezení.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

V tématu [trasy referenčními informacemi k šablonám](../../fundamentals/routing.md#route-template-reference) podrobný popis syntaxe šablon trasy.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Vlastní trasy atributů s použitím `IRouteTemplateProvider`

Všechny atributy trasy k dispozici v rámci ( `[Route(...)]`, `[HttpGet(...)]` atd) implementace `IRouteTemplateProvider` rozhraní. MVC hledá atributy u třídy kontroleru a metody akce při spuštění aplikace a používá ty, které implementují `IRouteTemplateProvider` vytvářet počáteční sadu trasy.

Můžete implementovat `IRouteTemplateProvider` definovat vlastní atributy trasy. Každý `IRouteTemplateProvider` vám umožní definovat jednu trasu šablonou vlastní trasy, pořadí a název:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Atribut z výše uvedeném příkladu se automaticky nastaví `Template` k `"api/[controller]"` při `[MyApiController]` platí.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Přizpůsobení trasy atributů pomocí aplikačního modelu

*Aplikační model* je vytvořen při spuštění se všemi metadat používané MVC pro směrování a provádění akcí objektový model. *Aplikační model* zahrnuje všechna data shromážděná z atributů tras (prostřednictvím `IRouteTemplateProvider`). Můžete napsat *konvence* upravit aplikační model v době spuštění k přizpůsobení chování směrování. Tato část ukazuje jednoduchý příklad přizpůsobení, směrování pomocí aplikačního modelu.

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Smíšené směrování: Atribut směrování vs konvenční směrování

Aplikace MVC můžete kombinovat použití konvenční směrování a směrování atributů. Je typické použití konvenční trasy pro kontrolery obsluhující stránky HTML pro prohlížeče, a atribut směrování pro kontrolery slouží rozhraní REST API.

Akce jsou buď konvenčně směrovat nebo atribut směrovat. Umístění trasy na kontroler nebo akce umožňuje směrovat atribut. Akce, které definují trasy atributů není dostupný prostřednictvím konvenční trasy a naopak. **Žádné** atribut trasy na řadiči provede všechny akce v kontroleru atribut směrovat.

> [!NOTE]
> Co rozlišuje dva typy směrování systémů je proces použijí po adresa URL odpovídá šablonu trasy. V konvenční směrování, se používají hodnoty trasy ze shody lze vybírat vyhledávací tabulky všech akcí konvenční směrované akce a kontroler. V směrování atributů, každá šablona je již přidružen akce a je potřeba žádné další vyhledávání.

## <a name="complex-segments"></a>Komplexní segmenty

Komplexní segmentech (například `[Route("/dog{token}cat")]`), se zpracovávají to provede spárováním odpovídajících nahoru literály zprava doleva bez metody greedy způsobem. Zobrazit [zdrojový kód](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) popis. Další informace najdete v tématu [tento problém](https://github.com/aspnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Generování adresy URL

Aplikace MVC slouží ke generování adresy URL odkazů na akce směrování pro funkce generování adresy URL. Generování adresy URL eliminuje hardcoding adresy URL, provádění kódu, robustní a udržovatelný. Tato část se zaměřuje na Funkce generování adresy URL poskytnuté MVC a bude pouze zabývat základy fungování generování adresy URL. Zobrazit [směrování](../../fundamentals/routing.md) podrobný popis generování adresy URL.

`IUrlHelper` Rozhraní je základní část infrastruktury mezi MVC a směrování pro generování adresy URL. Zjistíte instance `IUrlHelper` k dispozici prostřednictvím `Url` vlastnost v řadiči, zobrazení a zobrazení komponenty.

V tomto příkladu `IUrlHelper` rozhraní se používá prostřednictvím `Controller.Url` vlastnost ke generování adresy URL pro jiná akce.

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Pokud aplikace používá výchozí konvenční směrovat, hodnota `url` proměnná bude řetězec cesty adresy URL `/UrlGeneration/Destination`. Tato cesta URL je vytvořené směrování kombinací hodnot trasy z aktuální žádosti (okolí hodnoty), obsahuje hodnotu předanou do `Url.Action` a nahraďte hodnoty v šabloně trasy:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Každý parametr trasa v šabloně trasy je jeho hodnota nahradí odpovídající názvy s hodnotami a okolí hodnoty. Parametr trasy, která nemá hodnotu můžete použít výchozí hodnotu, pokud má jeden, nebo přeskočit, pokud je volitelný (stejně jako v případě třídy `id` v tomto příkladu). Generování adresy URL se nezdaří, pokud všechny požadované trasy parametr nemá odpovídající hodnotu. Pokud selže generování adresy URL pro trasu, zkusí se další směrování, dokud vyzkoušeny všechny trasy nebo se najde shoda.

Příklad `Url.Action` uvedená výše předpokládá konvenční směrování, ale adresa URL generování funguje podobně se směrováním atributů, i když popsané koncepty se jiný. S konvenčním směrování, hodnoty trasy umožňují rozšířit šablonu a hodnot trasy pro `controller` a `action` obvykle zobrazují v šabloně – to funguje, protože odpovídající směrování adres URL dodržovat *konvence*. V atributu směrování, hodnoty trasy `controller` a `action` nepovolují se zobrazí v šabloně – místo toho slouží k vyhledání, která šablona se má použít.

Tento příklad používá směrování atributů:

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

Vytvoří vyhledávací tabulku všech akcí atribut směrovat MVC a bude odpovídat `controller` a `action` hodnoty a vyberte šablonu trasy pro generování adresy URL. V příkladu výše `custom/url/to/destination` je generován.

### <a name="generating-urls-by-action-name"></a>Generování adresy URL pomocí názvu akce

`Url.Action` (`IUrlHelper` . `Action`) a všech souvisejících přetížení všechny jsou založeny na tento nápad, že chcete určit, co při připojování ke zadáním názvu kontroleru a názvu akce.

> [!NOTE]
> Při použití `Url.Action`, aktuální trasy hodnoty `controller` a `action` jsou určené pro vás – hodnota `controller` a `action` jsou součástí obou *okolí hodnoty* **a** *hodnoty*. Metoda `Url.Action`, vždy používá aktuální hodnoty `action` a `controller` a budou vytvářet cesty adresy URL, která směruje na aktuální akci.

Směrování pokusy, aby použil hodnoty v okolí hodnoty k vyplnění informací, které nebyly poskytují při generování adresy URL. Nepřidávejte jako `{a}/{b}/{c}/{d}` a okolí hodnoty `{ a = Alice, b = Bob, c = Carol, d = David }`, směrování má dostatek informací ke generování adresy URL bez jakékoli další hodnoty – protože směrovat všechny parametry mají hodnotu. Pokud jste přidali hodnota `{ d = Donovan }`, hodnota `{ d = David }` by být ignorovány, a vygenerovanou cestu adresy URL by `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Cesty adresy URL jsou hierarchická. V příkladu výše, pokud jste přidali hodnota `{ c = Cheryl }`, obě hodnoty `{ c = Carol, d = David }` bude ignorován. V tomto případě jsme už nebude mít hodnotu `d` a generování adresy URL se nezdaří. Je třeba zadat hodnotu požadovaného `c` a `d`.  Můžete očekávat, že přístupů tento problém se výchozí trasa (`{controller}/{action}/{id?}`) – tomuto chování v praxi jako bude docházet zřídka, ale `Url.Action` bude vždy explicitně určete `controller` a `action` hodnotu.

Delší přetížení `Url.Action` taky využít další *hodnot trasy* objektu k poskytnutí hodnot pro parametry trasy jiné než `controller` a `action`. Nejčastěji to použít s se zobrazí `id` jako `Url.Action("Buy", "Products", new { id = 17 })`. Podle konvence *hodnot trasy* objekt je obvykle objekt anonymního typu, ale může být `IDictionary<>` nebo *prostý původní objekt .NET*. Žádné další trasy hodnoty, které neodpovídají parametry trasy jsou umístěny v řetězci dotazu.

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> Absolutní adresu URL vytvoříte pomocí přetížení přijímající `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generuje adresy URL trasy

Výše uvedený kód jsme vám ukázali, generování adresy URL předáním názvu kontroleru a akce. `IUrlHelper` poskytuje také `Url.RouteUrl` řady metod. Tyto metody jsou podobné `Url.Action`, ale jejich aktuálními hodnotami nekopírujte `action` a `controller` hodnoty trasy. Nejběžnější použití je zadání názvu trasy pro konkrétní trasu použít ke generování adresy URL, obecně *bez* zadáním názvu kontroler nebo akce.

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generování adresy URL ve formátu HTML

`IHtmlHelper` poskytuje `HtmlHelper` metody `Html.BeginForm` a `Html.ActionLink` ke generování `<form>` a `<a>` prvky v uvedeném pořadí. Tyto metody používají `Url.Action` metoda ke generování adresy URL a přijetí podobně jako argumenty. `Url.RouteUrl` Companions pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` které mají podobné funkce.

TagHelpers generování adres URL prostřednictvím `form` Taghelperu a `<a>` Taghelperu. Obě tyto použít `IUrlHelper` pro jejich implementaci. Zobrazit [práce s formuláři](../views/working-with-forms.md) Další informace.

Uvnitř zobrazení `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnost pro všechny ad-hoc generování adresy URL není pokrytá výše.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generování adresy URL v výsledky akcí

Výše uvedené příklady mají zobrazit díky `IUrlHelper` v kontroleru, zatímco nejběžnější využití v kontroleru je ke generování adresy URL jako součást výsledku akce.

`ControllerBase` a `Controller` základní třídy poskytují vhodné metody pro výsledky akce, které odkazují na jiná akce. Jeden typickému využití je provést přesměrování po přijetí vstupu uživatele.

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

Metody pro vytváření objektů výsledků akce použijte podobný vzorec na metody na `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Zvláštní případ pro vyhrazené konvenční trasy

Konvenční směrování můžete použít zvláštní druh definice trasy volána *vyhrazené konvenční trasy*. V následujícím příkladu s názvem trasy `blog` vyhrazené konvenční tras.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Pomocí těchto definicí cesty `Url.Action("Index", "Home")` vygeneruje cesty URL `/` s `default` trasy, ale proč? Může být uhodnout hodnoty trasy `{ controller = Home, action = Index }` dost informací k vygenerování adresy URL použije `blog`, a výsledkem bude `/blog?action=Index&controller=Home`.

Konvenční trasy vyhrazené Spolehněte se na zvláštní chování výchozí hodnoty, které nemají odpovídající parametr trasy, trasy brání "příliš chamtivého" s generování adresy URL. V tomto případě výchozí hodnoty jsou `{ controller = Blog, action = Article }`a ani `controller` ani `action` se zobrazí jako parametr trasa. Při generování adresy URL směrování provádí, hodnoty poskytnuté musí odpovídat výchozím hodnotám. Pomocí generování adresy URL `blog` selže, protože hodnoty `{ controller = Home, action = Index }` neodpovídají `{ controller = Blog, action = Article }`. Směrování pak přejde k akci `default`, která proběhne úspěšně.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Oblasti

[Oblasti](areas.md) jsou funkce služby MVC používány pro organizaci související funkce do skupiny jako samostatné směrování – obor názvů (pro akce kontroleru) a strukturu složek (pro zobrazení). Pomocí oblastí umožňuje aplikaci mít víc řadičích se stejným názvem – za předpokladu, že mají různé *oblasti*. Pomocí oblastí vytvoří hierarchii pro účely směrování tak, že přidáte další parametr trasy, `area` k `controller` a `action`. Tato část se bude zabývat směrování interakci s oblastmi - naleznete v tématu [oblasti](areas.md) podrobnosti o použití oblasti se zobrazeními.

Následující příklad nastaví MVC použití konvenční výchozí trasu a *trasy oblasti* pro danou oblast s názvem `Blog`:

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

Při porovnávání cesty adresy URL jako `/Manage/Users/AddUser`, první trasa vytvoří hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }`. `area` Trasy hodnota je vytvořen výchozí hodnotu pro `area`, ve skutečnosti vytvořené trasy `MapAreaRoute` je ekvivalentní následujícímu:

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` Vytvoří trasu pomocí výchozí hodnoty a omezení pro `area` pomocí názvu zadané oblasti, v tomto případě `Blog`. Výchozí hodnota zajistí, že vždy vytváří trasy `{ area = Blog, ... }`, omezení vyžaduje hodnotu `{ area = Blog, ... }` pro generování adresy URL.

> [!TIP]
> Konvenční směrování je závislé na pořadí. Obecně platí trasy s oblastmi by měl umístit výše ve směrovací tabulce, jako jsou podrobnější než trasy bez oblasti.

Pomocí výše uvedený příklad, hodnoty trasy odpovídají následující akce:

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` Je co označuje kontroleru v rámci oblasti, říkáme, že tento kontroler je v `Blog` oblasti. Řadiče bez `[Area]` atributu nejsou členy libovolné oblasti a bude **není** odpovídat, kdy `area` trasy hodnota poskytuje směrování. V následujícím příkladu může odpovídat pouze první řadič uvedené hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Obor názvů každý kontroler je znázorněna zde pro úplnost – kontrolery jinak bude mít názvy v konfliktu a generovat chybu kompilátoru. Obory názvů třídy nemají žádný vliv na směrování MVC.

První dva řadiče jsou členy oblastí a porovnávat pouze při jejich název příslušné oblasti poskytuje `area` trasy hodnotu. Třetí kontroler není členem žádné oblasti a pouze shody může při žádná hodnota pro `area` poskytuje směrování.

> [!NOTE]
> Z hlediska odpovídající *žádná hodnota*, neexistence `area` hodnota je stejná jako hodnota `area` byla null nebo prázdný řetězec.

Při provádění akce v oblasti, hodnoty trasy pro `area` bude k dispozici jako *okolí hodnotu* pro směrování pro generování adresy URL. To znamená, že ve výchozím nastavení oblasti fungovat *vždy navrchu* pro generování adresy URL, jak je ukázáno v následujícím příkladu.

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Principy IActionConstraint

> [!NOTE]
> Tato část se podrobně na interní informace o rozhraní framework a způsobu, jakým MVC vybírá akci, kterou chcete spustit. Typická aplikace nebudete potřebovat vlastní `IActionConstraint`

Pravděpodobně už používáte `IActionConstraint` i v případě, že nejste obeznámeni s rozhraním. `[HttpGet]` Atribut a podobné `[Http-VERB]` implementaci atributy `IActionConstraint` aby bylo možné omezit provádění metody akce.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Za předpokladu, že výchozí konvenční trasu cesty URL `/Products/Edit` vyprodukuje hodnoty `{ controller = Products, action = Edit }`, která by odpovídala **obě** akcí je vidět tady. V `IActionConstraint` terminologie by říkáme, že obě tyto akce jsou považovány za kandidáty – jako obě shodovat data trasy.

Když `HttpGetAttribute` spustí, bude říct, že *Edit()* odpovídá *získat* a není nalezena shoda s další příkaz protokolu HTTP. `Edit(...)` Akce nemá žádné omezení definovaná a proto bude odpovídat libovolný příkaz protokolu HTTP. To za předpokladu, že `POST` – pouze `Edit(...)` odpovídá. Ale pro `GET` obě akce může i nadále odpovídat - však akce s `IActionConstraint` je vždy považován za *lepší* než akce bez. Takže protože `Edit()` má `[HttpGet]` se považuje za konkrétnější a bude vybrána, pokud obě akce můžou odpovídat.

Koncepčně `IActionConstraint` je forma *přetížení*, ale ne přetížení metod se stejným názvem, je přetížení mezi akcemi, které odpovídají stejnou adresu URL. Směrování atributů, zabírá `IActionConstraint` a může docházet k provedení akcí z různých řadičů obě jsou považovány za kandidáty.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementace IActionConstraint

Nejjednodušší způsob, jak implementovat `IActionConstraint` je vytvoření třídy odvozené od `System.Attribute` a umístěte ho na akce a kontrolery. MVC automaticky zjistí všechny `IActionConstraint` , která jsou použita jako atributy. Můžete použít model aplikace použít omezení, a to je pravděpodobně nejflexibilnějším přístupem, protože umožňuje metaprogram jak uplatňují.

V následujícím příkladu vybere omezení akce na základě *směrové číslo země* z dat trasy. [Úplná ukázka na Githubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

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

Zodpovídáte za implementaci `Accept` metoda a zvolením "Objednávku" omezení ke spuštění. V tomto případě `Accept` vrátí metoda `true` k označení "action" je shoda při `country` trasy hodnotu shody. Tím se liší od `RouteValueAttribute` v tom, že umožňuje použití náhradní lokality bez atributů akce. Vzorek ukazuje, že pokud definujete `en-US` akce potom takový kód země `fr-FR` použije místo toho obecnější kontroler, který nemá `[CountrySpecific(...)]` použít.

`Order` Určuje vlastnost, na které *fáze* je součástí omezení. Akce omezení spuštění ve skupinách na základě `Order`. Například všechny rozhraní zadané atributy metody HTTP použít stejné `Order` hodnotu tak, aby spouštět ve stejné fázi. Můžete mít libovolný počet fáze, protože je potřeba implementovat požadované zásady.

> [!TIP]
> Při rozhodování o hodnotu `Order` přemýšlení o tom, zda by měla být vaše omezení používají před metody HTTP. Nižší hodnoty se spouští jako první.
