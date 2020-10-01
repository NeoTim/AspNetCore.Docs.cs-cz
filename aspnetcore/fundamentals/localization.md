---
title: Globalizace a lokalizace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core poskytuje služby a middleware pro lokalizaci obsahu do různých jazyků a kultur.
ms.author: riande
ms.date: 11/30/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: fcf69bdaaed5cf0283ae27440c28061857d2cbcb
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606772"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalizace a lokalizace v ASP.NET Core

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya

Vícejazyčný web umožňuje webu oslovit širší cílovou skupinu. ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.

Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization). Globalizace je proces návrhu aplikací, které podporují různé jazykové verze. Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.

Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí. Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.

Lokalizace aplikace zahrnuje následující:

1. Nastavit lokalizaci obsahu aplikace
1. Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.
1. Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Nastavit lokalizaci obsahu aplikace

<xref:Microsoft.Extensions.Localization.IStringLocalizer> a <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> bylo navrženo pro zlepšení produktivity při vývoji lokalizovaných aplikací. `IStringLocalizer` používá <xref:System.Resources.ResourceManager> a <xref:System.Resources.ResourceReader> k poskytnutí prostředků specifických pro jazykovou verzi v době běhu. Rozhraní má indexer a `IEnumerable` pro vracení lokalizovaných řetězců. `IStringLocalizer` nevyžaduje uložení výchozích řetězců jazyka v souboru prostředků. Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji. Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

V předchozím kódu `IStringLocalizer<T>` implementace pochází z [Injektáže závislosti](dependency-injection.md). Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title". Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace. Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků. Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka. Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace. Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.

Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód HTML. `IHtmlLocalizer` HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku. V ukázce zvýrazněné níže je pouze hodnota `name` parametru kódována HTML.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Obecně pouze lokalizovat text, ne HTML.

Na nejnižší úrovni můžete obdržet zástupné `IStringLocalizerFactory` [vkládání závislostí](dependency-injection.md):

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Výše uvedený kód ukazuje každou ze dvou metod Create Factory.

Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru. V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

Někteří vývojáři používají `Startup` třídu pro zahrnutí globálních nebo sdílených řetězců. V níže uvedené ukázce se `InfoController` `SharedResource` používají a Localize:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Lokalizace zobrazení

`IViewLocalizer`Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview). `ViewLocalizer`Třída implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení. Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer` :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

Výchozí implementace nástroje `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení. Neexistuje možnost použít globální sdílený soubor prostředků. `ViewLocalizer` implementuje lokalizátora pomocí `IHtmlLocalizer` , takže Razor nekóduje kód HTML jako lokalizovaný řetězec. Můžete parametrizovat řetězce prostředků a `IViewLocalizer` zakódovat parametry HTML, ale ne řetězec prostředku. Vezměte v úvahu následující Razor značky:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Soubor prostředků francouzštiny může obsahovat následující:

| Klíč | Hodnota |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.

> [!NOTE]
> Obecně pouze lokalizovat text, ne HTML.

