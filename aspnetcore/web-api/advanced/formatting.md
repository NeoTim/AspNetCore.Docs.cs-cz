---
title: Formátování dat odpovědi v ASP.NET Core Web API
author: ardalis
description: Naučte se, jak formátovat data odpovědí v ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 8/22/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 7b19bf54ea9f8a87c26ba7567a7348fcbea997c8
ms.sourcegitcommit: e7dc89620fa02c2ff80bee1e3f77297f97616968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71151182"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formátování dat odpovědi v ASP.NET Core Web API

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC podporuje formátování dat odpovědi. Data odpovědi lze formátovat pomocí konkrétních formátů nebo v reakci na požadovaný formát klienta.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([stažení](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Výsledky akce specifické pro formát

Některé typy výsledků akce jsou specifické pro konkrétní formát, například <xref:Microsoft.AspNetCore.Mvc.JsonResult> a. <xref:Microsoft.AspNetCore.Mvc.ContentResult> Akce mohou vracet výsledky, které jsou formátovány v určitém formátu bez ohledu na Předvolby klienta. Například vrácení `JsonResult` vrátí data ve formátu JSON. Vrácení `ContentResult` nebo řetězec vrací data řetězce ve formátu prostého textu.

Akce není nutná k vrácení určitého konkrétního typu. ASP.NET Core podporuje návratovou hodnotu objektu.  Výsledky z akcí, které vracejí objekty, které <xref:Microsoft.AspNetCore.Mvc.IActionResult> nejsou typy, jsou serializovány <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> pomocí příslušné implementace. Další informace naleznete v tématu <xref:web-api/action-return-types>.

Integrovaná pomocná metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> vrací data ve formátu JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Ukázka stažení vrátí seznam autorů. Pomocí nástrojů pro vývojáře v prohlížeči F12 nebo [příspěvku](https://www.getpostman.com/tools) s předchozím kódem:

* Zobrazí se hlavička odpovědi obsahující **typ obsahu:** `application/json; charset=utf-8` .
* Zobrazí se hlavičky žádosti. Například `Accept` záhlaví. `Accept` Hlavička je ignorována v předchozím kódu.

Chcete-li vrátit data ve formátu prostého textu <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> , použijte aplikaci <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a pomocníka:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

V předchozím kódu `Content-Type` je `text/plain`vráceno. Vrácení řetězcového doručení `Content-Type`: `text/plain`

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Pro akce s více návratovými typy vrátí `IActionResult`. Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.

## <a name="content-negotiation"></a>Vyjednávání obsahu

K domlouvání obsahu dojde, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Výchozí formát používaný ASP.NET Core je [JSON](https://json.org/). Vyjednávání obsahu je:

* <xref:Microsoft.AspNetCore.Mvc.ObjectResult>Implementuje.
* Integrováno do výsledků akce specifické pro kód stavu vrácených z pomocných metod. Pomocné metody výsledků akcí jsou založeny na `ObjectResult`.

Při vrácení typu modelu je `ObjectResult`návratový typ.

Následující metoda akce používá `Ok` pomocné metody a: `NotFound`

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Odpověď ve formátu JSON se vrátí, pokud není požadován jiný formát a server může vracet požadovaný formát. Nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [Poster](https://www.getpostman.com/tools) , `Accept` mohou nastavit hlavičku pro určení návratového formátu. `Accept` Když obsahuje typ, který server podporuje, vrátí se tento typ.

Ve výchozím nastavení ASP.NET Core podporuje pouze JSON. Pro aplikace, které nemění výchozí hodnoty, jsou odpovědi ve formátu JSON vždycky vráceny bez ohledu na požadavky klienta. V další části se dozvíte, jak přidat další formátovací moduly.

Akce kontroleru mohou vracet POCOs (objekty CLR ve starém formátu). Když se vrátí POCO, modul runtime automaticky vytvoří `ObjectResult` objekt, který zabalí objekt. Klient získá formátovaný serializovaný objekt. Pokud je `null`vrácen objekt `204 No Content` , je vrácena odpověď.

Vrací se typ objektu:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

V předchozím kódu vrátí `200 OK` požadavek na platný alias autora odpověď s daty autora. Požadavek na neplatný alias vrací `204 No Content` odpověď.

### <a name="the-accept-header"></a>Hlavička Accept

Po`Accept` zobrazení hlavičky v požadavku se *domlouvání* obsahu provede. Když požadavek obsahuje hlavičku Accept, ASP.NET Core:

* Vytvoří výčet typů médií v hlavičce Accept v upřednostňovaném pořadí.
* Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z určených formátů.

Pokud se nenajde žádný formátovací modul, který může splnit požadavek klienta, ASP.NET Core:

* Vrátí `406 Not Acceptable` , <xref:Microsoft.AspNetCore.Mvc.MvcOptions> Pokud byla nastavena, nebo-
* Pokusí se najít první formátovací modul, který může vytvořit odpověď.

Pokud není pro požadovaný formát nakonfigurován žádný formátovací modul, je použit první formátovací modul, který může objekt formátovat. Pokud se `Accept` v požadavku nezobrazí žádná hlavička:

* První formátovací modul, který může zpracovat objekt, je použit k serializaci odpovědi.
* Nedochází k žádnému vyjednávání. Server určí, který formát se má vrátit.

Pokud hlavička Accept obsahuje `*/*`, záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` <xref:Microsoft.AspNetCore.Mvc.MvcOptions>není nastavené na hodnotu true.

### <a name="browsers-and-content-negotiation"></a>Prohlížeče a vyjednávání obsahu

Na rozdíl od typických klientů rozhraní API webové `Accept` prohlížeče poskytují hlavičky. Webový prohlížeč určí mnoho formátů, včetně zástupných znaků. Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče:

* `Accept` Záhlaví je ignorováno.
* Pokud není nakonfigurováno jinak, vrátí se obsah ve formátu JSON.

Díky tomu je v prohlížečích k dispozici jednotnější prostředí pro využívání rozhraní API.

Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hlavičky přijetí <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> prohlížeče `true`, nastavte na:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Konfigurace formátovacích modulů

Aplikace, které vyžadují podporu dalších formátů, můžou přidat příslušné balíčky NuGet a nakonfigurovat podporu. Pro vstup a výstup existují samostatné formátovací moduly. Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding). Výstupní formátovací moduly se používají k formátování odpovědí. Informace o vytvoření vlastního formátovacího modulu naleznete v tématu [Custom formátovací modules](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Přidat podporu formátu XML

Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním: <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Předchozí kód serializace výsledků pomocí `XmlSerializer`.

Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě `Accept` hlavičky požadavku.

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurace formátovacích modulů založených na System. text. JSON

Funkce pro `System.Text.Json`formátovací moduly založené na službě se dají konfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`. Příklad:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Přidání podpory formátu JSON založeného na Newtonsoft. JSON

Před ASP.NET Core 3,0 byly použity výchozí formátovací moduly JSON implementované pomocí `Newtonsoft.Json` balíčku. V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json`. Podpora formátování a funkcí `Startup.ConfigureServices` založenýchnazákladechjekdispozicipoinstalacibalíčkuNuGetMicrosoft.AspNetCore.Mvc.NewtonsoftJsonajehokonfiguraciv.`Newtonsoft.Json` [](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Některé funkce nemusí fungovat správně s `System.Text.Json`formátovacími moduly a vyžadují odkaz `Newtonsoft.Json`na formátování na základě. I nadále používat `Newtonsoft.Json`formátovací moduly založené na tom, pokud aplikace:

* Používá `Newtonsoft.Json` atributy. Například `[JsonProperty]` nebo `[JsonIgnore]`.
* Přizpůsobuje nastavení serializace.
* Spoléhá na funkce, `Newtonsoft.Json` které poskytuje.
* Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` akceptuje `JsonSerializerSettings` instance, která je specifická pro `Newtonsoft.Json`.
* Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .

Funkce pro `Newtonsoft.Json`formátovací moduly založené na službě se dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefautlContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`. Příklad:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Přidat podporu formátu XML

Formátování XML vyžaduje balíček NuGet [Microsoft. AspNetCore. Mvc. formátovací modul. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .

Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním: <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Předchozí kód serializace výsledků pomocí `XmlSerializer`.

Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě `Accept` hlavičky požadavku.

::: moniker-end

### <a name="specify-a-format"></a>Zadat formát

Chcete-li omezit formáty odpovědi, použijte [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr. Podobně jako [](xref:mvc/controllers/filters)u většiny `[Produces]` filtrů lze použít akci, řadič nebo globální rozsah:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Předchozí [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr:

* Vynutí, aby všechny akce v rámci kontroleru vracely odpovědi ve formátu JSON.
* Pokud jsou nakonfigurovány jiné formátovací moduly a klient určí jiný formát, bude vrácen kód JSON.

Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formátování speciálních případů

Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů. Ve výchozím nastavení `string` jsou návratové typy formátovány jako *textové/jednoduché* (*text/HTML* , pokud `Accept` jsou požadovány prostřednictvím hlavičky). Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter>. Formátovací moduly jsou v `Configure` metodě odebrány. Akce, které mají vrácen návratový typ `204 No Content` objektu modelu při návratu. `null` Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>. Následující kód odstraní `TextOutputFormatter` a `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end

Bez rozhraní `TextOutputFormatter` `string` vrátí návratové typy `406 Not Acceptable`. Pokud formátovací modul XML existuje, formátuje `string` návratové typy, `TextOutputFormatter` Pokud je odebrán.

`HttpNoContentOutputFormatter`Bez objektů jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu. Příklad:

* Formátovací modul JSON vrátí odpověď s tělem `null`.
* Formátovací modul XML vrátí prázdný element XML s nastaveným atributem `xsi:nil="true"` .

## <a name="response-format-url-mappings"></a>Mapování adres URL formátu odpovědi

Klienti mohou požádat o konkrétní formát v rámci adresy URL, například:

* V řetězci dotazu nebo v části cesty.
* Pomocí přípony souboru specifické pro formát, jako je například. XML nebo. JSON.

Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API. Příklad:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru. Atribut kontroluje existenci hodnoty formátu `RouteData` v a při vytvoření odpovědi mapuje formát odpovědi na příslušný formátovací modul. [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)

|           Cestě            |             Modul              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    Výchozí výstupní formátovací modul    |
| `/products/GetById/5.json` | Formátovací modul JSON (Pokud je nakonfigurovaný) |
| `/products/GetById/5.xml`  | Formátovací modul XML (Pokud je nakonfigurovaný)  |
