---
title: Formátování dat odpovědi v ASP.NET Core Web API
author: ardalis
description: Naučte se, jak formátovat data odpovědí v ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/05/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 908016720ade67a02ebe30d1dcb7929ad7592270
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661901"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formátování dat odpovědi v ASP.NET Core Web API

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC podporuje formátování dat odpovědi. Data odpovědi lze formátovat pomocí konkrétních formátů nebo v reakci na požadovaný formát klienta.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Výsledky akce specifické pro formát

Některé typy výsledků akce jsou specifické pro konkrétní formát, například <xref:Microsoft.AspNetCore.Mvc.JsonResult> a <xref:Microsoft.AspNetCore.Mvc.ContentResult>. Akce mohou vracet výsledky, které jsou formátovány v určitém formátu bez ohledu na Předvolby klienta. Například vrácení `JsonResult` vrátí data ve formátu JSON. Vrácení `ContentResult` nebo řetězce vrátí data řetězce ve formátu prostého textu.

Akce není nutná k vrácení určitého konkrétního typu. ASP.NET Core podporuje návratovou hodnotu objektu.  Výsledky z akcí, které vracejí objekty, které nejsou <xref:Microsoft.AspNetCore.Mvc.IActionResult> typy, jsou serializovány pomocí příslušné <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementace. Další informace naleznete v tématu <xref:web-api/action-return-types>.