Chcete-li použít sdílený soubor prostředků v zobrazení, vložení `IHtmlLocalizer<T>` :

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalizace DataAnnotations

Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>` . Pomocí možnosti `ResourcesPath = "Resources"` mohou být chybové zprávy v nástroji `RegisterViewModel` uloženy v jedné z následujících cest:

* *Resources/ViewModels. Account. RegisterViewModel. fr. resx*
* *Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

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

V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy. S tímto přístupem budou dataanotace používat pouze `SharedResource` místo prostředků pro každou třídu.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures a SupportedUICultures

ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze `SupportedCultures` a `SupportedUICultures` . Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je datum, čas, číslo a formátování měny. `SupportedCultures` Určuje také pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců. Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) . `SupportedUICultures`Určuje, které přeložené řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager). `ResourceManager`Jednoduše vyhledá řetězce specifické pro jazykovou verzi, které určuje `CurrentUICulture` . Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty. ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi. Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, 18. února 2016", ale pokud `CurrentCulture` je nastavená na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Soubory prostředků

Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu. Přeložené řetězce pro jazyk, který není výchozí, jsou izolované v souborech prostředků *. resx* . Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce. "ES" je kód jazyka pro španělštinu. Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:

1. V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.

   ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená. Druhá kontextová nabídka je otevřená pro přidání se zvýrazněným příkazem nová položka.](localization/_static/newi.png)

1. V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.

   ![Dialogové okno Přidat novou položku](localization/_static/res.png)

1. Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).

   ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

   Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .

   ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Pojmenovávání souborů prostředků

Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení. Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by měla být pojmenována *Startup. fr. resx*. Prostředek pro třídu by měl `LocalizationWebsite.Web.Controllers.HomeController` pojmenovat *Controllers. HomeController. fr. resx*. Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu. Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.

V ukázkovém projektu `ConfigureServices` Metoda nastaví `ResourcesPath` na "prostředky", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*. Případně můžete použít složky k uspořádání souborů prostředků. V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*. Pokud tuto možnost nepoužijete `ResourcesPath` , soubor *. resx* by přešel do základního adresáře projektu. Soubor prostředků pro `HomeController` by měl mít název *Controllers. HomeController. fr. resx*. Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.

| Název prostředku | Pojmenování teček nebo Path |
| ------------   | ------------- |
| Prostředky/řadiče. HomeController. fr. resx | Tečka  |
| Prostředky/řadiče/HomeController. fr. resx  | Cesta |

Soubory prostředků používané `@inject IViewLocalizer` v Razor zobrazeních následují podobně jako vzor. Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty. Razor zobrazení soubory prostředků napodobá cestě k jejich přidruženému souboru zobrazení. Za předpokladu, že nastavíme `ResourcesPath` "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:

* Prostředky/zobrazení/domů/o. fr. resx

* Prostředky/zobrazení. domů. about. fr. resx

Pokud tuto možnost nepoužijete `ResourcesPath` , bude soubor *. resx* pro zobrazení umístěn ve stejné složce jako zobrazení.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení. 

> [!WARNING]
> K tomu může dojít, když název projektu není platným identifikátorem .NET. V případě instance `my-project-name.csproj` bude použit kořenový obor názvů `my_project_name` a název sestavení, `my-project-name` který vede k této chybě. 

Pokud se kořenový obor názvů sestavení liší od názvu sestavení:

* Lokalizace ve výchozím nastavení nefunguje.
* Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení. `RootNamespace` je hodnota doby sestavení, která není k dispozici pro spuštěný proces. 

Pokud se `RootNamespace` liší od z `AssemblyName` , zahrňte následující v *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Předchozí kód umožňuje úspěšné vyřešení souborů RESX.

## <a name="culture-fallback-behavior"></a>Chování záložního kultivačního prostředí

Při hledání prostředku se lokalizace zapojit do "záložní kultury". Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze. Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi. To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO. Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX. Má nadřízenou španělštinu ES, která &mdash; není specifická pro žádnou zemi.

Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA". Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:

* *Welcome.fr-CA. resx*
* *Welcome. fr. resx*
* *Welcome. resx* (Pokud `NeutralResourcesLanguage` je to "fr-CA")

Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce. Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi. Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.

### <a name="generate-resource-files-with-visual-studio"></a>Generování souborů prostředků pomocí sady Visual Studio

Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada Visual Studio vytvoří třídu jazyka C# s vlastností pro každý řetězec. To obvykle není to, co chcete s ASP.NET Core. Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze). Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*). Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.

### <a name="add-other-cultures"></a>Přidat další jazykové verze

Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků. Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**). Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.

### <a name="configure-localization"></a>Konfigurace lokalizace

Lokalizace je nakonfigurovaná v `Startup.ConfigureServices` metodě:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Přidá služby lokalizace do kontejneru služby. Výše uvedený kód také nastaví cestu prostředků na prostředky.

* `AddViewLocalization` Přidá podporu pro lokalizované soubory zobrazení. V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení. Například "fr" v souboru *index. fr. cshtml* .

* `AddDataAnnotationsLocalization` přidává podporu pro lokalizované `DataAnnotations` ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.

### <a name="localization-middleware"></a>Middleware lokalizace

Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace. Middleware lokalizace je povolená v `Startup.Configure` metodě. Middleware lokalizace musí být konfigurovány před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()` ).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` Inicializuje `RequestLocalizationOptions` objekt. Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` výčtu a první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti. Výchozí zprostředkovatelé přicházejí z `RequestLocalizationOptions` třídy:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický. Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze. Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, `DefaultRequestCulture` je použita.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Některé aplikace budou k nastavení použít řetězec dotazu <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> . Pro aplikace, které používají cookie přístup k hlavičkám nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu. Ve výchozím nastavení `QueryStringRequestCultureProvider` je jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu zaregistrován. Předáte parametry řetězce dotazu `culture` a `ui-culture` . Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Pokud předáte pouze jeden z těchto dvou ( `culture` nebo `ui-culture` ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali. Například nastavení pouze jazyková verze nastaví jak `Culture` a `UICulture` :

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze s ASP.NET Coreovou kulturou cookie . Použijte `MakeCookieValue` metodu k vytvoření cookie .

`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název, který cookie slouží ke sledování informací o preferované jazykové verzi uživatele. Výchozí cookie název je `.AspNetCore.Culture` .

cookieFormát je `c=%LANGCODE%|uic=%LANGCODE%` , kde `c` je `Culture` a je například `uic` `UICulture` :

```
c=en-UK|uic=en-US
```

Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.

### <a name="the-accept-language-http-header"></a>Hlavička protokolu HTTP pro přijetí – jazyk

[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele. Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému. Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.

### <a name="set-the-accept-language-http-header-in-ie"></a>Nastavení hlavičky protokolu HTTP Accept-Language v IE

1. Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.

1. Klepněte na **jazyky**.

   ![Možnosti Internetu](localization/_static/lang.png)

1. Klepněte na **nastavit jazykové předvolby**.

1. Klepněte na **Přidat jazyk**.

1. Přidejte jazyk.

1. Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.

### <a name="use-a-custom-provider"></a>Použití vlastního zprostředkovatele

Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází. Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele. Následující kód ukazuje, jak přidat vlastního zprostředkovatele:

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

Použijte `RequestLocalizationOptions` k přidání nebo odebrání poskytovatelů lokalizace.

### <a name="set-the-culture-programmatically"></a>Programové nastavení kultury

Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture` . Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do `footer` oddílu souboru rozložení, takže bude k dispozici pro všechna zobrazení:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage`Metoda nastaví jazykovou verzi cookie .

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt. Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do Razor částečného toku prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .

## <a name="model-binding-route-data-and-query-strings"></a>Vazba modelu data směrování a řetězce dotazů

Podívejte [se na téma chování globalizace modelu vazby dat trasy a řetězce dotazu](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Výrazy globalizace a lokalizace

Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí. I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly. Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.

[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.

Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>` , kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury. Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti. Viz třída <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

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

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Další zdroje informací

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.
* [Globalizace a lokalizace aplikací .NET](/dotnet/standard/globalization-localization/index)
* [Prostředky v souborech. resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Sada nástrojů pro vícejazyčné aplikace od Microsoftu](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalizace & – obecné typy](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya

Vícejazyčný web umožňuje webu oslovit širší cílovou skupinu. ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.

Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization). Globalizace je proces návrhu aplikací, které podporují různé jazykové verze. Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.

Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí. Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.

Lokalizace aplikace zahrnuje následující:

1. Nastavit lokalizaci obsahu aplikace
1. Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.
1. Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Nastavit lokalizaci obsahu aplikace

<xref:Microsoft.Extensions.Localization.IStringLocalizer> a <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> bylo navrženo pro zlepšení produktivity při vývoji lokalizovaných aplikací. `IStringLocalizer` používá <xref:System.Resources.ResourceManager> a <xref:System.Resources.ResourceReader> k poskytnutí prostředků specifických pro jazykovou verzi v době běhu. Rozhraní má indexer a `IEnumerable` pro vracení lokalizovaných řetězců. `IStringLocalizer` nevyžaduje uložení výchozích řetězců jazyka v souboru prostředků. Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji. Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

V předchozím kódu `IStringLocalizer<T>` implementace pochází z [Injektáže závislosti](dependency-injection.md). Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title". Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace. Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků. Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka. Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace. Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.

Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód HTML. `IHtmlLocalizer` HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku. V ukázce zvýrazněné níže je pouze hodnota `name` parametru kódována HTML.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Obecně pouze lokalizovat text, ne HTML.

Na nejnižší úrovni můžete obdržet zástupné `IStringLocalizerFactory` [vkládání závislostí](dependency-injection.md):

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Výše uvedený kód ukazuje každou ze dvou metod Create Factory.

Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru. V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

Někteří vývojáři používají `Startup` třídu pro zahrnutí globálních nebo sdílených řetězců. V níže uvedené ukázce se `InfoController` `SharedResource` používají a Localize:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Lokalizace zobrazení

`IViewLocalizer`Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview). `ViewLocalizer`Třída implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení. Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer` :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

Výchozí implementace nástroje `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení. Neexistuje možnost použít globální sdílený soubor prostředků. `ViewLocalizer` implementuje lokalizátora pomocí `IHtmlLocalizer` , takže Razor nekóduje kód HTML jako lokalizovaný řetězec. Můžete parametrizovat řetězce prostředků a `IViewLocalizer` zakódovat parametry HTML, ale ne řetězec prostředku. Vezměte v úvahu následující Razor značky:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Soubor prostředků francouzštiny může obsahovat následující:

