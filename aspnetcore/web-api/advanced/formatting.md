---
title: Formátování dat odpovědi v ASP.NET Core Web API
author: ardalis
description: Naučte se, jak formátovat data odpovědí v ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/formatting
ms.openlocfilehash: 8aa94bd1f33d1dd8ce8e7f50468ed60b4ccb2515
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019934"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formátování dat odpovědi v ASP.NET Core Web API

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC podporuje formátování dat odpovědi. Data odpovědi lze formátovat pomocí konkrétních formátů nebo v reakci na požadovaný formát klienta.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Výsledky akce specifické pro formát

Některé typy výsledků akce jsou specifické pro konkrétní formát, například <xref:Microsoft.AspNetCore.Mvc.JsonResult> a <xref:Microsoft.AspNetCore.Mvc.ContentResult> . Akce mohou vracet výsledky, které jsou formátovány v určitém formátu bez ohledu na Předvolby klienta. Například vrácení `JsonResult` vrátí data ve formátu JSON. Vrácení `ContentResult` nebo řetězec vrací data řetězce ve formátu prostého textu.

Akce není nutná k vrácení určitého konkrétního typu. ASP.NET Core podporuje návratovou hodnotu objektu.  Výsledky z akcí, které vracejí objekty, které nejsou <xref:Microsoft.AspNetCore.Mvc.IActionResult> typy, jsou serializovány pomocí příslušné <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementace. Další informace naleznete v tématu <xref:web-api/action-return-types>.

