---
title: Formátování dat odpovědi v ASP.NET Core Web API
author: ardalis
description: Naučte se, jak formátovat data odpovědí v ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 05/29/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: e3417c9bfd3824133b86de2fe74f5f71367e1560
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886523"
---
<!-- DO NOT EDIT BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12077 MERGES -->
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formátování dat odpovědi v ASP.NET Core Web API

[Steve Smith](https://ardalis.com/)

ASP.NET Core MVC obsahuje integrovanou podporu pro formátování dat odpovědí pomocí pevných formátů nebo v reakci na specifikace klientů.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Výsledky akce specifické pro formát

Některé typy výsledků akce jsou specifické pro konkrétní formát, například `JsonResult` a. `ContentResult` Akce mohou vracet konkrétní výsledky, které jsou vždy formátovány určitým způsobem. Například vrácení a vrátí data `JsonResult` ve formátu JSON bez ohledu na Předvolby klienta. `ContentResult` Stejně tak vrátí návratová data řetězce ve formátu prostého textu (stejně jako jednoduše vrátí řetězec).

> [!NOTE]
> Akce není nutná k vrácení určitého konkrétního typu; MVC podporuje jakoukoli návratovou hodnotu objektu. Pokud akce vrátí `IActionResult` implementaci a kontroler dědí z `Controller`, mají vývojáři mnoho pomocných metod, které odpovídají mnoha volbám. Výsledky z akcí, které vracejí objekty, které `IActionResult` nejsou typy, budou serializovány pomocí `IOutputFormatter` příslušné implementace.

Chcete-li vrátit data v konkrétním formátu z kontroleru, který dědí ze `Controller` základní třídy, použijte vestavěnou pomocnou metodu `Json` pro návrat JSON a `Content` pro prostý text. Vaše metoda Action by měla vracet buď konkrétní typ výsledku (například, `JsonResult`) nebo. `IActionResult`

Vracení dat ve formátu JSON:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

Ukázková odpověď z této akce:

![Karta síť Vývojářské nástroje v Microsoft Edge ukazující, že typ obsahu odpovědi je Application/JSON](formatting/_static/json-response.png)

Všimněte si, že typ obsahu odpovědi je `application/json`zobrazen v seznamu síťových požadavků a v části hlavičky odpovědi. Všimněte si také seznam možností prezentovaných prohlížečem (v tomto případě Microsoft Edge) v hlavičce Accept v části s hlavičkami žádosti. Aktuální technika ignoruje tuto hlavičku. pojednává o tom, jak je popsáno níže.

Chcete-li vrátit data ve formátu prostého textu `Content` , použijte aplikaci `ContentResult` a pomocníka:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

Odpověď z této akce:

![Karta síť Vývojářské nástroje v Microsoft Edge ukazující, že typ obsahu odpovědi je text/prostý](formatting/_static/text-response.png)

Všimněte si, že `Content-Type` v tomto případě je `text/plain`vráceno. Stejné chování můžete také dosáhnout pouze pomocí typu řetězcové odpovědi:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> Pro netriviální akce s vícenásobnými návratovými typy nebo možnostmi (například různé stavové kódy HTTP založené na výsledku prováděných operací), preferovat `IActionResult` jako návratový typ.

## <a name="content-negotiation"></a>Vyjednávání obsahu

Vyjednávání obsahu (*conneg* pro krátké) proběhne, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Výchozí formát používaný ASP.NET Core MVC je JSON. Vyjednávání obsahu je implementováno `ObjectResult`nástrojem. Je také integrováno do výsledků akce specifické pro stav, které byly vráceny z pomocných metod (které jsou založeny `ObjectResult`na). Můžete také vrátit typ modelu (třídu, kterou jste definovali jako typ přenosu dat), a rozhraní si ho `ObjectResult` automaticky zalomí za vás.

Následující metoda akce používá `Ok` pomocné metody a: `NotFound`

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

Pokud není požadován jiný formát a server může vracet požadovaný formát, bude vrácena odpověď ve formátu JSON. Pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) , můžete vytvořit požadavek, který obsahuje hlavičku Accept a zadat jiný formát. V takovém případě, pokud má server *formátovací modul* , který může vytvořit odpověď v požadovaném formátu, výsledek bude vrácen ve formátu upřednostňovaném pro klienta.

![Konzola Fiddler zobrazující manuálně vytvořený požadavek GET s hodnotou potvrdit hlavičku Application/XML](formatting/_static/fiddler-composer.png)

Ve výše uvedeném snímku obrazovky byl k vygenerování požadavku použit skladatel Fiddler, který určí `Accept: application/xml`. Ve výchozím nastavení ASP.NET Core MVC podporuje pouze JSON, takže i v případě, že je zadán jiný formát, vrácený výsledek je stále ve formátu JSON. V další části se dozvíte, jak přidat další formátovací modul.