| Klíč | Hodnota |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.

> [!NOTE]
> Obecně pouze lokalizovat text, ne HTML.

Chcete-li použít sdílený soubor prostředků v zobrazení, vložení `IHtmlLocalizer<T>` :

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalizace DataAnnotations

Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>` . Pomocí možnosti `ResourcesPath = "Resources"` mohou být chybové zprávy v nástroji `RegisterViewModel` uloženy v jedné z následujících cest:

* *Resources/ViewModels. Account. RegisterViewModel. fr. resx*
* *Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

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

V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy. S tímto přístupem budou dataanotace používat pouze `SharedResource` místo prostředků pro každou třídu.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures a SupportedUICultures

ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze `SupportedCultures` a `SupportedUICultures` . Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je datum, čas, číslo a formátování měny. `SupportedCultures` Určuje také pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců. Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) . `SupportedUICultures`Určuje, které přeložené řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager). `ResourceManager`Jednoduše vyhledá řetězce specifické pro jazykovou verzi, které určuje `CurrentUICulture` . Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty. ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi. Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, 18. února 2016", ale pokud `CurrentCulture` je nastavená na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Soubory prostředků

Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu. Přeložené řetězce pro jazyk, který není výchozí, jsou izolované v souborech prostředků *. resx* . Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce. "ES" je kód jazyka pro španělštinu. Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:

1. V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.

   ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená. Druhá kontextová nabídka je otevřená pro přidání se zvýrazněným příkazem nová položka.](localization/_static/newi.png)

1. V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.

   ![Dialogové okno Přidat novou položku](localization/_static/res.png)

1. Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).

   ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

   Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .

   ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Pojmenovávání souborů prostředků

Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení. Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by měla být pojmenována *Startup. fr. resx*. Prostředek pro třídu by měl `LocalizationWebsite.Web.Controllers.HomeController` pojmenovat *Controllers. HomeController. fr. resx*. Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu. Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.

V ukázkovém projektu `ConfigureServices` Metoda nastaví `ResourcesPath` na "prostředky", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*. Případně můžete použít složky k uspořádání souborů prostředků. V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*. Pokud tuto možnost nepoužijete `ResourcesPath` , soubor *. resx* by přešel do základního adresáře projektu. Soubor prostředků pro `HomeController` by měl mít název *Controllers. HomeController. fr. resx*. Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.

| Název prostředku | Pojmenování teček nebo Path |
| ------------   | ------------- |
| Prostředky/řadiče. HomeController. fr. resx | Tečka  |
| Prostředky/řadiče/HomeController. fr. resx  | Cesta |

Soubory prostředků používané `@inject IViewLocalizer` v Razor zobrazeních následují podobně jako vzor. Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty. Razor zobrazení soubory prostředků napodobá cestě k jejich přidruženému souboru zobrazení. Za předpokladu, že nastavíme `ResourcesPath` "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:

* Prostředky/zobrazení/domů/o. fr. resx

* Prostředky/zobrazení. domů. about. fr. resx

Pokud tuto možnost nepoužijete `ResourcesPath` , bude soubor *. resx* pro zobrazení umístěn ve stejné složce jako zobrazení.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení. 

> [!WARNING]
> K tomu může dojít, když název projektu není platným identifikátorem .NET. V případě instance `my-project-name.csproj` bude použit kořenový obor názvů `my_project_name` a název sestavení, `my-project-name` který vede k této chybě. 

Pokud se kořenový obor názvů sestavení liší od názvu sestavení:

* Lokalizace ve výchozím nastavení nefunguje.
* Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení. `RootNamespace` je hodnota doby sestavení, která není k dispozici pro spuštěný proces. 

Pokud se `RootNamespace` liší od z `AssemblyName` , zahrňte následující v *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Předchozí kód umožňuje úspěšné vyřešení souborů RESX.

## <a name="culture-fallback-behavior"></a>Chování záložního kultivačního prostředí

Při hledání prostředku se lokalizace zapojit do "záložní kultury". Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze. Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi. To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO. Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX. Má nadřízenou španělštinu ES, která &mdash; není specifická pro žádnou zemi.

Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA". Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:

* *Welcome.fr-CA. resx*
* *Welcome. fr. resx*
* *Welcome. resx* (Pokud `NeutralResourcesLanguage` je to "fr-CA")

Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce. Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi. Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.

### <a name="generate-resource-files-with-visual-studio"></a>Generování souborů prostředků pomocí sady Visual Studio

Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada Visual Studio vytvoří třídu jazyka C# s vlastností pro každý řetězec. To obvykle není to, co chcete s ASP.NET Core. Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze). Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*). Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.

### <a name="add-other-cultures"></a>Přidat další jazykové verze

Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků. Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**). Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.

### <a name="configure-localization"></a>Konfigurace lokalizace

Lokalizace je nakonfigurovaná v `Startup.ConfigureServices` metodě:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Přidá služby lokalizace do kontejneru služby. Výše uvedený kód také nastaví cestu prostředků na prostředky.

* `AddViewLocalization` Přidá podporu pro lokalizované soubory zobrazení. V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení. Například "fr" v souboru *index. fr. cshtml* .

* `AddDataAnnotationsLocalization` přidává podporu pro lokalizované `DataAnnotations` ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.

### <a name="localization-middleware"></a>Middleware lokalizace

Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace. Middleware lokalizace je povolená v `Startup.Configure` metodě. Middleware lokalizace musí být konfigurovány před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()` ).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` Inicializuje `RequestLocalizationOptions` objekt. Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` výčtu a první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti. Výchozí zprostředkovatelé přicházejí z `RequestLocalizationOptions` třídy:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický. Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze. Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, `DefaultRequestCulture` je použita.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Některé aplikace budou k nastavení použít řetězec dotazu <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> . Pro aplikace, které používají cookie přístup k hlavičkám nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu. Ve výchozím nastavení `QueryStringRequestCultureProvider` je jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu zaregistrován. Předáte parametry řetězce dotazu `culture` a `ui-culture` . Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Pokud předáte pouze jeden z těchto dvou ( `culture` nebo `ui-culture` ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali. Například nastavení pouze jazyková verze nastaví jak `Culture` a `UICulture` :

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze s ASP.NET Coreovou kulturou cookie . Použijte `MakeCookieValue` metodu k vytvoření cookie .

`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název, který cookie slouží ke sledování informací o preferované jazykové verzi uživatele. Výchozí cookie název je `.AspNetCore.Culture` .

cookieFormát je `c=%LANGCODE%|uic=%LANGCODE%` , kde `c` je `Culture` a je například `uic` `UICulture` :

```
c=en-UK|uic=en-US
```

Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.

### <a name="the-accept-language-http-header"></a>Hlavička protokolu HTTP pro přijetí – jazyk

[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele. Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému. Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.

### <a name="set-the-accept-language-http-header-in-ie"></a>Nastavení hlavičky protokolu HTTP Accept-Language v IE

1. Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.

1. Klepněte na **jazyky**.

   ![Možnosti Internetu](localization/_static/lang.png)

1. Klepněte na **nastavit jazykové předvolby**.

1. Klepněte na **Přidat jazyk**.

1. Přidejte jazyk.

1. Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.

### <a name="use-a-custom-provider"></a>Použití vlastního zprostředkovatele

Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází. Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele. Následující kód ukazuje, jak přidat vlastního zprostředkovatele:

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

Použijte `RequestLocalizationOptions` k přidání nebo odebrání poskytovatelů lokalizace.

### <a name="set-the-culture-programmatically"></a>Programové nastavení kultury

Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture` . Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do `footer` oddílu souboru rozložení, takže bude k dispozici pro všechna zobrazení:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage`Metoda nastaví jazykovou verzi cookie .

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt. Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do Razor částečného toku prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .

## <a name="model-binding-route-data-and-query-strings"></a>Vazba modelu data směrování a řetězce dotazů

Podívejte [se na téma chování globalizace modelu vazby dat trasy a řetězce dotazu](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Výrazy globalizace a lokalizace

Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí. I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly. Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.

[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.

Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>` , kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury. Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti. Viz třída <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

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

## <a name="additional-resources"></a>Další zdroje informací

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.
* [Globalizace a lokalizace aplikací .NET](/dotnet/standard/globalization-localization/index)
* [Prostředky v souborech. resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Sada nástrojů pro vícejazyčné aplikace od Microsoftu](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalizace & – obecné typy](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya

Vícejazyčný web umožňuje webu oslovit širší cílovou skupinu. ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.

Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization). Globalizace je proces návrhu aplikací, které podporují různé jazykové verze. Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.

Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí. Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.

Lokalizace aplikace zahrnuje následující:

1. Nastavit lokalizaci obsahu aplikace
1. Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.
1. Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Nastavit lokalizaci obsahu aplikace

<xref:Microsoft.Extensions.Localization.IStringLocalizer> a <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> bylo navrženo pro zlepšení produktivity při vývoji lokalizovaných aplikací. `IStringLocalizer` používá <xref:System.Resources.ResourceManager> a <xref:System.Resources.ResourceReader> k poskytnutí prostředků specifických pro jazykovou verzi v době běhu. Rozhraní má indexer a `IEnumerable` pro vracení lokalizovaných řetězců. `IStringLocalizer` nevyžaduje uložení výchozích řetězců jazyka v souboru prostředků. Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji. Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

V předchozím kódu `IStringLocalizer<T>` implementace pochází z [Injektáže závislosti](dependency-injection.md). Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title". Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace. Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků. Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka. Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace. Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.

Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód HTML. `IHtmlLocalizer` HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku. V ukázce zvýrazněné níže je pouze hodnota `name` parametru kódována HTML.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Obecně pouze lokalizovat text, ne HTML.

Na nejnižší úrovni můžete obdržet zástupné `IStringLocalizerFactory` [vkládání závislostí](dependency-injection.md):

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Výše uvedený kód ukazuje každou ze dvou metod Create Factory.

Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru. V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

Někteří vývojáři používají `Startup` třídu pro zahrnutí globálních nebo sdílených řetězců. V níže uvedené ukázce se `InfoController` `SharedResource` používají a Localize:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Lokalizace zobrazení

`IViewLocalizer`Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview). `ViewLocalizer`Třída implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení. Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer` :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

Výchozí implementace nástroje `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení. Neexistuje možnost použít globální sdílený soubor prostředků. `ViewLocalizer` implementuje lokalizátora pomocí `IHtmlLocalizer` , takže Razor nekóduje kód HTML jako lokalizovaný řetězec. Můžete parametrizovat řetězce prostředků a `IViewLocalizer` zakódovat parametry HTML, ale ne řetězec prostředku. Vezměte v úvahu následující Razor značky:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Soubor prostředků francouzštiny může obsahovat následující:

