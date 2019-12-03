---
title: Globalizace a lokalizace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core poskytuje služby a middleware pro lokalizaci obsahu do různých jazyků a kultur.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: 391786498dc7088f3a06915128f23b2c0e734656
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681107"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalizace a lokalizace v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya

Vytvoření vícejazyčného webu pomocí ASP.NET Core umožní vašemu webu oslovit širší cílovou skupinu. ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.

Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization). Globalizace je proces návrhu aplikací, které podporují různé jazykové verze. Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.

Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí. Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.

Lokalizace aplikace zahrnuje následující:

1. Nastavit lokalizaci obsahu aplikace

2. Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.

3. Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Nastavit lokalizaci obsahu aplikace

Zavedeno v ASP.NET Core, `IStringLocalizer` a `IStringLocalizer<T>` byly navrženy pro zvýšení produktivity při vývoji lokalizovaných aplikací. `IStringLocalizer` používá [správce](/dotnet/api/system.resources.resourcemanager) prostředků a [ResourceReader](/dotnet/api/system.resources.resourcereader) k poskytování prostředků specifických pro jazykovou verzi v době běhu. Jednoduché rozhraní má indexer a `IEnumerable` pro vracení lokalizovaných řetězců. `IStringLocalizer` nevyžaduje uložení výchozích řetězců jazyka do souboru prostředků. Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji. Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

Ve výše uvedeném kódu `IStringLocalizer<T>` implementace z [Injektáže závislosti](dependency-injection.md). Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title". Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace. Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků. Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka. Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace. Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.

Použijte implementaci `IHtmlLocalizer<T>` pro prostředky, které obsahují kód HTML. `IHtmlLocalizer` HTML kóduje argumenty, které jsou formátovány v řetězci prostředku, ale nekóduje kód HTML samotný řetězec prostředku. V ukázce zvýrazněné níže je pouze hodnota parametru `name` kódovaný v jazyce HTML.

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.

Na nejnižší úrovni můžete získat `IStringLocalizerFactory` pro [vkládání závislostí](dependency-injection.md):

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Výše uvedený kód ukazuje každou ze dvou metod Create Factory.

Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru. V ukázkové aplikaci se pro sdílené prostředky používá fiktivní třída s názvem `SharedResource`.

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

Někteří vývojáři používají třídu `Startup` k zahrnutí globálních nebo sdílených řetězců. V níže uvedené ukázce se používají `InfoController` a `SharedResource` lokalizace:

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Lokalizace zobrazení

Služba `IViewLocalizer` poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview). Třída `ViewLocalizer` implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení. Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer`:

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

Výchozí implementace `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení. Neexistuje možnost použít globální sdílený soubor prostředků. `ViewLocalizer` implementuje lokalizátora pomocí `IHtmlLocalizer`, takže Razor nekóduje lokalizovaný řetězec ve formátu HTML. Můžete parametrizovat řetězce prostředků a `IViewLocalizer` bude kódovat parametry HTML, ale ne řetězec prostředku. Vezměte v úvahu následující značky Razor:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Soubor prostředků francouzštiny může obsahovat následující:

| Key | Hodnota |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.

**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.