Akce kontroleru mohou vracet POCOs (staré staré objekty CLR). v takovém případě ASP.NET Core MVC automaticky vytvoří `ObjectResult` pro vás objekt, který ho zalomí. Klient získá formátovaný serializovaný objekt (výchozí formát JSON, můžete nakonfigurovat XML nebo jiné formáty). Pokud je `null`vrácen objekt, pak rozhraní `204 No Content` vrátí odpověď.

Vrací se typ objektu:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

V ukázce bude požadavek na platný alias autora dostávat odpověď 200 OK s daty autora. Požadavek na neplatný alias obdrží odpověď Content-204 bez obsahu. Snímky obrazovky ukazující odpověď ve formátech XML a JSON jsou uvedené níže.

### <a name="content-negotiation-process"></a>Proces vyjednávání obsahu

K`Accept` vyjednání obsahu dochází pouze v případě, že se v požadavku objeví hlavička. Když požadavek obsahuje hlavičku Accept, rozhraní provede výčet typů médií v hlavičce Accept v pořadí předvolby a pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z formátů určených hlavičkou Accept. V případě, že se nenajde žádný formátovací modul, který může splnit požadavek klienta, rozhraní se pokusí najít první formátovací modul, který může vyvolat odpověď (pokud vývojář nenakonfiguroval možnost na `MvcOptions` vrácení 406. místo toho není přijatelné). Pokud požadavek Určuje kód XML, ale formátovací modul XML nebyl nakonfigurován, bude použit formátovací modul JSON. Obecně platí, že pokud není nakonfigurován žádný formátovací modul, který může poskytnout požadovaný formát, bude použit první formátovací modul, který může objekt formátovat. Pokud není zadán žádný nadpis, bude při serializaci odpovědi použit první formátovací modul, který může zpracovat objekt, který má být vrácen. V takovém případě se neuskuteční žádné vyjednávání – server určí, který formát bude používat.

> [!NOTE]
> Pokud hlavička Accept obsahuje `*/*`, záhlaví se bude ignorovat, pokud `RespectBrowserAcceptHeader` `MvcOptions`není nastavené na hodnotu true.

### <a name="browsers-and-content-negotiation"></a>Prohlížeče a vyjednávání obsahu

Na rozdíl od typických klientů rozhraní API mají webové prohlížeče `Accept` v úmyslu zadávat hlavičky, které obsahují nejrůznější formáty, včetně zástupných znaků. Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče, ignoruje `Accept` hlavičku a místo toho vrátí obsah v nakonfigurovaném výchozím formátu aplikace (JSON, pokud není nakonfigurované jinak). To poskytuje jednotnější prostředí při použití různých prohlížečů ke využívání rozhraní API.

Pokud upřednostňujete, `RespectBrowserAcceptHeader` aby aplikace přijímala hlavičky, můžete ji nakonfigurovat jako součást konfigurace MVC nastavením na `true` v `ConfigureServices` metodě v *Startup.cs*.

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a>Konfigurace formátovacích modulů

Pokud vaše aplikace potřebuje podporovat další formáty nad rámec výchozího formátu JSON, můžete přidat balíčky NuGet a nakonfigurovat MVC na jejich podporu. Pro vstup a výstup existují samostatné formátovací moduly. Vstupní formátovací moduly jsou používány [vazbou modelu](xref:mvc/models/model-binding); výstupní formátovací moduly se používají k formátování odpovědí. Můžete také nakonfigurovat [vlastní formátovací](xref:web-api/advanced/custom-formatters)moduly.

::: moniker range=">= aspnetcore-3.0"

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurace formátovacích modulů založených na System. text. JSON

Funkce pro `System.Text.Json`formátovací moduly založené na službě se dají konfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcOptions.SerializerOptions`.

```csharp
services.AddMvc(options =>
{
    options.SerializerOptions.WriterSettings.Indented = true;
});
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Přidání podpory formátu JSON založeného na Newtonsoft. JSON

Před ASP.NET Core 3,0 byla `Newtonsoft.Json` sada MVC použita jako výchozí pro použití formátovacích formátovacích formátu JSON implementovaných pomocí balíčku. V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json`. Podpora formátovacích a funkcí založených na standardech je k dispozici po instalaci balíčku NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) a jeho konfiguraci v `Startup.ConfigureServices`. `Newtonsoft.Json`

```csharp
services.AddMvc()
    .AddNewtonsoftJson();
```

Některé funkce nemusí fungovat správně s `System.Text.Json`formátovacími moduly a vyžadují odkaz `Newtonsoft.Json`na formátování založené na ASP.NET Core 3,0 vydání. I nadále používat `Newtonsoft.Json`formátovací moduly založené na ASP.NET Core 3,0 nebo novější:

* Používá `Newtonsoft.Json` atributy ( `[JsonProperty]` například nebo `[JsonIgnore]`), přizpůsobuje nastavení serializace nebo spoléhá na funkce, které `Newtonsoft.Json` poskytuje.
* Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` akceptuje `JsonSerializerSettings` instance, která je specifická pro `Newtonsoft.Json`.
* Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .

::: moniker-end

### <a name="add-xml-format-support"></a>Přidat podporu formátu XML

::: moniker range="<= aspnetcore-2.2"