Integrovaná pomocná metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> vrátí data ve formátu JSON: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Ukázka stažení vrátí seznam autorů. Pomocí nástrojů pro vývojáře v prohlížeči F12 nebo [příspěvku](https://www.getpostman.com/tools) s předchozím kódem:

* Zobrazí se hlavička odpovědi obsahující **Content-Type:** `application/json; charset=utf-8`.
* Zobrazí se hlavičky žádosti. Například hlavička `Accept`. Předchozí kód ignoruje hlavičku `Accept`.

Chcete-li vrátit data ve formátu prostého textu, použijte <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a pomocníka <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

V předchozím kódu je vrácen `Content-Type` `text/plain`. Vrácení řetězce doručí `Content-Type` `text/plain`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Pro akce s více návratovými typy vrátí `IActionResult`. Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.

## <a name="content-negotiation"></a>Vyjednávání obsahu

K domlouvání obsahu dojde, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Výchozí formát používaný ASP.NET Core je [JSON](https://json.org/). Vyjednávání obsahu je:

* Implementováno pomocí <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.
* Integrováno do výsledků akce specifické pro kód stavu vrácených z pomocných metod. Pomocné metody výsledků akcí jsou založené na `ObjectResult`.

Když je vrácen typ modelu, návratový typ je `ObjectResult`.

Následující metoda akce používá pomocné metody `Ok` a `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Ve výchozím nastavení ASP.NET Core podporuje typy médií `application/json`, `text/json`a `text/plain`. Nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [Poster](https://www.getpostman.com/tools) , můžou nastavit hlavičku pro `Accept` žádosti o určení návratového formátu. Pokud hlavička `Accept` obsahuje typ, který server podporuje, vrátí se tento typ. V další části se dozvíte, jak přidat další formátovací moduly.

Akce kontroleru mohou vracet POCOs (objekty CLR ve starém formátu). Když se vrátí POCO, modul runtime automaticky vytvoří `ObjectResult`, který tento objekt zabalí. Klient získá formátovaný serializovaný objekt. Pokud je vrácen objekt `null`, je vrácena odpověď `204 No Content`.

Vrací se typ objektu:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

V předchozím kódu vrátí požadavek na platný alias autora `200 OK` reakci s daty autora. Požadavek na neplatný alias vrací odpověď `204 No Content`.

### <a name="the-accept-header"></a>Hlavička Accept

K *vyjednání* obsahu dochází, když se v žádosti objeví hlavička `Accept`. Když požadavek obsahuje hlavičku Accept, ASP.NET Core:

* Vytvoří výčet typů médií v hlavičce Accept v upřednostňovaném pořadí.
* Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z určených formátů.

Pokud se nenajde žádný formátovací modul, který může splnit požadavek klienta, ASP.NET Core:

* Vrátí `406 Not Acceptable`, pokud <xref:Microsoft.AspNetCore.Mvc.MvcOptions> nastavena, nebo-
* Pokusí se najít první formátovací modul, který může vytvořit odpověď.

Pokud není pro požadovaný formát nakonfigurován žádný formátovací modul, je použit první formátovací modul, který může objekt formátovat. Pokud se v požadavku nezobrazí hlavička `Accept`:

* První formátovací modul, který může zpracovat objekt, je použit k serializaci odpovědi.
* Nedochází k žádnému vyjednávání. Server určí, který formát se má vrátit.

Pokud hlavička Accept obsahuje `*/*`, záhlaví se ignoruje, pokud není u <xref:Microsoft.AspNetCore.Mvc.MvcOptions>nastavená hodnota `RespectBrowserAcceptHeader` na hodnotu true.

### <a name="browsers-and-content-negotiation"></a>Prohlížeče a vyjednávání obsahu

Na rozdíl od typických klientů rozhraní API poskytují webové prohlížeče `Accept` hlavičky. Webový prohlížeč určí mnoho formátů, včetně zástupných znaků. Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče:

* Hlavička `Accept` je ignorována.
* Pokud není nakonfigurováno jinak, vrátí se obsah ve formátu JSON.

Díky tomu je v prohlížečích k dispozici jednotnější prostředí pro využívání rozhraní API.

Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hlavičky přijetí prohlížečem, nastavte <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true`:

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

Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Předchozí kód serializace výsledky pomocí `XmlSerializer`.

Při použití předchozího kódu vrátí metody kontroleru odpovídající formát na základě hlavičky `Accept` požadavku.

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurace formátovacích modulů založených na System. text. JSON

Funkce pro formátovací moduly založené na `System.Text.Json`lze konfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.

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

Před ASP.NET Core 3,0 byly použity výchozí formátovací moduly JSON implementované pomocí balíčku `Newtonsoft.Json`. V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json`. Podpora formátování a funkcí založených na `Newtonsoft.Json` je k dispozici po instalaci balíčku NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) a jeho konfiguraci v `Startup.ConfigureServices`.

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Některé funkce nemusí fungovat správně s `System.Text.Json`formátovacími moduly a vyžadují odkaz na formátovací moduly založené na `Newtonsoft.Json`. I nadále používat formátovací moduly založené na `Newtonsoft.Json`, pokud aplikace:

* Používá `Newtonsoft.Json` atributy. Například `[JsonProperty]` nebo `[JsonIgnore]`.
* Přizpůsobuje nastavení serializace.
* Spoléhá na funkce, které `Newtonsoft.Json` poskytuje.
* Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` přijímá instanci `JsonSerializerSettings`, která je specifická pro `Newtonsoft.Json`.
* Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .

Funkce pro formátovací moduly založené na `Newtonsoft.Json`se dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

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

Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Předchozí kód serializace výsledky pomocí `XmlSerializer`.

Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě hlavičky `Accept` požadavku.

::: moniker-end

### <a name="specify-a-format"></a>Zadat formát

Chcete-li omezit formáty odpovědi, použijte filtr [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) . Podobně jako u většiny [filtrů](xref:mvc/controllers/filters)můžete `[Produces]` použít na akci, řadič nebo globální rozsah:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Předchozí [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr:

* Vynutí, aby všechny akce v rámci kontroleru vracely odpovědi ve formátu JSON.
* Pokud jsou nakonfigurovány jiné formátovací moduly a klient určí jiný formát, bude vrácen kód JSON.

Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formátování speciálních případů

Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů. Ve výchozím nastavení `string` návratové typy jsou formátovány jako *Text/prostý* (*text/HTML* , pokud jsou požadovány pomocí `Accept` záhlaví). Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>. Formátovací moduly jsou v metodě `ConfigureServices` odebrány. Akce, které mají návratový typ Object, vrátí `204 No Content` při vrácení `null`. Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>. Následující kód odstraní `StringOutputFormatter` a `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Bez `StringOutputFormatter`, předdefinované formáty formátování JSON `string` návratové typy. Pokud je vestavěný formátovací modul JSON odebraný a je k dispozici formátovací modul XML, formátovací formáty XML `string` návratové typy. V opačném případě `string` návratové typy vrátí `406 Not Acceptable`.

Bez `HttpNoContentOutputFormatter`jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu. Příklad:

* Formátovací modul JSON vrátí odpověď s tělem `null`.
* Formátovací modul XML vrátí prázdný element XML s atributem `xsi:nil="true"` sady.

## <a name="response-format-url-mappings"></a>Mapování adres URL formátu odpovědi

Klienti mohou požádat o konkrétní formát v rámci adresy URL, například:

* V řetězci dotazu nebo v části cesty.
* Pomocí přípony souboru specifické pro formát, jako je například. XML nebo. JSON.

Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API. Příklad:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru. Atribut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) kontroluje existenci hodnoty formátu v `RouteData` a při vytvoření odpovědi mapuje formát odpovědi na příslušný formátovací modul.

|           Trasa        |             Modul              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Výchozí výstupní formátovací modul    |
| `/api/products/5.json` | Formátovací modul JSON (Pokud je nakonfigurovaný) |
| `/api/products/5.xml`  | Formátovací modul XML (Pokud je nakonfigurovaný)  |