Chcete-li použít sdílený soubor prostředků v zobrazení, je nutné vložit `IHtmlLocalizer<T>`:

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalizace DataAnnotations

Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>`. Pomocí `ResourcesPath = "Resources"`možností mohou být chybové zprávy v `RegisterViewModel` uloženy v některé z následujících cest:

* *Resources/ViewModels. Account. RegisterViewModel. fr. resx*
* *Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

V ASP.NET Core MVC 1.1.0 a vyšší jsou lokalizovány neověřovací atributy. ASP.NET Core MVC 1,0 **nehledá lokalizované** řetězce pro atributy, které nejsou ověřovány.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Použití jednoho řetězce prostředku pro více tříd

Následující kód ukazuje, jak použít jeden řetězec prostředku pro atributy ověřování s více třídami:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy. S tímto přístupem budou dataanotace používat místo prostředků pro každou třídu jenom `SharedResource`.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures a SupportedUICultures

ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze, `SupportedCultures` a `SupportedUICultures`. Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je například datum, čas, číslo a formátování měny. `SupportedCultures` také určuje pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců. Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) . `SupportedUICultures` určuje, které překládá řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager). `ResourceManager` jednoduše vyhledává řetězce specifické pro jazykovou verzi, které jsou určeny `CurrentUICulture`. Každé vlákno v rozhraní .NET má `CurrentCulture` a objekty `CurrentUICulture`. ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi. Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, Únor 18, 2016", ale pokud je `CurrentCulture` nastavena na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de Febrero de 2016".

## <a name="resource-files"></a>Soubory prostředků

Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu. Přeložené řetězce pro jazyk, který není výchozí, jsou izolované soubory prostředků *. resx* . Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce. "ES" je kód jazyka pro španělštinu. Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:

1. V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.

    ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená. Druhá kontextová nabídka je otevřená pro přidání se zvýrazněným příkazem nová položka.](localization/_static/newi.png)

2. V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.

    ![Dialogové okno Přidat novou položku](localization/_static/res.png)

3. Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).

    ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

    Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .

    ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Pojmenovávání souborů prostředků

Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení. Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro `LocalizationWebsite.Web.Startup` třídy by se jmenovala *Startup. fr. resx*. Prostředek pro třídu `LocalizationWebsite.Web.Controllers.HomeController` by měl pojmenovat *Controllers. HomeController. fr. resx*. Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu. Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.

V ukázkovém projektu metoda `ConfigureServices` nastaví `ResourcesPath` na "Resources", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*. Případně můžete použít složky k uspořádání souborů prostředků. V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*. Pokud nepoužijete možnost `ResourcesPath`, soubor *. resx* by přešel do základního adresáře projektu. Soubor prostředků pro `HomeController` by měl pojmenovat *Controllers. HomeController. fr. resx*. Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.

| Název prostředku | Pojmenování teček nebo Path |
| ------------   | ------------- |
| Prostředky/řadiče. HomeController. fr. resx | Tečka  |
| Prostředky/řadiče/HomeController. fr. resx  | Cesta |
|    |     |

Soubory prostředků používající `@inject IViewLocalizer` v zobrazeních Razor následují podobný vzor. Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty. Soubory prostředků zobrazení Razor napodobují cestu k souboru přidruženého zobrazení. Za předpokladu, že `ResourcesPath` nastavíme na "prostředky", francouzský soubor prostředků přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:

* Prostředky/zobrazení/domů/o. fr. resx

* Prostředky/zobrazení. domů. about. fr. resx

Pokud nepoužijete možnost `ResourcesPath`, soubor *. resx* pro zobrazení by byl umístěn ve stejné složce jako zobrazení.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení. 

> [!WARNING]
> K tomu může dojít, když název projektu není platným identifikátorem .NET. Instance `my-project-name.csproj` například použije kořenový obor názvů `my_project_name` a název sestavení `my-project-name` což vede k této chybě. 

Pokud se kořenový obor názvů sestavení liší od názvu sestavení:

* Lokalizace ve výchozím nastavení nefunguje.
* Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení. `RootNamespace` je hodnota doby sestavení, která není k dispozici pro spuštěný proces. 

Pokud se `RootNamespace` liší od `AssemblyName`, zahrňte do *AssemblyInfo.cs* následující položky (s hodnotami parametrů nahrazenými skutečnými hodnotami):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Předchozí kód umožňuje úspěšné vyřešení souborů RESX.

## <a name="culture-fallback-behavior"></a>Chování záložního kultivačního prostředí

Při hledání prostředku se lokalizace zapojit do "záložní kultury". Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze. Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi. To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO. Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX. Má nadřazenou "ES"&mdash;španělštinu nespecifickou pro žádnou zemi.

Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA". Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:

* *Welcome.fr-CA. resx*
* *Welcome. fr. resx*
* *Welcome. resx* (Pokud `NeutralResourcesLanguage` je "fr-CA")

Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce. Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi. Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.

### <a name="generate-resource-files-with-visual-studio"></a>Generování souborů prostředků pomocí sady Visual Studio

Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada C# Visual Studio vytvoří třídu s vlastností pro každý řetězec. To obvykle není to, co chcete s ASP.NET Core. Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze). Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*). Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.

### <a name="add-other-cultures"></a>Přidat další jazykové verze

Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků. Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**). Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.

### <a name="configure-localization"></a>Konfigurace lokalizace

Lokalizace je nakonfigurována v metodě `Startup.ConfigureServices`:

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` přidá služby lokalizace do kontejneru služby. Výše uvedený kód také nastaví cestu prostředků na prostředky.

* `AddViewLocalization` přidává podporu lokalizovaných souborů zobrazení. V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení. Například "fr" v souboru *index. fr. cshtml* .

* `AddDataAnnotationsLocalization` přidává podporu pro lokalizované zprávy ověřování `DataAnnotations` prostřednictvím `IStringLocalizer` abstrakce.

### <a name="localization-middleware"></a>Middleware lokalizace

Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace. Middleware lokalizace je povolena v metodě `Startup.Configure`. Middleware lokalizace musí být nakonfigurované před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]