Chcete-li přidat podporu formátování XML v ASP.NET Core 2,2 nebo starší, nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. formátovací Modules. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .

::: moniker-end

Formátovací moduly XML implementované pomocí `System.Xml.Serialization.XmlSerializer` lze nakonfigurovat voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> v `Startup.ConfigureServices`:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

Formátovací moduly XML implementované pomocí `System.Runtime.Serialization.DataContractSerializer` lze také nakonfigurovat voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> v `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .AddXmlDataContractSerializerFormatters();
```

Jakmile přidáte podporu pro formátování XML, metody kontroleru by měly vracet odpovídající formát na základě `Accept` hlavičky žádosti, jak ukazuje tento příklad Fiddler:

![Konzola Fiddler: Nepůvodní karta pro požadavek zobrazuje hodnotu přijmout hlavičku je Application/XML. Nepůvodní karta pro odpověď zobrazuje hodnotu hlavičky Content-Type pro Application/XML.](formatting/_static/xml-response.png)

Na kartě kontrolori si můžete prohlédnout nezpracovaný požadavek GET s `Accept: application/xml` hlavičkovou sadou. V podokně odpověď se zobrazí `Content-Type: application/xml` záhlaví `Author` a objekt byl serializován do formátu XML.

Pomocí karty skladatele upravte požadavek na zadání `application/json` `Accept` v hlavičce. Spusťte požadavek a odpověď bude formátována jako JSON:

![Konzola Fiddler: Nepůvodní karta pro požadavek zobrazuje hodnotu přijmout hlavičku je Application/JSON. Nepůvodní karta pro odpověď zobrazuje hodnotu hlavičky Content-Type pro Application/JSON.](formatting/_static/json-response-fiddler.png)

Na tomto snímku obrazovky vidíte, že požadavek nastaví hlavičku `Accept: application/json` a odpověď určuje stejnou hodnotu jako její. `Content-Type` `Author` Objekt je zobrazen v těle odpovědi ve formátu JSON.

### <a name="forcing-a-particular-format"></a>Vynucení konkrétního formátu

Pokud chcete omezit formáty odezvy pro konkrétní akci, můžete použít `[Produces]` filtr. `[Produces]` Filtr určuje formáty odezvy konkrétní akce (nebo kontroleru). Podobně jako u většiny [filtrů](xref:mvc/controllers/filters)můžete tuto možnost použít na akci, řadič nebo globální rozsah.

```csharp
[Produces("application/json")]
public class AuthorsController
```

Filtr vynutí všechny akce `AuthorsController` v rámci, aby vracely odpovědi ve formátu JSON, `Accept` a to i v případě, že se pro aplikaci nakonfigurovaly jiné formátovací moduly a klient zadal hlavičku s požadavkem na jinou dostupnou. `[Produces]` formátovat. Další informace, včetně toho, jak filtry použít globálně, najdete v tématu [filtry](xref:mvc/controllers/filters) .

### <a name="special-case-formatters"></a>Formátování speciálních případů

Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů. Ve výchozím nastavení `string` budou návratové typy formátovány jako *Text/prostý* (*text/HTML* , pokud `Accept` jsou požadovány prostřednictvím záhlaví). Toto chování je možné odebrat odebráním `TextOutputFormatter`. Odstraňte formátovací moduly v `Configure` metodě v *Startup.cs* (viz níže). Akce, které mají návratový typ objektu modelu, vrátí 204 bez odezvy obsahu `null`, pokud se vrátí. Toto chování je možné odebrat odebráním `HttpNoContentOutputFormatter`. Následující kód odstraní `TextOutputFormatter` a `HttpNoContentOutputFormatter`.

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

`TextOutputFormatter` Bezhodnotvrátínávratové`string` typy 406, například. Všimněte si, že pokud formátovací modul XML existuje, naformátuje `string` návratové typy, `TextOutputFormatter` Pokud je odebrán.

`HttpNoContentOutputFormatter`Bez objektů jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu. Například formátovací modul JSON jednoduše vrátí odpověď s tělem `null`, zatímco formátovací modul XML vrátí prázdný element XML s nastaveným atributem. `xsi:nil="true"`

## <a name="response-format-url-mappings"></a>Mapování adres URL formátu odpovědi

Klienti mohou požádat o konkrétní formát v rámci adresy URL, například v řetězci dotazu nebo části cesty, nebo pomocí přípony souboru specifického pro formát, jako je například. XML nebo. JSON. Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API. Příklad:

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

Tato trasa umožní zadat požadovaný formát jako volitelnou příponu souboru. Atribut kontroluje existenci hodnoty formátu `RouteData` v a při vytvoření odpovědi namapuje formát odpovědi na příslušný formátovací modul. `[FormatFilter]`

|           Cestě            |             Modul              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    Výchozí výstupní formátovací modul    |
| `/products/GetById/5.json` | Formátovací modul JSON (Pokud je nakonfigurovaný) |
| `/products/GetById/5.xml`  | Formátovací modul XML (Pokud je nakonfigurovaný)  |
