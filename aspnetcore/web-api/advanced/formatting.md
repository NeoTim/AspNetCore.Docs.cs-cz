---
title: Formátování dat odpovědi v ASP.NET Core Web API
author: ardalis
description: Naučte se, jak formátovat data odpovědí v ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 8/22/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 0dd8b3b5ec58a199db086c4c0b0f057d26afd589
ms.sourcegitcommit: 7a2c820692f04bfba04398641b70f27fa15391f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2019
ms.locfileid: "72290629"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formátování dat odpovědi v ASP.NET Core Web API

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC podporuje formátování dat odpovědi. Data odpovědi lze formátovat pomocí konkrétních formátů nebo v reakci na požadovaný formát klienta.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Výsledky akce specifické pro formát

Některé typy výsledků akce jsou specifické pro konkrétní formát, například <xref:Microsoft.AspNetCore.Mvc.JsonResult> a <xref:Microsoft.AspNetCore.Mvc.ContentResult>. Akce mohou vracet výsledky, které jsou formátovány v určitém formátu bez ohledu na Předvolby klienta. Například vrácení `JsonResult` vrací data ve formátu JSON. Vrácení `ContentResult` nebo řetězce vrátí data řetězce ve formátu prostého textu.

Akce není nutná k vrácení určitého konkrétního typu. ASP.NET Core podporuje návratovou hodnotu objektu.  Výsledky z akcí, které vracejí objekty, které nejsou <xref:Microsoft.AspNetCore.Mvc.IActionResult>, jsou serializovány pomocí příslušné implementace <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>. Další informace najdete v tématu <xref:web-api/action-return-types>.