`UseRequestLocalization` inicializuje objekt `RequestLocalizationOptions`. Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` a použije se první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti. Výchozí zprostředkovatelé přicházejí z třídy `RequestLocalizationOptions`:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický. Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze. Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, je použita `DefaultRequestCulture`.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Některé aplikace budou používat řetězec dotazu k nastavení jazykové verze [a jazykové verze uživatelského rozhraní](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). Pro aplikace, které používají přístup k hlavičkám souborů cookie nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu. Ve výchozím nastavení je `QueryStringRequestCultureProvider` registrován jako první poskytovatel lokalizace v seznamu `RequestCultureProvider`. Parametry řetězce dotazu předáte `culture` a `ui-culture`. Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Pokud předáte pouze jeden ze dvou (`culture` nebo `ui-culture`), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali. Například nastavení pouze jazyková verze nastaví `Culture` i `UICulture`:

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze pomocí souboru cookie ASP.NET Core jazykové verze. K vytvoření souboru cookie použijte metodu `MakeCookieValue`.

`CookieRequestCultureProvider` `DefaultCookieName` vrátí výchozí název souboru cookie, který se používá ke sledování preferovaných informací o jazykové verzi uživatele. Výchozí název souboru cookie je `.AspNetCore.Culture`.

Formát souboru cookie je `c=%LANGCODE%|uic=%LANGCODE%`, kde `c` je `Culture` a `uic` `UICulture`například:

    c=en-UK|uic=en-US

Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.

### <a name="the-accept-language-http-header"></a>Hlavička protokolu HTTP pro přijetí – jazyk

[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele. Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému. Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.

### <a name="set-the-accept-language-http-header-in-ie"></a>Nastavení hlavičky protokolu HTTP Accept-Language v IE

1. Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.

2. Klepněte na **jazyky**.

    ![Možnosti Internetu](localization/_static/lang.png)

3. Klepněte na **nastavit jazykové předvolby**.

4. Klepněte na **Přidat jazyk**.

5. Přidejte jazyk.

6. Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.

::: moniker range=">= aspnetcore-3.1"
### <a name="the-content-language-http-header"></a>Hlavička protokolu HTTP v jazykovém obsahu

Hlavička entity [jazyka obsahu](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :

 - Slouží k popisu jazyků, které jsou určené pro cílovou skupinu.
 - Umožňuje uživateli rozlišovat podle vlastního preferovaného jazyka uživatelů.

Záhlaví entit se používají v požadavcích HTTP i v odpovědích.

Hlavičku `Content-Language` lze přidat nastavením vlastnosti `ApplyCurrentCultureToResponseHeaders`.

Přidání hlavičky `Content-Language`:

 - Umožňuje RequestLocalizationMiddleware nastavit hlavičku `Content-Language` pomocí `CurrentUICulture`.
 - Eliminuje nutnost nastavit hlavičku odpovědi `Content-Language` explicitně.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a>Použití vlastního zprostředkovatele

Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází. Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele. Následující kód ukazuje, jak přidat vlastního zprostředkovatele:

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

Pomocí `RequestLocalizationOptions` můžete přidat nebo odebrat poskytovatele lokalizace.

### <a name="set-the-culture-programmatically"></a>Programové nastavení kultury

Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture`. Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do části `footer` souboru rozložení, takže bude k dispozici pro všechna zobrazení:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Metoda `SetLanguage` nastaví soubor cookie jazykové verze.

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt. Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do sady Razor částečně prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .

## <a name="globalization-and-localization-terms"></a>Výrazy globalizace a lokalizace

Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí. I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly. Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.

[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.

Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>`, kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury. Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti. Viz [název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).

Mezinárodní využití se často zkracuje na "I18N". Zkratka používá první a poslední písmena a počet písmen mezi nimi, takže 18 představuje počet písmen mezi první I a poslední znak "N". Totéž platí pro globalizaci (G11N) a lokalizaci (L10N).

Uvedenými

* Globalizace (G11N): proces vytvoření aplikace, která podporuje různé jazyky a oblasti.
* Lokalizace (L10N): proces přizpůsobení aplikace pro daný jazyk a oblast.
* Mezinárodní (I18N): popisuje globalizaci a lokalizaci.
* Jazyková verze: Jedná se o jazyk a volitelně i oblast.
* Neutrální jazyková verze: jazyková verze, která má zadaný jazyk, ale ne oblast. (například "en", "ES")
* Specifická jazyková verze: jazyková verze, která má zadaný jazyk a oblast. (například "en-US", "en-GB", "ES-CL")
* Nadřazená jazyková verze: neutrální jazyková verze, která obsahuje konkrétní jazykovou verzi. (například "en" je nadřazená jazyková verze "en-US" a "en-GB")
* Národní prostředí: národní prostředí je stejné jako jazyková verze.

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.
* [Globalizace a lokalizace aplikací .NET](/dotnet/standard/globalization-localization/index)
* [Prostředky v souborech. resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Sada nástrojů pro vícejazyčné aplikace od Microsoftu](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalizace & – obecné typy](http://hishambinateya.com/localization-and-generics)