Integrovaná pomocná metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> vrací data ve formátu JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Ukázka stažení vrátí seznam autorů. Pomocí nástrojů pro vývojáře v prohlížeči F12 nebo [příspěvku](https://www.getpostman.com/tools) s předchozím kódem:

* Zobrazí se hlavička odpovědi obsahující **typ obsahu:** `application/json; charset=utf-8` .
* Zobrazí se hlavičky žádosti. Například `Accept` záhlaví. `Accept`Hlavička je ignorována v předchozím kódu.

Chcete-li vrátit data ve formátu prostého textu, použijte aplikaci <xref:Microsoft.AspNetCore.Mvc.ContentResult> a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> pomocníka:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

V předchozím kódu `Content-Type` je vráceno `text/plain` . Vrácení řetězcového doručení `Content-Type` `text/plain` :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Pro akce s více návratovými typy vrátí `IActionResult` . Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.

## <a name="content-negotiation"></a>Vyjednávání obsahu

K domlouvání obsahu dojde, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Výchozí formát používaný ASP.NET Core je [JSON](https://json.org/). Vyjednávání obsahu je:

* Implementuje <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .
* Integrováno do výsledků akce specifické pro kód stavu vrácených z pomocných metod. Pomocné metody výsledků akcí jsou založeny na `ObjectResult` .

Při vrácení typu modelu je návratový typ `ObjectResult` .

Následující metoda akce používá `Ok` `NotFound` pomocné metody a:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Ve výchozím nastavení ASP.NET Core podporuje `application/json` , `text/json` a `text/plain` typy médií. Nástroje, jako je například [Fiddler](https://www.telerik.com/fiddler) nebo [Poster](https://www.getpostman.com/tools) , mohou nastavit `Accept` hlavičku požadavku na zadání návratového formátu. Pokud `Accept` Hlavička obsahuje typ, který server podporuje, vrátí se tento typ. V další části se dozvíte, jak přidat další formátovací moduly.

Akce kontroleru mohou vracet POCOs (objekty CLR ve starém formátu). Když se vrátí POCO, modul runtime automaticky vytvoří `ObjectResult` objekt, který zabalí objekt. Klient získá formátovaný serializovaný objekt. Pokud je vrácen objekt `null` , `204 No Content` je vrácena odpověď.

Vrací se typ objektu:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

V předchozím kódu vrátí požadavek na platný alias autora `200 OK` odpověď s daty autora. Požadavek na neplatný alias vrací `204 No Content` odpověď.

### <a name="the-accept-header"></a>Hlavička Accept

Po zobrazení hlavičky v požadavku se *domlouvání* obsahu provede `Accept` . Když požadavek obsahuje hlavičku Accept, ASP.NET Core:

* Vytvoří výčet typů médií v hlavičce Accept v upřednostňovaném pořadí.
* Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z určených formátů.

Pokud se nenajde žádný formátovací modul, který může splnit požadavek klienta, ASP.NET Core:

* Vrátí `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> , pokud byla nastavena, nebo-
* Pokusí se najít první formátovací modul, který může vytvořit odpověď.

Pokud není pro požadovaný formát nakonfigurován žádný formátovací modul, je použit první formátovací modul, který může objekt formátovat. Pokud `Accept` se v požadavku nezobrazí žádná hlavička:

* První formátovací modul, který může zpracovat objekt, je použit k serializaci odpovědi.
* Nedochází k žádnému vyjednávání. Server určí, který formát se má vrátit.

Pokud hlavička Accept obsahuje `*/*` , záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` není nastavené na hodnotu true <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

### <a name="browsers-and-content-negotiation"></a>Prohlížeče a vyjednávání obsahu

Na rozdíl od typických klientů rozhraní API webové prohlížeče poskytují `Accept` hlavičky. Webový prohlížeč určí mnoho formátů, včetně zástupných znaků. Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče:

* `Accept`Záhlaví je ignorováno.
* Pokud není nakonfigurováno jinak, vrátí se obsah ve formátu JSON.

Díky tomu je v prohlížečích k dispozici jednotnější prostředí pro využívání rozhraní API.

Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hlavičky přijetí prohlížeče, nastavte <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true` :

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

Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Předchozí kód serializace výsledků pomocí `XmlSerializer` .

Při použití předchozího kódu vrátí metody kontroleru odpovídající formát na základě `Accept` hlavičky požadavku.

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurace System.Text.Jsformátovacích modulech na bázi

Funkce pro formátovací moduly založené na službě se `System.Text.Json` dají konfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` .

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult` . Například:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Přidání podpory formátu JSON založeného na Newtonsoft.Js

Před ASP.NET Core 3,0 byly použity výchozí formátovací moduly JSON implementované pomocí `Newtonsoft.Json` balíčku. V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json` . Podpora `Newtonsoft.Json` formátování a funkcí založených na základech je k dispozici po instalaci [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) balíčku NuGet a jeho konfiguraci v nástroji `Startup.ConfigureServices` .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Některé funkce nemusí fungovat správně s `System.Text.Json` formátovacími moduly a vyžadují odkaz na `Newtonsoft.Json` formátování na základě. I nadále používat formátovací moduly založené na tom, `Newtonsoft.Json` Pokud aplikace:

* Používá `Newtonsoft.Json` atributy. Příkladem je `[JsonProperty]` nebo `[JsonIgnore]`.
* Přizpůsobuje nastavení serializace.
* Spoléhá na funkce, které `Newtonsoft.Json` poskytuje.
* Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` . Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` akceptuje instance `JsonSerializerSettings` , která je specifická pro `Newtonsoft.Json` .
* Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .

Funkce pro formátovací moduly založené na službě se `Newtonsoft.Json` dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult` . Například:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Přidat podporu formátu XML

Formátování XML vyžaduje [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) balíček NuGet.

Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Předchozí kód serializace výsledků pomocí `XmlSerializer` .

Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě `Accept` hlavičky požadavku.

::: moniker-end

### <a name="specify-a-format"></a>Zadat formát

Chcete-li omezit formáty odpovědi, použijte [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr. Podobně jako u většiny [filtrů](xref:mvc/controllers/filters) `[Produces]` lze použít akci, řadič nebo globální rozsah:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Předchozí [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr:

* Vynutí, aby všechny akce v rámci kontroleru vracely odpovědi ve formátu JSON.
* Pokud jsou nakonfigurovány jiné formátovací moduly a klient určí jiný formát, bude vrácen kód JSON.

Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formátování speciálních případů

Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů. Ve výchozím nastavení `string` jsou návratové typy formátovány jako *textové/jednoduché* (*text/HTML* , pokud jsou požadovány prostřednictvím `Accept` hlavičky). Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> . Formátovací moduly jsou v metodě odebrány `ConfigureServices` . Akce, které mají vrácen návratový typ objektu modelu `204 No Content` při návratu `null` . Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> . Následující kód odstraní `StringOutputFormatter` a `HttpNoContentOutputFormatter` .

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Bez rozhraní `StringOutputFormatter` , předdefinované formáty formátování JSON `string` vrátí typy. Pokud je vestavěný formátovací modul JSON odebraný a je k dispozici formátovací modul XML, vrátí typy formátování formátovací kód XML `string` . V opačném případě `string` návratové typy vrátí `406 Not Acceptable` .

Bez `HttpNoContentOutputFormatter` objektů jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu. Například:

* Formátovací modul JSON vrátí odpověď s tělem `null` .
* Formátovací modul XML vrátí prázdný element XML s `xsi:nil="true"` nastaveným atributem.

## <a name="response-format-url-mappings"></a>Mapování adres URL formátu odpovědi

Klienti mohou požádat o konkrétní formát v rámci adresy URL, například:

* V řetězci dotazu nebo v části cesty.
* Pomocí přípony souboru specifické pro formát, jako je například. XML nebo. JSON.

Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API. Například:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru. [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Atribut kontroluje existenci hodnoty formátu v `RouteData` a při vytvoření odpovědi mapuje formát odpovědi na příslušný formátovací modul.

|           Trasa        |             Modul              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Výchozí výstupní formátovací modul    |
| `/api/products/5.json` | Formátovací modul JSON (Pokud je nakonfigurovaný) |
| `/api/products/5.xml`  | Formátovací modul XML (Pokud je nakonfigurovaný)  |
