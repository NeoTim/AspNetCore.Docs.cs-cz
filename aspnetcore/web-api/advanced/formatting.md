---
title: Formátování dat odpovědi v rozhraní Web API ASP.NET Core
author: ardalis
description: Zjistěte, jak k formátování dat odpovědi v rozhraní Web API ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 05/29/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: b050011aa38743353fb2a7d133abcdca0b8c6d33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814819"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formátování dat odpovědi v rozhraní Web API ASP.NET Core

Podle [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC obsahuje integrovanou podporu pro formátování dat odpovědi pomocí pevné formáty nebo v reakci na požadavky klienta.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Výsledky specifické pro formát akcí

Některé typy výsledků akcí, jako jsou specifické pro konkrétní formát `JsonResult` a `ContentResult`. Akce může vrátit konkrétní výsledky, které jsou vždy formátována určitým způsobem. Například vrácení `JsonResult` vrátí data ve formátu JSON, bez ohledu na předvolby klienta. Podobně, vrácení `ContentResult` vrátí data ve formátu prostého textu řetězce (stejně jako jednoduše vrátit řetězec).

> [!NOTE]
> Akce nemusí vracet žádné konkrétní typ; MVC podporuje libovolný objekt návratovou hodnotu. Akce vrátí-li `IActionResult` provádění a kontroler dědí z `Controller`, je vývojářům k dispozici mnoho pomocné metody odpovídající na řadu možností. Výsledky z akcí, které vracejí objekty, které nejsou `IActionResult` typy se serializovat pomocí odpovídající `IOutputFormatter` implementace.

Vrácení dat v určitém formátu z kontroler, který dědí z `Controller` základní třídy, použijte metodu integrovaných pomocných `Json` vrátit JSON a `Content` pro prostý text. Vaše metoda akce by měla vrátit buď konkrétní výsledný typ (například `JsonResult`) nebo `IActionResult`.

Vrácení dat ve formátu JSON:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

Ukázková odpověď z tuto akci:

![Karta síť vývojářských nástrojů v Microsoft Edge zobrazující typ obsahu odpovědi je application/json](formatting/_static/json-response.png)

Všimněte si, že je typem obsahu odpovědi `application/json`, jak je znázorněno v seznamu síťových požadavků i v sekci hlavičky odpovědi. Všimněte si také seznam možností v prohlížeči (v tomto případě Microsoft Edge) v hlavičce Accept v části záhlaví požadavku. Současnou techniku ignoruje tuto hlavičku; obeying ho, jsou popsány níže.

Chcete-li vrátit data ve formátu prostého textu, použijte `ContentResult` a `Content` pomocné rutiny:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

Odpověď z tuto akci:

![Karta síť vývojářských nástrojů v Microsoft Edge zobrazující typ obsahu odpovědi je text/plain](formatting/_static/text-response.png)

Poznámka: v tomto případě `Content-Type` vrátil je `text/plain`. Také můžete dosáhnout toto stejné chování pomocí pouze typ odpovědi řetězec:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> U netriviální akcí s několika vrátí typy nebo možností (například jiné stavové kódy HTTP na základě výsledku operace prováděné), při odstraňování preferujte `IActionResult` jako návratový typ.

## <a name="content-negotiation"></a>Vyjednávání obsahu

Vyjednávání obsahu (*conneg* zkráceně) nastane, pokud klient Určuje [hlavičky Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Výchozí formát používaný čtečkou ASP.NET Core MVC je JSON. Vyjednávání obsahu je implementováno `ObjectResult`. Je také součástí stavový kód určité akce výsledky vracené z metod helper (které jsou všechny založené na `ObjectResult`). Můžete se taky vrátit typ modelu (třídy, které jste definovali jako typ přenosu dat) a rozhraní budou automaticky zabalte ji v `ObjectResult` za vás.

Používá následující metody akce `Ok` a `NotFound` pomocné metody:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

Odpověď ve formátu JSON se vrátí, pokud byl požadován jiného formátu a server může vrátit požadovanému formátu. Můžete použít nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) vytvořte žádost, která obsahuje hlavičku Accept a určete jiný formát. V takovém případě pokud má server *formátovací modul* , který může vytvořit odpověď požadovaný formát, výsledkem bude vrácen ve formátu upřednostňované klienta.

![Fiddler konzola znázorňující ručně vytvořili získat požadavek s hodnotou hlavičky Accept application/XML](formatting/_static/fiddler-composer.png)

Ve výše uvedeném snímku obrazovky se použil autora aplikace Fiddler k vytvoření žádosti, určení `Accept: application/xml`. Ve výchozím nastavení, ASP.NET Core MVC podporuje pouze JSON, takže i když je určený jiný formát, vrácený výsledek je stále ve formátu JSON. Uvidíte jak přidat další formátování v další části.

Akce kontroleru vrátit POCOs (Plain původní objekty CLR), v takovém případě automaticky vytvoří ASP.NET Core MVC `ObjectResult` za vás, která obaluje objekt. Klient získá formátovaný serializovaný objekt (formát JSON je výchozí, můžete nakonfigurovat, XML nebo jiných formátů). Pokud je objekt vrácen je `null`, pak se vrátíte rozhraní framework `204 No Content` odpovědi.

Vrátí typ objektu:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

Ve vzorku obdrží žádost o platné Autor alias odpověď 200 OK údaji autora. Žádost o neplatný alias přijetí 204 bez obsahu odpovědi. Snímky obrazovky zobrazující odpověď ve formátu XML a JSON jsou uvedeny níže.

### <a name="content-negotiation-process"></a>Zpracování vyjednávání obsahu

Obsahu *vyjednávání* pouze probíhá-li `Accept` hlavička objeví v požadavku. Pokud požadavek obsahuje hlavičku accept, rozhraní se zobrazí výčet typů médií v hlavičce accept v pořadí priority a pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z formátů zadaná v hlavičce accept. V případě, že není formátování nalezeno, která splňují požadavek klienta, rozhraní se pokusí najít první formátovací modul, který může vytvořit odpověď (Pokud pro vývojáře se nakonfigurovala možnost v `MvcOptions` vrátit 406 Nepřijatelný místo). Pokud požadavek určuje XML, ale formátovací modul XML není nakonfigurovaná, budou formátovací modul JSON použít. Obecně platí Pokud je nakonfigurovaná žádná formátovací modul, který může poskytnout požadovaný formát, pak první formátovací modul, která dokáže formátovat objektu se používá. Pokud není uveden žádný záhlaví, první formátovací modul, který dokáže zpracovat objekt, který má být vrácen se použije k serializaci odpovědi. V takovém případě není k dispozici žádné vyjednávání dochází – na serveru je určení jakém formátu bude používat.

> [!NOTE]
> Pokud hlavička Accept obsahuje `*/*`, záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` je nastavena na hodnotu true v `MvcOptions`.

### <a name="browsers-and-content-negotiation"></a>Prohlížeče a vyjednávání obsahu

Na rozdíl od typických klientů rozhraní API, webové prohlížeče jsou obvykle slouží k poskytování `Accept` hlavičky, které obsahují širokou škálu formátů, včetně zástupných znaků. Ve výchozím nastavení, když rozhraní zjistí, že žádost pochází z prohlížeče, se bude ignorovat `Accept` hlavičky a místo toho návratový obsah v aplikaci nakonfigurovaná výchozí formát (JSON není-li jinak nakonfigurované). To poskytuje konzistentní prostředí při použití různých prohlížečů k používání rozhraní API.

Pokud si přejete prohlížeče dodržet aplikace hlavičky accept, to můžete nakonfigurovat jako součást konfigurace MVC nastavením `RespectBrowserAcceptHeader` k `true` v `ConfigureServices` metoda *Startup.cs*.

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a>Konfigurace formátovacích modulů

Pokud vaše aplikace potřebuje pro podporu dalších formátech nad výchozí hodnotu JSON, můžete přidat balíčky NuGet a nakonfigurovat MVC pro jejich podporu. Existují samostatné formátovací moduly pro vstup a výstup. Vstupní formátovací moduly jsou používány [vazby modelu](xref:mvc/models/model-binding); formátování výstupu se používají k formátování odpovědi. Můžete taky nakonfigurovat [vlastní formátování](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurace na základě System.Text.Json formátovacích modulů

Funkce `System.Text.Json`– na základě formátování lze konfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcOptions.SerializerOptions`.

```csharp
services.AddMvc(options =>
{
    options.SerializerOptions.WriterSettings.Indented = true;
});
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Přidání podpory pro formát JSON založených na Newtonsoft.Json

Před ASP.NET Core 3.0 MVC nastavena na výchozí pomocí formátování JSON, které jsou implementované pomocí `Newtonsoft.Json` balíčku. V ASP.NET Core 3.0 nebo novější, jsou na základě výchozí formátování JSON `System.Text.Json`. Podpora pro `Newtonsoft.Json`– na základě formátování a funkce je k dispozici nainstalováním [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet balíček a jeho v konfiguraci `Startup.ConfigureServices`.

```csharp
services.AddMvc()
    .AddNewtonsoftJson();
```

Některé funkce nemusí fungovat s `System.Text.Json`– na základě formátovacích modulů a vyžadují odkaz na `Newtonsoft.Json`– na základě formátovací moduly pro vydanou verzi ASP.NET Core 3.0. Pokračovat v používání `Newtonsoft.Json`– na základě formátovacích modulů, pokud vaší aplikace ASP.NET Core 3.0 nebo novější:

* Používá `Newtonsoft.Json` atributy (například `[JsonProperty]` nebo `[JsonIgnore]`), umožňuje upravit nastavení serializace nebo spoléhá na funkce, které `Newtonsoft.Json` poskytuje.
* Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Před ASP.NET Core 3.0 `JsonResult.SerializerSettings` přijímá instanci `JsonSerializerSettings` , která je specifická pro `Newtonsoft.Json`.
* Generuje [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) dokumentaci.

::: moniker-end

### <a name="add-xml-format-support"></a>Přidání podpory pro formát XML

::: moniker range="<= aspnetcore-2.2"

Chcete-li přidat podporu v ASP.NET Core 2.2 nebo dřívější formátování data XML, nainstalovat [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) balíček NuGet.

::: moniker-end

Formátovací moduly XML implementované pomocí `System.Xml.Serialization.XmlSerializer` se dá nakonfigurovat pomocí volání <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> v `Startup.ConfigureServices`:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

Alternativně XML formátování, které jsou implementované pomocí `System.Runtime.Serialization.DataContractSerializer` se dá nakonfigurovat pomocí volání <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> v `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .AddXmlDataContractSerializerFormatters();
```

Po přidání podpory pro formát XML, by měl vrátit svoje metody kontroleru vhodný formát na základě daného požadavku `Accept` záhlaví jako Fiddler, tento příklad ukazuje:

![Fiddler konzoly: Nezpracovaná karta pro požadavek zobrazuje, že je hodnota hlavičky Accept application/xml. Na kartě nezpracovaná pro odpověď zobrazena hodnota hlavičky Content-Type application/XML.](formatting/_static/xml-response.png)

Se zobrazí v kartě kontroly, která byla vytvořena žádost o získání nezpracovaných se `Accept: application/xml` hlavička set. Zobrazí se podokno odpovědi `Content-Type: application/xml` záhlaví a `Author` objekt má byl serializován do formátu XML.

Použijte kartu Composer upravit požadavku k určení `application/json` v `Accept` záhlaví. Provedení požadavku a odpovědi naformátovaný jako JSON:

![Fiddler konzoly: Nezpracovaná karta pro požadavek zobrazuje, že je hodnota hlavičky Accept application/json. Na kartě nezpracovaná pro odpověď zobrazena hodnota záhlaví Content-Type application/json.](formatting/_static/json-response-fiddler.png)

Na tomto snímku obrazovky vidíte požadavek nastaví hlavičku z `Accept: application/json` a odpovědi určuje stejný jako jeho `Content-Type`. `Author` Objektu se zobrazí v těle odpovědi ve formátu JSON.

### <a name="forcing-a-particular-format"></a>Vynucení konkrétní formát

Pokud chcete omezit formáty odpovědi pro konkrétní akci je možné, můžete použít `[Produces]` filtru. `[Produces]` Filtr určuje formát odpovědi pro konkrétní akci (nebo řadič). Jako většina [filtry](xref:mvc/controllers/filters), to je možné použít na akci, kontroler nebo globální obor.

```csharp
[Produces("application/json")]
public class AuthorsController
```

`[Produces]` Filtr vynutí všechny akce v rámci `AuthorsController` k vrácení odpovědí ve formátu JSON i v případě jiných formátovací moduly byly nakonfigurované pro aplikaci a poskytnutý klientem `Accept` záhlaví žádosti o jiné, k dispozici formát. Zobrazit [filtry](xref:mvc/controllers/filters) Další informace, včetně postupu jak používat filtry globálně.

### <a name="special-case-formatters"></a>Zvláštní případ formátovacích modulů

Některé speciální případy jsou implementovány pomocí předdefinované formátování. Ve výchozím nastavení `string` návratové typy naformátovaný jako *text/plain* (*text/html* žádost prostřednictvím `Accept` záhlaví). Toto chování lze odebrat odstraněním `TextOutputFormatter`. Odebrat formátování v `Configure` metoda *Startup.cs* (viz dole). Akce, které mají objekt modelu vrátit typ vrátí 204 neodpovídá obsahu při vrácení `null`. Toto chování lze odebrat odstraněním `HttpNoContentOutputFormatter`. Následující kód, odebere `TextOutputFormatter` a `HttpNoContentOutputFormatter`.

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

Bez `TextOutputFormatter`, `string` vrátí typy vrátit 406 Nepřijatelný, například. Všimněte si, že pokud existuje formátovací modul XML ji naformátuje `string` návratové typy, pokud `TextOutputFormatter` se odebere.

Bez `HttpNoContentOutputFormatter`, objektů null jsou formátovány pomocí nakonfigurovaného formátovacího modulu. Například formátování JSON jednoduše vrátit odpověď se na znalostech `null`, zatímco formátovací modul XML vrátí prázdný element XML s atributem `xsi:nil="true"` nastavit.

## <a name="response-format-url-mappings"></a>Mapování formát adresy URL odpovědi

Klienti mohou požadovat konkrétní formát jako část adresy URL, například v řetězci dotazu nebo části cesty nebo pomocí rozšíření specifické pro formát souborů například XML nebo .json. Mapování z cestu požadavku by měl určený v trase, kterou používá rozhraní API. Příklad:

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

Tato trasa by umožnilo požadovaný formát zadaný jako volitelný soubor rozšíření. `[FormatFilter]` Atribut zkontroluje existenci hodnoty formátu `RouteData` a bude mapovat formát odpovědi na odpovídající formátovací modul, když je vytvořen odpověď.

|           trasy            |             Formátovací modul              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    Výchozí formátování výstupu    |
| `/products/GetById/5.json` | Formátování JSON (je-li konfigurováno) |
| `/products/GetById/5.xml`  | Formátovací modul XML (Pokud je nakonfigurovaná)  |