Integrovaná pomocná metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> vrátí data ve formátu JSON: [!code-csharp @ no__t-2

Ukázka stažení vrátí seznam autorů. Pomocí nástrojů pro vývojáře v prohlížeči F12 nebo [příspěvku](https://www.getpostman.com/tools) s předchozím kódem:

* Zobrazí se hlavička odpovědi obsahující **Content-Type:** `application/json; charset=utf-8`.
* Zobrazí se hlavičky žádosti. Například hlavička `Accept`. Předchozí kód ignoruje hlavičku `Accept`.

Chcete-li vrátit data ve formátu prostého textu, použijte <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a pomocníka <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

V předchozím kódu je vráceno `Content-Type` `text/plain`. Vrácení řetězce poskytuje `Content-Type` `text/plain`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Pro akce s více návratovými typy vrátí `IActionResult`. Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.

## <a name="content-negotiation"></a>Vyjednávání obsahu

K domlouvání obsahu dojde, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Výchozí formát používaný ASP.NET Core je [JSON](https://json.org/). Vyjednávání obsahu je:

* Implementováno pomocí <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.
* Integrováno do výsledků akce specifické pro kód stavu vrácených z pomocných metod. Pomocné metody výsledků akcí jsou založené na `ObjectResult`.

Při vrácení typu modelu je návratový typ `ObjectResult`.

Následující metoda akce používá pomocné metody `Ok` a `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Ve výchozím nastavení ASP.NET Core podporuje typy médií `application/json`, `text/json` a `text/plain`. Nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [Poster](https://www.getpostman.com/tools) , můžou nastavit hlavičku požadavku `Accept` na zadání návratového formátu. Pokud hlavička `Accept` obsahuje typ, který server podporuje, vrátí se tento typ. V další části se dozvíte, jak přidat další formátovací moduly.

Akce kontroleru mohou vracet POCOs (objekty CLR ve starém formátu). Když se vrátí POCO, modul runtime automaticky vytvoří `ObjectResult`, který tento objekt zabalí. Klient získá formátovaný serializovaný objekt. Pokud je vrácen objekt `null`, je vrácena odpověď `204 No Content`.

Vrací se typ objektu:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

V předchozím kódu vrátí požadavek na platný alias autora odpověď `200 OK` s daty autora. Požadavek na neplatný alias vrátí odpověď `204 No Content`.

### <a name="the-accept-header"></a>Hlavička Accept

Pokud se v požadavku objeví hlavička `Accept`, bude provedeno *vyjednávání* obsahu. Když požadavek obsahuje hlavičku Accept, ASP.NET Core:

* Vytvoří výčet typů médií v hlavičce Accept v upřednostňovaném pořadí.
* Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z určených formátů.

Pokud se nenajde žádný formátovací modul, který může splnit požadavek klienta, ASP.NET Core:

* Vrátí `406 Not Acceptable`, pokud byl nastaven <xref:Microsoft.AspNetCore.Mvc.MvcOptions>, nebo-
* Pokusí se najít první formátovací modul, který může vytvořit odpověď.

Pokud není pro požadovaný formát nakonfigurován žádný formátovací modul, je použit první formátovací modul, který může objekt formátovat. Pokud se v požadavku nezobrazí hlavička `Accept`:

* První formátovací modul, který může zpracovat objekt, je použit k serializaci odpovědi.
* Nedochází k žádnému vyjednávání. Server určí, který formát se má vrátit.

Pokud hlavička Accept obsahuje `*/*`, záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` není v <xref:Microsoft.AspNetCore.Mvc.MvcOptions> nastaveno na hodnotu true.

### <a name="browsers-and-content-negotiation"></a>Prohlížeče a vyjednávání obsahu

Na rozdíl od typických klientů rozhraní API poskytují webové prohlížeče hlavičky `Accept`. Webový prohlížeč určí mnoho formátů, včetně zástupných znaků. Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče:

* Hlavička `Accept` je ignorována.
* Pokud není nakonfigurováno jinak, vrátí se obsah ve formátu JSON.

Díky tomu je v prohlížečích k dispozici jednotnější prostředí pro využívání rozhraní API.

Pokud chcete aplikaci nakonfigurovat tak, aby přijímala odpovědi prohlížeče, nastavte <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true`:

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

Předchozí kód serializace výsledků pomocí `XmlSerializer`.

Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě hlavičky `Accept` požadavku.

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurace formátovacích modulů založených na System. text. JSON

Funkce pro @no__t formátovací moduly založené na -0 se dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.

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

Před ASP.NET Core 3,0 byly použity výchozí formátovací moduly JSON implementované pomocí balíčku `Newtonsoft.Json`. V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json`. Podpora formátování a funkcí založených na @no__t 0 je k dispozici po instalaci balíčku NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) a jeho konfiguraci v `Startup.ConfigureServices`.

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Některé funkce nemusí fungovat dobře s @no__t formátovacími moduly založenými na -0 a vyžadují odkaz na @no__t formátovací modul založený na -1. Nadále používat @no__t formátovací moduly založené na -0, pokud aplikace:

* Používá `Newtonsoft.Json` atributů. Například `[JsonProperty]` nebo `[JsonIgnore]`.
* Přizpůsobuje nastavení serializace.
* Spoléhá na funkce, které poskytuje `Newtonsoft.Json`.
* Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` přijímá instanci `JsonSerializerSettings`, která je specifická pro `Newtonsoft.Json`.
* Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .

Funkce pro @no__t formátovací moduly založené na -0 se dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:

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

Předchozí kód serializace výsledků pomocí `XmlSerializer`.

Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě hlavičky `Accept` požadavku.

::: moniker-end

### <a name="specify-a-format"></a>Zadat formát

Chcete-li omezit formáty odpovědi, použijte filtr [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) . Podobně jako u většiny [filtrů](xref:mvc/controllers/filters)`[Produces]` lze použít na akci, řadič nebo globální rozsah:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Předchozí filtr [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) :

* Vynutí, aby všechny akce v rámci kontroleru vracely odpovědi ve formátu JSON.
* Pokud jsou nakonfigurovány jiné formátovací moduly a klient určí jiný formát, bude vrácen kód JSON.

Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formátování speciálních případů

Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů. Ve výchozím nastavení jsou návratové typy `string` formátovány jako *textové/jednoduché* (*text/HTML* , pokud jsou požadovány prostřednictvím hlavičky `Accept`). Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter>. Formátovací moduly jsou odebrány v metodě `Configure`. Akce, které mají návratový typ Object, vrátí `204 No Content` při vrácení `null`. Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>. Následující kód odstraní `TextOutputFormatter` a `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end

Bez `TextOutputFormatter` `string` návratové typy vrátí `406 Not Acceptable`. Pokud formátovací modul XML existuje, formátuje návratové typy `string`, pokud je odebráno `TextOutputFormatter`.

Bez `HttpNoContentOutputFormatter` jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu. Příklad:

* Formátovací modul JSON vrátí odpověď s tělem `null`.
* Formátovací modul XML vrátí prázdný element XML s atributem `xsi:nil="true"` nastavenou.

## <a name="response-format-url-mappings"></a>Mapování adres URL formátu odpovědi

Klienti mohou požádat o konkrétní formát v rámci adresy URL, například:

* V řetězci dotazu nebo v části cesty.
* Pomocí přípony souboru specifické pro formát, jako je například. XML nebo. JSON.

Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API. Příklad:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru. Atribut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) kontroluje existenci hodnoty formátu v `RouteData` a při vytvoření odpovědi mapuje formát odpovědi na příslušný formátovací modul.

|           Cestě        |             Modul              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Výchozí výstupní formátovací modul    |
| `/api/products/5.json` | Formátovací modul JSON (Pokud je nakonfigurovaný) |
| `/api/products/5.xml`  | Formátovací modul XML (Pokud je nakonfigurovaný)  |
