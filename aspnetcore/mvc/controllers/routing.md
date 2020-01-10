---
title: Směrování na akce kontroleru v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core MVC používá middleware směrování k párování adres URL příchozích požadavků a jejich mapování na akce.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/routing
ms.openlocfilehash: 8cf7e74df292a614f287eff8561a22187f6558ce
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866056"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Směrování na akce kontroleru v ASP.NET Core

Služba [Ryan Nowak](https://github.com/rynowak) a [Rick Anderson](https://twitter.com/RickAndMSFT)

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

Uvnitř volání `UseMvc`se `MapRoute` používá k vytvoření jedné trasy, na kterou odkazujeme jako na `default` trase. Většina aplikací MVC bude používat trasu se šablonou podobnou `default` trase.

`"{controller=Home}/{action=Index}/{id?}"` šablony trasy se může shodovat s cestou URL jako `/Products/Details/5` a extrahuje hodnoty trasy `{ controller = Products, action = Details, id = 5 }` cestou tokenizací. MVC se pokusí vyhledat kontroler s názvem `ProductsController` a spustit akci `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Všimněte si, že v tomto příkladu vazba modelu používá hodnotu `id = 5` k nastavení parametru `id` na `5` při vyvolání této akce. Další podrobnosti najdete v tématu [vazba modelu](../models/model-binding.md) .

Použití trasy `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Šablona trasy:

* `{controller=Home}` definuje jako výchozí `controller` `Home`.

* `{action=Index}` definuje jako výchozí `action` `Index`.

* `{id?}` definuje `id` jako volitelné.

Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici. Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](../../fundamentals/routing.md#route-template-reference) .

`"{controller=Home}/{action=Index}/{id?}"` se může shodovat s cestou adresy URL `/` a vytvoří hodnoty trasy `{ controller = Home, action = Index }`. Hodnoty pro `controller` a `action` využívají výchozí hodnoty, `id` nevytvoří hodnotu, protože v cestě URL není žádný odpovídající segment. MVC použije tyto hodnoty tras k výběru `HomeController` a `Index` akci:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Pomocí této definice kontroleru a šablony trasy se spustí akce `HomeController.Index` pro některou z následujících cest URL:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Metoda usnadnění `UseMvcWithDefaultRoute`:

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

`UseMvc` a `UseMvcWithDefaultRoute` přidat instanci `RouterMiddleware` do kanálu middlewaru. MVC nekomunikuje přímo se middlewarem a používá směrování pro zpracování požadavků. MVC je připojeno k trasám prostřednictvím instance `MvcRouteHandler`. Kód uvnitř `UseMvc` je podobný následujícímu:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` přímo nedefinuje žádné trasy, přidá zástupný symbol do kolekce směrování pro trasu `attribute`. Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a zároveň podporuje směrování atributů.  `UseMvc` a všechny jeho variace přidají zástupný symbol pro atribut směrování atributu směrování je vždycky dostupný bez ohledu na to, jak nakonfigurujete `UseMvc`. `UseMvcWithDefaultRoute` definuje výchozí trasu a podporuje směrování atributů. Oddíl [Směrování atributů](#attribute-routing-ref-label) obsahuje další podrobnosti o směrování atributů.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Konvenční směrování

Trasa `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

je příkladem *konvenčního směrování*. Tento styl se nazývá *konvenční směrování* , protože vytváří *konvenci* pro cesty URL:

* první segment cesty se mapuje na název kontroleru.

* druhý se mapuje na název akce.

* Třetí segment se používá pro volitelný `id`, který se používá k mapování na entitu modelu.

Pomocí této `default` trasy adresa URL `/Products/List` mapuje na `ProductsController.List` akci a `/Blog/Article/17` mapuje na `BlogController.Article`. Toto mapování je založené **pouze** na názvech kontroléru a akce a není založené na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.

> [!TIP]
> Použití konvenčního směrování s výchozí trasou vám umožní rychle vytvořit aplikaci, aniž by se musela používat nový vzor adresy URL pro každou akci, kterou definujete. Pro aplikace s akcemi stylu CRUD, které mají konzistenci pro adresy URL napříč řadiči, může zjednodušit váš kód a zajistit větší předvídatelné uživatelské rozhraní.

> [!WARNING]
> `id` je definována jako volitelná šablonou směrování, což znamená, že se akce můžou provádět bez ID, které je součástí adresy URL. Obvykle k tomu, co se stane, když se `id` z adresy URL vynechá, znamená to, že bude nastavená na `0` podle vazby modelu a v důsledku toho se v databázi odpovídající `id == 0`nenajde žádná entita. Směrování atributů vám může poskytnout jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní. Podle konvence bude dokumentace obsahovat volitelné parametry, jako `id`, když se budou pravděpodobně zobrazovat ve správném použití.

## <a name="multiple-routes"></a>Více tras

Přidáním dalších volání do `MapRoute`můžete do `UseMvc` přidat více tras. V takovém případě můžete definovat více konvencí nebo přidat konvenční trasy, které jsou vyhrazeny určité akci, například:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`blog` trasa je to *vyhrazená konvenční trasa*, což znamená, že používá konvenční směrovací systém, ale je vyhrazená konkrétní akci. Vzhledem k tomu, že `controller` a `action` se v šabloně směrování nezobrazují jako parametry, můžou mít jenom výchozí hodnoty, takže tato trasa se vždycky namapuje na `BlogController.Article`akcí.

Trasy v kolekci tras jsou seřazené a budou zpracovávány v pořadí, v jakém byly přidány. Takže v tomto příkladu bude `blog` trasa vyzkoušena před trasou `default`.

> [!NOTE]
> *Vyhrazené konvenční trasy* často používají zachycení všech parametrů tras, jako je `{*article}`, k zachycení zbývající části cesty URL. To může vést k tomu, že trasa "je příliš hlade", což znamená, že odpovídá adresám URL, které mají odpovídat jiným trasám. Pokud to chcete vyřešit, umístěte trasy "hladce" do směrovací tabulky později.

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

Tento kontroler definuje dvě akce, které by odpovídaly cestě URL `/Products/Edit/17` a `{ controller = Products, action = Edit, id = 17 }`dat směrování. Toto je typický vzor pro řadiče MVC, kde `Edit(int)` zobrazuje formulář pro úpravu produktu a `Edit(int, Product)` zpracovává vystavený formulář. Aby tato možnost MVC mohla být v případě, že se jedná o jinou akci než příkaz HTTP, musela zvolit `Edit(int, Product)`, když je žádost `POST` HTTP a `Edit(int)`.

`HttpPostAttribute` (`[HttpPost]`) je implementace `IActionConstraint`, která umožňuje, aby byla akce vybrána pouze v případě, že je `POST`příkaz HTTP. Přítomnost `IActionConstraint` nastaví `Edit(int, Product)` "lepší", než `Edit(int)`, takže se `Edit(int, Product)` vyzkouší jako první.

Stačí napsat vlastní implementace `IActionConstraint` ve specializovaných scénářích, ale je důležité pochopit, že role atributů, jako jsou `HttpPostAttribute` podobné atributy, jsou definovány pro jiné příkazy HTTP. V konvenčním směrování je běžné, že pokud jsou součástí pracovního postupu `show form -> submit form`, je běžné použít stejný název akce. Pohodlí tohoto vzoru se po kontrole části [porozumění IActionConstraint](#understanding-iactionconstraint) projeví.

Pokud se více tras shodují a MVC nemůže najít nejlepší trasu, vyvolá `AmbiguousActionException`.

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

Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování. V následujícím příkladu se `app.UseMvc();` používá v metodě `Configure` a není předána žádná trasa. `HomeController` se bude shodovat s množinou adres URL podobných tomu, co by se výchozí trasa `{controller=Home}/{action=Index}/{id?}` shodovala:

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

Akce `HomeController.Index()` se spustí pro všechny cesty URL `/`, `/Home`nebo `/Home/Index`.

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

Směrování atributů může také používat atributy `Http[Verb]`, jako je například `HttpPostAttribute`. Všechny tyto atributy mohou přijmout šablonu směrování. Tento příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:

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

V případě cesty URL, jako je `/products` akce `ProductsApi.ListProducts` se spustí, když je příkaz HTTP `GET` a `ProductsApi.CreateProduct` se spustí, když se příkaz HTTP `POST`. Směrování atributů první odpovídá adrese URL oproti sadě šablon směrování definovaných atributy směrování. Po porovnání šablony trasy se použijí omezení `IActionConstraint` pro určení akcí, které lze provést.

> [!TIP]
> Při sestavování REST API je zřídka pravděpodobné, že budete chtít použít `[Route(...)]` na metodě akce, protože akce přijme všechny metody HTTP. Je lepší používat přesnější `Http*Verb*Attributes`, abyste měli přesně informace o tom, co vaše rozhraní API podporuje. Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.

Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy. V tomto příkladu je `id` nutné jako součást cesty URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Akce `ProductsApi.GetProduct(int)` se spustí pro cestu URL, jako je `/products/3`, ale ne pro cestu URL, jako je `/products`. Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](../../fundamentals/routing.md) .

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
> Na rozdíl od konvenční *výchozí trasy*definuje parametr `id` jako volitelné (`{id?}`). Tato možnost přesného určení rozhraní API má výhody, jako je například povolení `/products` a `/products/5` odeslání do různých akcí.

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

V tomto příkladu se cesta URL `/products` může shodovat `ProductsApi.ListProducts`a cesta URL `/products/5` může odpovídat `ProductsApi.GetProduct(int)`. Obě tyto akce odpovídají pouze `GET` HTTP, protože jsou označeny `HttpGetAttribute`.

Šablony směrování použité pro akci, která začíná na `/` nebo `~/` se nekombinují se šablonami směrování použitými pro kontroler. Tento příklad odpovídá sadě cest URL podobně jako *výchozí trasa*.

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

Na rozdíl od konvenčních tras, které provádějí v definovaném pořadí, směrování atributů vytvoří strom a porovnává všechny trasy současně. To se chová jako – Pokud byly položky trasy umístěny v ideálním pořadí; nejvíce konkrétní trasy mají možnost provést před obecnější trasy.

Například trasa jako `blog/search/{topic}` je konkrétnější než trasa, jako je `blog/{*article}`. Logicky mluví nejprve `blog/search/{topic}` Route "běžel", ve výchozím nastavení, protože se jedná o jediné řazení rozumné. Pomocí konvenčního směrování zodpovídá vývojář za umístění tras v požadovaném pořadí.

Trasy atributů mohou konfigurovat objednávku pomocí vlastnosti `Order` všech rozhraní, které poskytují atributy směrování. Trasy jsou zpracovávány podle vzestupného řazení vlastnosti `Order`. Výchozí pořadí je `0`. Nastavení trasy pomocí `Order = -1` spustí před trasami, které nenastaví objednávku. Nastavení trasy pomocí `Order = 1` spustí se po výchozím řazení trasy.

> [!TIP]
> Nepoužívejte v závislosti na `Order`. Pokud vaše adresa URL vyžaduje explicitní směrování hodnot pořadí, je pravděpodobné, že budou i u klientů matoucí. V části Obecné směrování atributů výběr správné trasy s odpovídající adresou URL. Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití vlastnosti `Order`.

Směrování Razor Pages směrování a řadiče MVC sdílí implementaci. Informace o pořadí směrování v Razor Pages tématech jsou k dispozici v tématu [Razor Pages konvence směrování a aplikace: pořadí směrování](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Nahrazení tokenu v šablonách směrování ([Controller], [Action], [oblast])

Pro usnadnění pohodlí trasy atributů podporují *Nahrazení tokenu* uzavřením tokenu do hranatých závorek (`[`, `]`). Tokeny `[action]`, `[area]`a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována. V následujícím příkladu akce odpovídají cestám adresy URL, jak je popsáno v komentářích:

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů. Výše uvedený příklad se chová stejně jako následující kód:

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

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

Nahrazení tokenu platí také pro názvy tras definované směrováními atributů. `[Route("[controller]/[action]", Name="[controller]_[action]")]` vygeneruje jedinečný název trasy pro každou akci.

Chcete-li porovnat oddělovač nahrazení tokenu literálu `[` nebo `]`, vydejte ho opakováním znaku (`[[` nebo `]]`).

::: moniker range=">= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Použití transformátoru parametrů k přizpůsobení náhrady tokenu

Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů. Transformátor parametru implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů. Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`.

`RouteTokenTransformerConvention` je konvence aplikačního modelu, kterou:

* Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.
* Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` je v `ConfigureServices`zaregistrován jako možnost.

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

Pokud jsou na akci umístěny více atributů trasy (které implementují `IActionConstraint`), pak každé omezení akce kombinuje s šablonou směrování z atributu, který ji definoval.

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

Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](../../fundamentals/routing.md#route-template-reference) .

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Vlastní atributy směrování pomocí `IRouteTemplateProvider`

Všechny atributy trasy, které jsou k dispozici v rozhraní (`[Route(...)]`, `[HttpGet(...)]` atd.) implementují rozhraní `IRouteTemplateProvider`. MVC vyhledává atributy tříd kontroleru a metody akcí při spuštění aplikace a používá ty, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.

Můžete implementovat `IRouteTemplateProvider` k definování vlastních atributů tras. Každý `IRouteTemplateProvider` umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Atribut z výše uvedeného příkladu automaticky nastaví `Template` na `"api/[controller]"` při použití `[MyApiController]`.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Přizpůsobení tras atributů pomocí aplikačního modelu

*Aplikační model* je objektový model vytvořený při spuštění se všemi metadaty, které MVC používá ke směrování a provádění akcí. *Aplikační model* zahrnuje všechna data získaná z atributů směrování (prostřednictvím `IRouteTemplateProvider`). Můžete napsat *konvence* pro úpravu aplikačního modelu v době spuštění, abyste mohli přizpůsobit způsob, jakým se směrování chová. V této části se dozvíte jednoduchý příklad přizpůsobení směrování pomocí aplikačního modelu.

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Kombinované směrování: směrování atributů vs. konvenční směrování

Aplikace MVC můžou kombinovat použití konvenčního směrování a směrování atributů. Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.

Akce jsou směrovány buď podle konvence nebo směrovaného atributu. Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT. Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak. **Libovolný** atribut směrování v řadiči provádí směrování všech akcí v atributu kontroleru.

> [!NOTE]
> Způsob, jakým se rozlišují dva typy systémů směrování, je proces, který se použije po tom, co adresa URL odpovídá šabloně směrování. V části konvenční směrování se při výběru akce a kontroleru z vyhledávací tabulky všech konvenčních akcí směrování používají hodnoty tras z shody. V směrování atributů je Každá šablona již přidružena k akci a není nutné žádné další vyhledávání.

## <a name="complex-segments"></a>Komplexní segmenty

Komplexní segmenty (například `[Route("/dog{token}cat")]`) jsou zpracovávány porovnáním koncových literálů zprava doleva nehladým způsobem. Popis najdete [ve zdrojovém kódu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) . Další informace najdete v [tomto problému](https://github.com/aspnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Generování adresy URL

Aplikace MVC můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL. Generování adres URL eliminuje adresy URL zakódujeme, takže je váš kód robustnější a udržovatelný. Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a bude se týkat jenom základních informací o tom, jak generování adresy URL funguje. Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](../../fundamentals/routing.md) .

Rozhraní `IUrlHelper` je základní částí infrastruktury mezi MVC a směrováním pro generování adresy URL. V části řadiče, zobrazení a zobrazení komponent najdete instanci `IUrlHelper` k dispozici prostřednictvím vlastnosti `Url`.

V tomto příkladu je `IUrlHelper` rozhraní použito prostřednictvím vlastnosti `Controller.Url` pro vygenerování adresy URL jiné akci.

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Pokud aplikace používá výchozí konvenční trasu, hodnota proměnné `url` bude řetězec cesty adresy URL `/UrlGeneration/Destination`. Tato cesta URL se vytvoří směrováním kombinováním hodnot tras z aktuální žádosti (okolní hodnoty) s hodnotami předanými do `Url.Action` a nahrazením těchto hodnot do šablony trasy:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami. Parametr trasy, který nemá hodnotu, může používat výchozí hodnotu, pokud má jednu, nebo může být vynechána, pokud je volitelná (jako v případě `id` v tomto příkladu). Generování adresy URL selže, pokud žádný požadovaný parametr trasy nemá odpovídající hodnotu. Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.

Příklad `Url.Action` výše předpokládá konvenční směrování, ale generování adresy URL funguje podobně jako při směrování atributů, i když se koncepty liší. Při použití konvenčního směrování se hodnoty tras používají k rozbalení šablony a hodnoty tras pro `controller` a `action` obvykle se v této šabloně zobrazují – to funguje, protože adresy URL, které odpovídají směrování, dodržují *konvence*. V případě směrování atributů se v šabloně nemůžou zobrazovat hodnoty tras pro `controller` a `action`, které se místo toho používají k vyhledání šablony, která se má použít.

V tomto příkladu se používá směrování atributů:

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC vytvoří vyhledávací tabulku všech akcí směrovaného atributu a bude odpovídat hodnotám `controller` a `action` a vybrat šablonu směrování, která se má použít pro generování adresy URL. V ukázce výše se vygeneruje `custom/url/to/destination`.

### <a name="generating-urls-by-action-name"></a>Generování adres URL podle názvu akce

`Url.Action` (`IUrlHelper` . `Action`) a všechna související přetížení jsou založena na této myšlence, se kterou chcete určit, k čemu se odkazuje, zadáním názvu kontroleru a názvu akce.

> [!NOTE]
> Při použití `Url.Action`jsou aktuální hodnoty trasy pro `controller` a `action` určené pro vás – hodnota `controller` a `action` jsou součástí obou *okolních hodnot* **a** *hodnot*. Metoda `Url.Action`, vždy používá aktuální hodnoty `action` a `controller` a vygeneruje cestu URL, která bude směrovat na aktuální akci.

Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které jste nezadali při generování adresy URL. Při použití trasy, jako je `{a}/{b}/{c}/{d}` a ambientních hodnot `{ a = Alice, b = Bob, c = Carol, d = David }`, směrování obsahuje dostatek informací pro vygenerování adresy URL bez dalších hodnot – protože všechny parametry tras mají hodnotu. Pokud jste přidali hodnotu `{ d = Donovan }`, hodnota `{ d = David }` by byla ignorována a vygenerovaná cesta URL bude `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Cesty URL jsou hierarchické. Pokud jste v předchozím příkladu přidali hodnotu `{ c = Cheryl }`, budou obě hodnoty `{ c = Carol, d = David }` ignorovány. V tomto případě již nemusíte mít hodnotu pro `d` a generování adresy URL selže. Je nutné zadat požadovanou hodnotu `c` a `d`.  Můžete očekávat, že se tento problém bude nacházet s výchozí trasou (`{controller}/{action}/{id?}`), ale toto chování se bude v praxi chovat zřídka, protože `Url.Action` vždy explicitně specifikuje `controller` a `action` hodnotu.

Delší přetížení `Url.Action` také přijímají další objekt *hodnoty trasy* , který poskytuje hodnoty pro parametry směrování jiné než `controller` a `action`. Nejčastěji to vidíte, že se používá `id` jako `Url.Action("Buy", "Products", new { id = 17 })`. Podle konvence objekt *hodnoty trasy* je obvykle objektem anonymního typu, ale může se také jednat o `IDictionary<>` nebo o *prostý starý objekt .NET*. Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> Chcete-li vytvořit absolutní adresu URL, použijte přetížení, které přijímá `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generování adres URL podle směrování

Výše uvedený kód ukázal generování adresy URL předáním do kontroleru a názvu akce. `IUrlHelper` také poskytuje `Url.RouteUrl` rodinu metod. Tyto metody jsou podobné `Url.Action`, ale nekopírují aktuální hodnoty `action` a `controller` na hodnoty tras. Nejběžnějším využitím je zadání názvu trasy pro použití konkrétní trasy pro vygenerování adresy URL, a to obecně *bez* zadání kontroleru nebo názvu akce.

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generování adres URL v HTML

`IHtmlHelper` poskytuje `HtmlHelper` metody `Html.BeginForm` a `Html.ActionLink` k vygenerování `<form>` a `<a>` prvků v uvedeném pořadí. Tyto metody používají metodu `Url.Action` k vygenerování adresy URL a akceptuje podobné argumenty. `Url.RouteUrl` doprovodné funkce pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` s podobnými funkcemi.

TagHelpers vygeneruje adresy URL prostřednictvím `form` Taghelperu a `<a>` Taghelperu. Obě tato použití `IUrlHelper` pro jejich implementaci. Další informace najdete v tématu [práce s formuláři](../views/working-with-forms.md) .

V zobrazeních jsou `IUrlHelper` k dispozici prostřednictvím vlastnosti `Url` pro všechny generace adres URL ad-hoc, které nejsou pokryté výše.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generování adres URL ve výsledcích akcí

Výše uvedené příklady ukazují použití `IUrlHelper` v kontroleru, zatímco nejběžnější využití v kontroleru vygeneruje adresu URL jako součást výsledku akce.

Základní třídy `ControllerBase` a `Controller` poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci. Jedním z typických použití je přesměrování po přijetí vstupu uživatele.

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

Metody pro vytváření výsledků pro zpracování akcí dodrží podobný vzor pro metody `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Zvláštní případ pro vyhrazené konvenční trasy

Konvenční směrování může používat speciální druh definice trasy, která se označuje jako *vyhrazená konvenční trasa*. V následujícím příkladu je trasa s názvem `blog` vyhrazenou konvenční trasou.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Pomocí těchto definic tras `Url.Action("Index", "Home")` vygeneruje cestu URL `/` s `default` trasou, ale proč? Můžete odhadnout hodnoty tras, `{ controller = Home, action = Index }` by bylo dostatečné pro vygenerování adresy URL pomocí `blog`a výsledek by byl `/blog?action=Index&controller=Home`.

Vyhrazené konvenční trasy spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla "příliš hlade" s generováním adresy URL. V tomto případě jsou `{ controller = Blog, action = Article }`výchozí hodnoty a ani `controller` ani `action` se nezobrazí jako parametr trasy. Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám. Generování adresy URL pomocí `blog` se nezdaří, protože hodnoty `{ controller = Home, action = Index }` neodpovídají `{ controller = Blog, action = Article }`. Směrování se pak vrátí k pokusu o `default`, který je úspěšný.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Oblasti

[Oblasti](areas.md) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů směrování (pro akce kontroleru) a struktury složek (pro zobrazení). Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé *oblasti*. Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru trasy `area` `controller` a `action`. V této části se dozvíte, jak směrování komunikuje s oblastmi – viz [oblasti](areas.md) pro podrobnosti o použití oblastí se zobrazeními.

V následujícím příkladu je nakonfiguruje MVC pro použití výchozí konvenční trasy a *trasy oblasti* pro oblast s názvem `Blog`:

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

Když se porovná cesta URL jako `/Manage/Users/AddUser`, první trasa vytvoří hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }`. Hodnota `area` trasy je vytvořena výchozí hodnotou pro `area`, ve skutečnosti je trasa vytvořená v `MapAreaRoute` shodná s následujícím:

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` vytvoří trasu pomocí výchozí hodnoty i omezení pro `area` pomocí zadaného názvu oblasti v tomto případě `Blog`. Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }`, omezení vyžaduje `{ area = Blog, ... }` hodnoty pro generování adresy URL.

> [!TIP]
> Konvenční směrování je závislé na pořadí. Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.

Pomocí výše uvedeného příkladu budou hodnoty směrování odpovídat následující akci:

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` je to, že jako součást oblasti označuje kontroler, říkáme, že tento kontroler je v oblasti `Blog`. Řadiče bez atributu `[Area]` nejsou členy žádné oblasti **a nebudou se shodovat,** Pokud je hodnota `area` trasy poskytnuta směrováním. V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám tras `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Obor názvů každého kontroleru se tady zobrazuje pro úplnost – jinak by řadiče vyžadovaly konflikt názvů a generovaly chybu kompilátoru. Obory názvů třídy nemají žádný vliv na směrování MVC.

První dva řadiče jsou členy oblastí a shodují se pouze v případě, že je jejich název příslušné oblasti poskytovaný hodnotou `area` trasy. Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že směrováním neposkytuje žádnou hodnotu pro `area`.

> [!NOTE]
> V souvislosti s neshodnou *hodnotou*je absence hodnoty `area` stejná, jako kdyby hodnota `area` měla hodnotu null nebo prázdný řetězec.

Při provádění akce uvnitř oblasti bude hodnota trasy pro `area` k dispozici jako *ambientní hodnota* pro směrování, která se má použít pro generování adresy URL. To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Principy IActionConstraint

> [!NOTE]
> Tato část je hlubokým podrobněem interních platforem a způsobu, jakým MVC vybírá akci, která se má provést. Typická aplikace nebude potřebovat vlastní `IActionConstraint`

Pravděpodobně jste již použili `IActionConstraint`, i když nejste obeznámeni s rozhraním. Atribut `[HttpGet]` a podobné atributy `[Http-VERB]` implementují `IActionConstraint`, aby bylo možné omezit provádění metody akce.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Za předpokladu, že výchozí konvenční trasa, adresa URL `/Products/Edit` by vytvořila `{ controller = Products, action = Edit }`hodnoty, které odpovídají **oběma** akcím, které jsou zde uvedeny. V `IActionConstraint` terminologii bychom znamenali, že obě tyto akce se považují za kandidáty, protože obě odpovídají datům směrování.

Pokud se `HttpGetAttribute` spustí, znamená to, že *Edit ()* je odpovídající pro *Get* a neshoduje se s žádným jiným příkazem http. Akce `Edit(...)` nemá definovány žádné omezení, a proto bude odpovídat jakémukoli příkazu HTTP. Proto předpokládáme, že `Edit(...)` odpovídá pouze `POST`. Ale u `GET` se obě akce stále shodují – ale akce s `IActionConstraint` je vždy považována za *lepší* , než je akce bez. Proto vzhledem k tomu, že `Edit()` má `[HttpGet]` považuje za konkrétnější, a vybere se, pokud se obě akce mohou shodovat.

Koncepční, `IActionConstraint` je forma *přetížení*, ale místo přetížení metod se stejným názvem, je přetížení mezi akcemi, které odpovídají stejné adrese URL. Směrování atributů používá taky `IActionConstraint` a může mít za následek, že se z různých řadičů považují i kandidáti.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementace IActionConstraint

Nejjednodušší způsob, jak implementovat `IActionConstraint`, je vytvořit třídu odvozenou z `System.Attribute` a umístit ji na vaše akce a řadiče. MVC automaticky zjistí všechny `IActionConstraint`, které se použijí jako atributy. Pomocí modelu aplikace můžete použít omezení, což je pravděpodobně nejpružnější přístup, protože umožňuje Metaprogram způsob jejich použití.

V následujícím příkladu omezení zvolí akci založenou na *kódu země* z údajů o trasách. [Úplná ukázka na GitHubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

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

Zodpovídáte za implementaci `Accept` metody a výběrem příkazu Order, aby se omezení spustilo. V tomto případě metoda `Accept` vrátí `true` k tomu, že se jedná o shodu, když hodnota `country` trasy odpovídá hodnotě. To se liší od `RouteValueAttribute` v tom, že umožňuje přechod na akci bez atributu. Ukázka ukazuje, že pokud definujete `en-US` akci, směrové číslo země, jako je `fr-FR`, se vrátí do obecnější kontroler, který nemá `[CountrySpecific(...)]` použití.

Vlastnost `Order` určuje, ve které *fázi* je omezení součástí. Omezení akcí se spouští ve skupinách na základě `Order`. Například všechny atributy, které poskytuje rozhraní HTTP, používají stejnou `Order` hodnotu tak, aby běžely ve stejné fázi. Můžete mít tolik fází, kolik potřebujete k implementaci požadovaných zásad.

> [!TIP]
> Chcete-li se rozhodnout o hodnotě pro `Order` zamyslete nad tím, zda by mělo být vaše omezení použito před metodami HTTP. Nižší čísla se spouštějí jako první.