| Klíč | Hodnota |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.

> [!NOTE]
> Obecně pouze lokalizovat text, ne HTML.

Chcete-li použít sdílený soubor prostředků v zobrazení, vložení `IHtmlLocalizer<T>` :

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalizace DataAnnotations

Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>` . Pomocí možnosti `ResourcesPath = "Resources"` mohou být chybové zprávy v nástroji `RegisterViewModel` uloženy v jedné z následujících cest:

* *Resources/ViewModels. Account. RegisterViewModel. fr. resx*
* *Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

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

V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy. S tímto přístupem budou dataanotace používat pouze `SharedResource` místo prostředků pro každou třídu.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures a SupportedUICultures

ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze `SupportedCultures` a `SupportedUICultures` . Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je datum, čas, číslo a formátování měny. `SupportedCultures` Určuje také pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců. Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) . `SupportedUICultures`Určuje, které přeložené řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager). `ResourceManager`Jednoduše vyhledá řetězce specifické pro jazykovou verzi, které určuje `CurrentUICulture` . Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty. ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi. Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, 18. února 2016", ale pokud `CurrentCulture` je nastavená na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Soubory prostředků

Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu. Přeložené řetězce pro jazyk, který není výchozí, jsou izolované v souborech prostředků *. resx* . Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce. "ES" je kód jazyka pro španělštinu. Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:

1. V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.

   ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená. Druhá kontextová nabídka je otevřená pro přidání se zvýrazněným příkazem nová položka.](localization/_static/newi.png)

1. V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.

   ![Dialogové okno Přidat novou položku](localization/_static/res.png)

1. Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).

   ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

   Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .

   ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Pojmenovávání souborů prostředků

Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení. Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by měla být pojmenována *Startup. fr. resx*. Prostředek pro třídu by měl `LocalizationWebsite.Web.Controllers.HomeController` pojmenovat *Controllers. HomeController. fr. resx*. Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu. Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.

V ukázkovém projektu `ConfigureServices` Metoda nastaví `ResourcesPath` na "prostředky", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*. Případně můžete použít složky k uspořádání souborů prostředků. V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*. Pokud tuto možnost nepoužijete `ResourcesPath` , soubor *. resx* by přešel do základního adresáře projektu. Soubor prostředků pro `HomeController` by měl mít název *Controllers. HomeController. fr. resx*. Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.

| Název prostředku | Pojmenování teček nebo Path |
| ------------   | ------------- |
| Prostředky/řadiče. HomeController. fr. resx | Tečka  |
| Prostředky/řadiče/HomeController. fr. resx  | Cesta |

Soubory prostředků používané `@inject IViewLocalizer` v Razor zobrazeních následují podobně jako vzor. Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty. Razor zobrazení soubory prostředků napodobá cestě k jejich přidruženému souboru zobrazení. Za předpokladu, že nastavíme `ResourcesPath` "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:

* Prostředky/zobrazení/domů/o. fr. resx

* Prostředky/zobrazení. domů. about. fr. resx

Pokud tuto možnost nepoužijete `ResourcesPath` , bude soubor *. resx* pro zobrazení umístěn ve stejné složce jako zobrazení.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení. 

> [!WARNING]
> K tomu může dojít, když název projektu není platným identifikátorem .NET. V případě instance `my-project-name.csproj` bude použit kořenový obor názvů `my_project_name` a název sestavení, `my-project-name` který vede k této chybě. 

Pokud se kořenový obor názvů sestavení liší od názvu sestavení:

* Lokalizace ve výchozím nastavení nefunguje.
* Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení. `RootNamespace` je hodnota doby sestavení, která není k dispozici pro spuštěný proces. 

Pokud se `RootNamespace` liší od z `AssemblyName` , zahrňte následující v *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Předchozí kód umožňuje úspěšné vyřešení souborů RESX.

## <a name="culture-fallback-behavior"></a>Chování záložního kultivačního prostředí

Při hledání prostředku se lokalizace zapojit do "záložní kultury". Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze. Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi. To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO. Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX. Má nadřízenou španělštinu ES, která &mdash; není specifická pro žádnou zemi.

Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA". Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:

* *Welcome.fr-CA. resx*
* *Welcome. fr. resx*
* *Welcome. resx* (Pokud `NeutralResourcesLanguage` je to "fr-CA")

Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce. Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi. Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.

### <a name="generate-resource-files-with-visual-studio"></a>Generování souborů prostředků pomocí sady Visual Studio

Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada Visual Studio vytvoří třídu jazyka C# s vlastností pro každý řetězec. To obvykle není to, co chcete s ASP.NET Core. Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze). Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*). Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.

### <a name="add-other-cultures"></a>Přidat další jazykové verze

Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků. Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**). Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.

### <a name="configure-localization"></a>Konfigurace lokalizace

Lokalizace je nakonfigurovaná v `Startup.ConfigureServices` metodě:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Přidá služby lokalizace do kontejneru služby. Výše uvedený kód také nastaví cestu prostředků na prostředky.

* `AddViewLocalization` Přidá podporu pro lokalizované soubory zobrazení. V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení. Například "fr" v souboru *index. fr. cshtml* .

* `AddDataAnnotationsLocalization` přidává podporu pro lokalizované `DataAnnotations` ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.

### <a name="localization-middleware"></a>Middleware lokalizace

Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace. Middleware lokalizace je povolená v `Startup.Configure` metodě. Middleware lokalizace musí být konfigurovány před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()` ).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` Inicializuje `RequestLocalizationOptions` objekt. Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` výčtu a první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti. Výchozí zprostředkovatelé přicházejí z `RequestLocalizationOptions` třídy:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický. Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze. Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, `DefaultRequestCulture` je použita.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Některé aplikace budou k nastavení použít řetězec dotazu <xref:System.Globalization.CultureInfo> . Pro aplikace, které používají cookie přístup k hlavičkám nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu. Ve výchozím nastavení `QueryStringRequestCultureProvider` je jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu zaregistrován. Předáte parametry řetězce dotazu `culture` a `ui-culture` . Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Pokud předáte pouze jeden z těchto dvou ( `culture` nebo `ui-culture` ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali. Například nastavení pouze jazyková verze nastaví jak `Culture` a `UICulture` :

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze s ASP.NET Coreovou kulturou cookie . Použijte `MakeCookieValue` metodu k vytvoření cookie .

`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název, který cookie slouží ke sledování informací o preferované jazykové verzi uživatele. Výchozí cookie název je `.AspNetCore.Culture` .

cookieFormát je `c=%LANGCODE%|uic=%LANGCODE%` , kde `c` je `Culture` a je například `uic` `UICulture` :

```
c=en-UK|uic=en-US
```

Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.

### <a name="the-accept-language-http-header"></a>Hlavička protokolu HTTP pro přijetí – jazyk

[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele. Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému. Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.

### <a name="set-the-accept-language-http-header-in-ie"></a>Nastavení hlavičky protokolu HTTP Accept-Language v IE

1. Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.

1. Klepněte na **jazyky**.

   ![Možnosti Internetu](localization/_static/lang.png)

1. Klepněte na **nastavit jazykové předvolby**.

1. Klepněte na **Přidat jazyk**.

1. Přidejte jazyk.

1. Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.

### <a name="the-content-language-http-header"></a>Hlavička protokolu HTTP v jazykovém obsahu

Hlavička entity [jazyka obsahu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) :

* Slouží k popisu jazyků, které jsou určené pro cílovou skupinu.
* Umožňuje uživateli rozlišovat podle vlastního preferovaného jazyka uživatelů.

Záhlaví entit se používají v požadavcích HTTP i v odpovědích.

`Content-Language`Záhlaví lze přidat nastavením vlastnosti `ApplyCurrentCultureToResponseHeaders` .

Přidání `Content-Language` hlavičky:

* Umožňuje RequestLocalizationMiddleware nastavit `Content-Language` hlavičku pomocí `CurrentUICulture` .
* Eliminuje nutnost explicitně nastavit hlavičku odpovědi `Content-Language` .

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a>Použití vlastního zprostředkovatele

Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází. Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele. Následující kód ukazuje, jak přidat vlastního zprostředkovatele:

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

Použijte `RequestLocalizationOptions` k přidání nebo odebrání poskytovatelů lokalizace.

### <a name="set-the-culture-programmatically"></a>Programové nastavení kultury

Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture` . Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do `footer` oddílu souboru rozložení, takže bude k dispozici pro všechna zobrazení:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage`Metoda nastaví jazykovou verzi cookie .

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt. Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do Razor částečného toku prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .

## <a name="model-binding-route-data-and-query-strings"></a>Vazba modelu data směrování a řetězce dotazů

Podívejte [se na téma chování globalizace modelu vazby dat trasy a řetězce dotazu](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Výrazy globalizace a lokalizace

Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí. I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly. Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.

[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.

Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>` , kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury. Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti. Viz třída <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

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

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Další zdroje informací

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.
* [Globalizace a lokalizace aplikací .NET](/dotnet/standard/globalization-localization/index)
* [Prostředky v souborech. resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Sada nástrojů pro vícejazyčné aplikace od Microsoftu](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalizace & – obecné typy](http://hishambinateya.com/localization-and-generics)

::: moniker-end
