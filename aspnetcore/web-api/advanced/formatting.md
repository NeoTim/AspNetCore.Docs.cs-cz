---
title: Formátování dat odpovědí v ASP.NET základního webového rozhraní API
author: ardalis
description: Přečtěte si, jak formátovat data odpovědí v ASP.NET základní webové rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642712"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formátování dat odpovědí v ASP.NET základního webového rozhraní API

[Podle Rick Anderson](https://twitter.com/RickAndMSFT) a Steve [Smith](https://ardalis.com/)

ASP.NET Core MVC podporuje formátování dat odpovědí. Data odpovědí mohou být formátována pomocí určitých formátů nebo v reakci na formát požadovaný klientem.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="format-specific-action-results"></a>Výsledky akce specifické pro formát

Některé typy výsledků akce jsou specifické pro <xref:Microsoft.AspNetCore.Mvc.JsonResult> <xref:Microsoft.AspNetCore.Mvc.ContentResult>určitý formát, například a . Akce mohou vrátit výsledky, které jsou formátovány v určitém formátu, bez ohledu na předvolby klienta. Například vrácení `JsonResult` vrátí data ve formátu JSON. Vrácení `ContentResult` nebo řetězec vrátí data řetězce ve formátu prostého textu.

Akce není nutné vrátit žádný konkrétní typ. ASP.NET Core podporuje libovolnou vrácenou hodnotu objektu.  Výsledky z akcí, které <xref:Microsoft.AspNetCore.Mvc.IActionResult> vracejí objekty, které <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> nejsou typy jsou serializovány pomocí příslušné implementace. Další informace naleznete v tématu <xref:web-api/action-return-types>.

Vestavěná pomocná <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> metoda vrátí data ve formátu JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Ukázka stažení vrátí seznam autorů. Použití nástrojů pro vývojáře prohlížeče F12 nebo [Postman](https://www.getpostman.com/tools) s předchozím kódem:

* Zobrazí se hlavička odpovědi obsahující **typ obsahu:** `application/json; charset=utf-8` .
* Zobrazí se hlavičky požadavku. Například `Accept` záhlaví. Záhlaví `Accept` je ignorováno předchozím kódem.

Chcete-li vrátit data formátovaná ve formátu prostého textu, použijte <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> pomocné ho:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

V předchozím kódu vrácené `Content-Type` `text/plain`je . Vrácení řetězec dodává `Content-Type` `text/plain`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Pro akce s více `IActionResult`návratovými typy vraťte . Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.

## <a name="content-negotiation"></a>Vyjednávání obsahu

K vyjednávání obsahu dochází, když klient určí [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Výchozím formátem používaným ASP.NET Core je [JSON](https://json.org/). Vyjednávání obsahu je:

* Implementována společností <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.
* Zabudované do výsledků akce specifických pro stavový kód vrácené z pomocných metod. Pomocné metody výsledků akce `ObjectResult`jsou založeny na .

Při vrácení typu modelu je `ObjectResult`návratový typ .

Následující metoda akce `Ok` používá `NotFound` a pomocné metody:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Ve výchozím nastavení `application/json`podporuje `text/json`ASP.NET `text/plain` Core typy médií a typy médií. Nástroje jako [Fiddler](https://www.telerik.com/fiddler) nebo [Postman](https://www.getpostman.com/tools) můžete nastavit hlavičku `Accept` požadavku určit formát vrácení. Pokud `Accept` záhlaví obsahuje typ, který server podporuje, je tento typ vrácen. V další části se zobrazí, jak přidat další formatters.

Akce řadiče může vrátit POCO (Plain Old CLR Objects). Když je vrácena POCO, runtime `ObjectResult` automaticky vytvoří, který obtéká objekt. Klient získá formátovaný serializovaný objekt. Pokud je `null`vrácený objekt `204 No Content` , je vrácena odpověď.

Vrácení typu objektu:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

V předchozím kódu vrátí požadavek na platný `200 OK` alias autora odpověď s daty autora. Požadavek na neplatný `204 No Content` alias vrátí odpověď.

### <a name="the-accept-header"></a>Hlavička Přijmout

*Vyjednávání* obsahu probíhá, když se v žádosti zobrazí `Accept` záhlaví. Pokud požadavek obsahuje hlavičku přijetí, ASP.NET jádro:

* Vyjmenovává typy médií v záhlaví přijmout v pořadí předvoleb.
* Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom ze zadaných formátů.

Pokud není nalezen žádný pohovce, který může uspokojit požadavek klienta, ASP.NET Core:

* `406 Not Acceptable` Vrátí, <xref:Microsoft.AspNetCore.Mvc.MvcOptions> pokud byla nastavena, nebo -
* Pokusí se najít první propojka, která může vytvořit odpověď.

Pokud žádný formátovací modul je nakonfigurován pro požadovaný formát, první formátovací modul, který může formátovat objekt se používá. Pokud `Accept` se v požadavku nezobrazí žádná hlavička:

* První formátování, které může zpracovat objekt se používá k serializaci odpovědi.
* Žádné vyjednávání se nekoná. Server určuje, jaký formát má být vrácen.

Pokud hlavička `*/*`Přijmout obsahuje , je `RespectBrowserAcceptHeader` záhlaví ignorováno, pokud není v . <xref:Microsoft.AspNetCore.Mvc.MvcOptions>

### <a name="browsers-and-content-negotiation"></a>Prohlížeče a vyjednávání obsahu

Na rozdíl od běžných `Accept` klientů rozhraní API poskytují webové prohlížeče záhlaví. Webový prohlížeč určuje mnoho formátů, včetně zástupných znaků. Ve výchozím nastavení, když rozhraní framework zjistí, že požadavek pochází z prohlížeče:

* Záhlaví `Accept` je ignorováno.
* Obsah je vrácen v JSON, pokud není nakonfigurováno jinak.

To poskytuje konzistentnější prostředí napříč prohlížeči při využívání api.

Chcete-li nakonfigurovat aplikaci tak, <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> `true`aby uctila záhlaví prohlížeče, nastavte na :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Konfigurace formatters

Aplikace, které potřebují podporovat další formáty můžete přidat příslušné balíčky NuGet a konfigurovat podporu. Existují samostatné formatters pro vstup a výstup. Vstupní formatters jsou používány [model vazby](xref:mvc/models/model-binding). Výstup formatters se používají k formátování odpovědí. Informace o vytvoření vlastního modulu průhlednce naleznete [v tématu Custom Formatters](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Přidat podporu formátu XML

Formáty XML <xref:System.Xml.Serialization.XmlSerializer> implementované pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>jsou konfigurovány voláním :

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Předchozí kód serializuje výsledky pomocí `XmlSerializer`.

Při použití předchozího kódu metody řadiče vrátí příslušný formát `Accept` na základě hlavičky požadavku.

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurace formanace založené na souboru System.Text.Json

Funkce `System.Text.Json`formatters založené lze konfigurovat `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`pomocí .

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Možnosti serializace výstupu pro individuální použití lze `JsonResult`konfigurovat pomocí aplikace . Příklad:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Přidat podporu formátu JSON založeného na newtonsoftu.Json

Před ASP.NET Core 3.0, výchozí použité JSON `Newtonsoft.Json` formatters implementována pomocí balíčku. V ASP.NET jádrem 3.0 nebo novějším, výchozí `System.Text.Json`formatters JSON jsou založeny na . Podpora `Newtonsoft.Json` formatters a funkcí založených na webu je k dispozici instalací balíčku [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet a jeho konfigurací v aplikaci `Startup.ConfigureServices`.

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Některé funkce nemusí fungovat `System.Text.Json`dobře s formatters založené `Newtonsoft.Json`a vyžadují odkaz na formatters založené na bázi. Pokračovat v `Newtonsoft.Json`používání formatters založené na základě, pokud aplikace:

* Používá `Newtonsoft.Json` atributy. Příkladem je `[JsonProperty]` nebo `[JsonIgnore]`.
* Přizpůsobí nastavení serializace.
* Spoléhá na funkce, které `Newtonsoft.Json` poskytuje.
* Konfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Před ASP.NET Core 3.0, přijímá `JsonSerializerSettings` instanci, `Newtonsoft.Json` `JsonResult.SerializerSettings` která je specifická pro .
* Generuje dokumentaci [OpenAPI.](<xref:tutorials/web-api-help-pages-using-swagger>)

Funkce `Newtonsoft.Json`formatters založené na bázi `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`lze konfigurovat pomocí :

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Možnosti serializace výstupu pro individuální použití lze `JsonResult`konfigurovat pomocí aplikace . Příklad:

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

Formátování XML vyžaduje balíček [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet.

Formáty XML <xref:System.Xml.Serialization.XmlSerializer> implementované pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>jsou konfigurovány voláním :

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Předchozí kód serializuje výsledky pomocí `XmlSerializer`.

Při použití předchozího kódu by metody řadiče měly vrátit `Accept` příslušný formát na základě hlavičky požadavku.

::: moniker-end

### <a name="specify-a-format"></a>Určení formátu

Chcete-li omezit formáty [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) odpovědí, použijte filtr. Stejně jako `[Produces]` [většina filtrů](xref:mvc/controllers/filters)lze použít v akci, kontroleru nebo globálním oboru:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Předchozí [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr:

* Vynutí všechny akce v rámci řadiče vrátit odpovědi ve formátu JSON.
* Pokud jsou nakonfigurovány jiné formatters a klient určuje jiný formát, je vrácena JSON.

Další informace naleznete v [tématu Filtry](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Zvláštní případ pro věci

Některé zvláštní případy jsou implementovány pomocí vestavěných formatters. Ve výchozím `string` nastavení jsou návratové typy formátovány jako *text/prostý* *(text/html* na vyžádání přes hlavičku). `Accept` Toto chování lze odstranit <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>odebráním . Formatters jsou odstraněny `ConfigureServices` v metodě. Akce, které mají objekt `204 No Content` modelu návratový typ vrátit při `null`vrácení . Toto chování lze odstranit <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>odebráním . Následující kód odebere `StringOutputFormatter` `HttpNoContentOutputFormatter`a .

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Bez `StringOutputFormatter`, předdefinované formáty formátů formátů formátů json formáty formátů `string` návratové. Pokud je odebrán předdefinovaný formátovací modul JSON a formát formátu XML `string` je k dispozici, formáty formátů formátů xml vrátí typy. V `string` opačném případě `406 Not Acceptable`vrátí typy vrátí .

Bez `HttpNoContentOutputFormatter`, null objekty jsou formátovány pomocí nakonfigurované formátovací modul. Příklad:

* JSON forové matné modul `null`vrátí odpověď s tělo .
* Formátovací modul XML vrátí prázdný element `xsi:nil="true"` XML se sadou atributů.

## <a name="response-format-url-mappings"></a>Mapování adres URL ve formátu odpovědi

Klienti mohou požádat o určitý formát jako součást adresy URL, například:

* V řetězci dotazu nebo části cesty.
* Pomocí přípony souboru specifického pro formát, například XML nebo JSON.

Mapování z cesty požadavku by mělo být zadáno v trase, kterou rozhraní API používá. Příklad:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru. Atribut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) zkontroluje existenci hodnoty formátu v `RouteData` a mapuje formát odpovědi na příslušný formátovací modul při vytvoření odpovědi.

|           Trasa        |             Formátovací modul              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Výchozí výstupní formatter    |
| `/api/products/5.json` | JSON formovní modul (je-li nakonfigurován) |
| `/api/products/5.xml`  | Formátovací modul XML (pokud je nakonfigurován)  |
