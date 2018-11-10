---
title: Globalizace a lokalizace v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ASP.NET Core nabízí služby a middleware pro lokalizaci obsahu do různých jazyků a kultur.
ms.author: riande
ms.date: 01/14/2017
uid: fundamentals/localization
ms.openlocfilehash: af11906f86fe4ea91ed520584daedc094ab2dc0b
ms.sourcegitcommit: edb9d2d78c9a4d68b397e74ae2aff088b325a143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51505827"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalizace a lokalizace v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien překážka](https://twitter.com/damien_bod), [ADAM Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://twitter.com/NadeemAfana), a [Ateya Hisham Bin](https://twitter.com/hishambinateya)

Vytvoření vícejazyčné web pomocí ASP.NET Core, vám umožní oslovit větší cílovou skupinu webu. ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.

Internacionalizace zahrnuje [globalizace](/dotnet/api/system.globalization) a [lokalizace](/dotnet/standard/globalization-localization/localization). Globalizace je proces návrhu aplikace, které podporují různé jazykové verze. Globalizace přidává podporu pro vstup, zobrazení a výstup definovanou sadu skripty jazyka, které se týkají konkrétní geografické oblasti.

Lokalizace je proces přizpůsobování globalizované aplikaci, která již byl zpracován lokalizovatelnosti konkrétní jazykovou verzi a národní prostředí. Další informace najdete v části **podmínkách globalizace a lokalizace** téměř na konci tohoto dokumentu.

Lokalizace aplikací zahrnuje následující:

1. Aby lokalizovatelný obsah aplikace

2. Zadejte lokalizované prostředky pro jazyky a jazykové verze, kterou podporujete

3. Implementovat strategii vyberte jazykovou verzi pro každý požadavek

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([stažení](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Aby lokalizovatelný obsah aplikace

Zavedené v ASP.NET Core, `IStringLocalizer` a `IStringLocalizer<T>` byl navržen ke zvýšení produktivity při vývoji lokalizovaných aplikací. `IStringLocalizer` používá [ResourceManager](/dotnet/api/system.resources.resourcemanager) a [ResourceReader](/dotnet/api/system.resources.resourcereader) k poskytnutí specifické pro jazykovou verzi prostředků v době běhu. Jednoduché rozhraní má indexer a `IEnumerable` pro vrácení lokalizované řetězce. `IStringLocalizer` nevyžaduje k uložení řetězců výchozí jazyk v souboru prostředků. Můžete vyvíjet aplikace určené pro lokalizaci a není nutné vytvářet soubory prostředků v rané fázi vývoje. Následující kód ukazuje postup při zabalení řetězec "Title o" pro lokalizaci.

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

Ve výše uvedeném kódu `IStringLocalizer<T>` implementace pochází z [injektáž závislostí](dependency-injection.md). Pokud lokalizované hodnoty "Title o" nebyl nalezen, pak klíč indexeru se vrátí, tedy řetězec "Title o". Můžete ponechat výchozí nastavení řetězcových literálů jazyka v aplikaci a zabalit je do lokalizátora, tak, aby se mohli soustředit na vývoj aplikace. Vývoj aplikace s výchozí jazyk a připravený k lokalizaci bez první vytvoření výchozího souboru prostředků. Alternativně můžete použít tradičního přístupu jinam a zadejte klíč k načtení výchozí řetězec jazyka. Pro mnoho vývojářů nový pracovní postup nemají výchozí jazyk *RESX* Souborová služba a jednoduše obtékání řetězcové literály můžou snížit režii lokalizace aplikace. Jinými vývojáři budou nejdřív tradiční pracovní postup, jak ho můžete usnadnit práci s delší textové literály a usnadňují aktualizovat lokalizované řetězce.

Použití `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód jazyka HTML. `IHtmlLocalizer` HTML kóduje argumenty, které jsou ve formátu řetězce zdroje, ale nebude samotný řetězec prostředku s kódováním HTML. V ukázce jejichž přehled najdete níže pouze hodnotu `name` parametr není kódován jazykem HTML.

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

**Poznámka:** obvykle chcete lokalizovat pouze text nebo HTML není.

Na nejnižší úrovni, můžete získat `IStringLocalizerFactory` z celkového počtu [injektáž závislostí](dependency-injection.md):

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Výše uvedený kód ukazuje, každý objekt pro vytváření dvě metody vytvoření.

Můžete oddílu lokalizované řetězce řadič, oblasti, nebo mít právě jeden kontejner. V ukázkové aplikaci fiktivní třída s názvem `SharedResource` slouží pro sdílené prostředky.

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

Někteří vývojáři použít `Startup` třídy tak, aby obsahovala globální nebo sdíleného řetězce. V následující ukázce `InfoController` a `SharedResource` Lokalizátoři používají:

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Lokalizace zobrazení

`IViewLocalizer` Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview). `ViewLocalizer` Třída implementuje toto rozhraní a najde umístění prostředků ze zobrazení cesty k souboru. Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer`:

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

Výchozí implementace `IViewLocalizer` vyhledá soubor prostředků založený na názvu souboru v zobrazení. Neexistuje žádná možnost použít soubor globální sdíleného prostředku. `ViewLocalizer` implementuje lokalizátora pomocí `IHtmlLocalizer`, takže nebude HTML Razor kódování lokalizovaný řetězec. Můžete parametrizovat řetězce prostředků a `IViewLocalizer` se použije kódování HTML. parametry, ale ne řetězec prostředku. Vezměte v úvahu následující kód Razor:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Francouzské zdrojového souboru může obsahovat následující:

| Key | Hodnota |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b> ` |

Vykreslené zobrazení bude obsahovat značky HTML ze souboru prostředků.

**Poznámka:** obvykle chcete lokalizovat pouze text nebo HTML není.

Použít soubor sdíleného prostředku v zobrazení, Vložit `IHtmlLocalizer<T>`:

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalizace DataAnnotations

DataAnnotations chybové zprávy jsou lokalizovány pomocí `IStringLocalizer<T>`. Pomocí možnosti `ResourcesPath = "Resources"`, chybové zprávy v `RegisterViewModel` mohou být uloženy v jednom z následujících cest:

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

V ASP.NET Core MVC 1.1.0 a vyšší, než ověření atributy jsou lokalizovány. ASP.NET Core MVC 1,0 nemá **není** vyhledávání lokalizovaných řetězců pro atributy bez ověření.

<a name="one-resource-string-multiple-classes"></a>
### <a name="using-one-resource-string-for-multiple-classes"></a>Porovnáním jednoho řetězce prostředků pro více tříd

Následující kód ukazuje, jak používat jeden řetězec prostředku pro ověření atributů s více třídami:

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

V kódu, který předchází `SharedResource` je třída odpovídající resx, kde jsou uloženy ověřovacích zpráv. S tímto přístupem budete používat pouze DataAnnotations `SharedResource`, namísto prostředků pro každou třídu.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Zadejte lokalizované prostředky pro jazyky a jazykové verze, kterou podporujete

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures a SupportedUICultures

ASP.NET Core můžete zadat hodnoty dvou jazykové verze, `SupportedCultures` a `SupportedUICultures`. [CultureInfo](/dotnet/api/system.globalization.cultureinfo) objekt pro `SupportedCultures` určuje výsledky ze závislých na jazykové verzi funkce, jako je například datum, čas, čísla a formátování měny. `SupportedCultures` také určuje pořadí řazení textu, konvence velká a malá písmena a porovnávání řetězců. Zobrazit [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) Další informace o tom, jak server získá jazykovou verzi. `SupportedUICultures` Určuje, který se přeloží řetězce (z *RESX* soubory) jsou vyhledány podle [ResourceManager](/dotnet/api/system.resources.resourcemanager). `ResourceManager` Jednoduše vyhledá řetězců specifické pro jazykovou verzi, které je určeno `CurrentUICulture`. Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty. ASP.NET Core zkontroluje tyto hodnoty při vykreslování funkcí závislých na jazykové verzi. Pokud jazykové verze aktuálního vlákna nastavená na "en US" (angličtina, Spojené státy), například `DateTime.Now.ToLongDateString()` zobrazí "Čtvrtek, únor 18. 2016", ale pokud `CurrentCulture` je nastavena na "es-ES" (španělština, Španělsko) výstup bude "jueves, de smluvních 18 de 2016".

## <a name="resource-files"></a>Soubory prostředků

Soubor prostředků je užitečné mechanismus pro rozdělení zdrojů lokalizovatelných řetězců z kódu. Přeložené řetězce pro jiné než výchozí jazyk jsou izolované *RESX* soubory prostředků. Například můžete chtít vytvořit soubor Španělština prostředků s názvem *Welcome.es.resx* obsahující přeložit řetězce. "es" je kód jazyka pro španělštinu. Chcete-li vytvořit tento soubor prostředků v sadě Visual Studio:

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **přidat** > **nová položka**.

    ![Vnořené místní nabídku: V Průzkumníku řešení kontextové nabídky je otevřen pro prostředky. Druhá místní nabídku je otevřen pro přidání zobrazení nová položka příkazu zvýrazní.](localization/_static/newi.png)

2. V **Hledat v nainstalovaných šablonách** zadejte "prostředek" a název souboru.

    ![Přidat novou položku – dialogové okno](localization/_static/res.png)

3. Zadejte hodnotu klíče (nativní řetězec) **název** sloupce a řetězce přeložené v **hodnotu** sloupce.

    ![Welcome.ES.resx soubor (soubor úvodní prostředků pro španělštinu) s slova Hello ve sloupci Název a word Hola (Hello ve španělštině) ve sloupci Hodnota](localization/_static/hola.png)

    Sada Visual Studio zobrazí *Welcome.es.resx* souboru.

    ![Průzkumník řešení zobrazující soubor prostředků úvodní Španělština (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Pojmenovávání souborů prostředků

Úplný název typu jejich třídy minus názvu sestavení jsou pojmenovány prostředky. Například francouzské prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu `LocalizationWebsite.Web.Startup` by se pojmenoval *Startup.fr.resx*. Prostředek pro třídu `LocalizationWebsite.Web.Controllers.HomeController` by se pojmenoval *Controllers.HomeController.fr.resx*. Pokud obor názvů cílové třídy není stejný jako název sestavení potřebujete úplný název typu. Například v ukázce projektu prostředků pro typ `ExtraNamespace.Tools` by se pojmenoval *ExtraNamespace.Tools.fr.resx*.

V projektu vzorku `ConfigureServices` metody nastaví `ResourcesPath` "Resources", tak pro soubor francouzské prostředek domovské řadiče relativní cestu projektu je *Resources/Controllers.HomeController.fr.resx*. Alternativně můžete použít složek k uspořádání souborů prostředků. Pro kontroler home, cesta bude *Resources/Controllers/HomeController.fr.resx*. Pokud nepoužíváte `ResourcesPath` možnost, *RESX* soubor přejde do základního adresáře projektu. Soubor prostředků pro `HomeController` by se pojmenoval *Controllers.HomeController.fr.resx*. Na výběr mezi používáním zásady vytváření názvů tečky nebo cesta závisí na způsobu uspořádání souborů prostředků.

| Název prostředku | Tečka nebo názvů cest |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Tečka  |
| Resources/Controllers/HomeController.fr.resx  | Cesta |
|    |     |

Soubory prostředků pomocí `@inject IViewLocalizer` v zobrazení syntaxe Razor proveďte podobný vzorec. Soubor prostředků pro zobrazení může být pojmenované pomocí pojmenování tečkou nebo názvů cest. Soubory prostředků zobrazení Razor napodobovat cestu k souboru jejich přidružené zobrazení. Za předpokladu, že jsme nastavili `ResourcesPath` "Resources", soubor prostředků francouzské přidružená *Views/Home/About.cshtml* zobrazení může být buď z následujících akcí:

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Pokud nepoužíváte `ResourcesPath` možnost, *RESX* soubor pro zobrazení by nacházet ve stejné složce jako zobrazení.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) atribut obsahuje kořenový obor názvů sestavení při kořenový obor názvů sestavení se liší od názvu sestavení. 

Pokud kořenový obor názvů sestavení se liší od názvu sestavení:

* Lokalizace nefunguje ve výchozím nastavení.
* Lokalizace selže kvůli způsobu, jakým se vyhledávají prostředky v rámci sestavení. `RootNamespace` je hodnota čas sestavení, která není k dispozici do spuštěného procesu. 

Pokud `RootNamespace` se liší od `AssemblyName`, vložte následující *AssemblyInfo.cs* (s hodnotami parametrů nahradit skutečnými hodnotami):

```Csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Předchozí kód umožňuje úspěšného vyřešení resx – soubory.

## <a name="culture-fallback-behavior"></a>Chování záložní jazykovou verzi

Při vyhledávání pro určitý prostředek, lokalizace mezi "culture záložní". Počínaje požadovanou jazykovou verzi, pokud nebyla nalezena, vrátí se k nadřazená jazykové verze této jazykové verze. Jako aside [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) vlastnost představuje nadřazená jazykové verze. To obvykle (ale ne vždy) znamená odebrání národní signifier ze souboru ISO. Dialekt Španělština používaný v Mexiku je například "es-MX". Má nadřazenou "es"&mdash;Španělština nespecifickou do zemí.

Představte si, že váš web obdrží požadavek na prostředek "Vítejte" pomocí jazykové verze "fr-CA". Lokalizace systému vypadá pro následující prostředky, v pořadí a vybere první shoda:

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (Pokud `NeutralResourcesLanguage` je "fr-CA")

Například pokud odeberete jazykovou verzi označení ".fr" a vy musíte jazyková verze nastavena na francouzštinu, výchozího souboru prostředků je pro čtení a jsou lokalizované řetězce. Resource manager Určuje výchozí nebo záložní prostředky pro Pokud nic splňuje požadovanou jazykovou verzi. Pokud chcete jenom vrátit klíč, když chybí prostředek pro požadovanou jazykovou verzi je nesmí mít výchozího souboru prostředků.

### <a name="generate-resource-files-with-visual-studio"></a>Generovat soubory prostředků pomocí sady Visual Studio

Pokud vytvoříte soubor prostředků v sadě Visual Studio bez jazykové verze v názvu souboru (například *Welcome.resx*), vytvoří Visual Studio třída jazyka C# s vlastností pro každý řetězec. To je obvykle nechcete s ASP.NET Core. Obvykle není nutné výchozí *RESX* souboru prostředků ( *RESX* soubor bez názvu jazykové verze). Doporučujeme vytvořit *RESX* soubor s názvem jazykové verze (například *Welcome.fr.resx*). Když vytvoříte *RESX* soubor s názvem jazykové verze, Visual Studio nebude generovat soubor třídy. Předpokládáme, že celá řada vývojářů nevytvoří soubor prostředků výchozího jazyka.

### <a name="add-other-cultures"></a>Přidat další jazykové verze

Každá kombinace jazyk a jazykovou verzi (jiné než výchozí jazyk) vyžaduje soubor prostředků jedinečné. Vytvořit soubor prostředků pro různé jazykové verze a národní prostředí tak, že vytvoříte nové soubory prostředků, ve kterých jsou kódy jazyk ISO část názvu souboru (například **en-us**, **fr-ca**, a  **en-gb**). Tyto kódy ISO jsou umístěny mezi název souboru a *RESX* příponu, stejně jako v souboru *Welcome.es MX.resx* (španělština/Mexiko).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementovat strategii vyberte jazykovou verzi pro každý požadavek

### <a name="configure-localization"></a>Konfigurace lokalizace

Lokalizace je nakonfigurovaný v `Startup.ConfigureServices` metody:

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Lokalizační služby přidá do kontejneru služby. Kód výše také nastaví prostředky cesta k "Resources".

* `AddViewLocalization` Přidává podporu pro lokalizované zobrazit soubory. V tomto zobrazení Ukázka lokalizace podle zobrazení příponu souboru. Například "fr" v *Index.fr.cshtml* souboru.

* `AddDataAnnotationsLocalization` Přidává podporu pro lokalizované `DataAnnotations` ověření zprávy prostřednictvím `IStringLocalizer` abstrakce.

### <a name="localization-middleware"></a>Lokalizace middlewaru

Aktuální jazykovou verzi na vyžádání je nastavena v lokalizace [Middleware](xref:fundamentals/middleware/index). Lokalizace middlewaru je povolený v `Startup.Configure` metody. Lokalizace middleware musí být nakonfigurovaná před veškerý middleware, který může zkontrolovat žádost o jazykové verzi (například `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]

`UseRequestLocalization` inicializuje `RequestLocalizationOptions` objektu. U každého požadavku seznamu z `RequestCultureProvider` v `RequestLocalizationOptions` je vypočten a prvním poskytovatelem, který úspěšně určuje jazykovou verzi požadavku se používá. Výchozí poskytovatele pocházejí z `RequestLocalizationOptions` třídy:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Seznam výchozích přechází od nejvíce po nejméně specifická. Později v tomto článku uvidíme, jak můžete změnit pořadí a dokonce přidat vlastní jazyková verze zprostředkovatele. Pokud žádný poskytovatelů určit jazykové verze požadavku, `DefaultRequestCulture` se používá.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Některé aplikace použije řetězec dotazu. Chcete-li nastavit [jazykové verze a jazykové verze uživatelského rozhraní](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). Aplikace používající soubor cookie nebo přístup hlavičku Accept-Language přidat řetězec dotazu k adrese URL je užitečné pro ladění a testování kódu. Ve výchozím nastavení `QueryStringRequestCultureProvider` je zaregistrovaný jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu. Předávání parametrů řetězce dotazu `culture` a `ui-culture`. Následující příklad nastaví španělština/Mexiko konkrétní jazykovou verzi (jazyka a oblasti):

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Pokud předáte pouze v jednom ze dvou (`culture` nebo `ui-culture`), nastaví obě hodnoty pomocí ten předaný v poskytovateli řetězec dotazu. Například nastavení pouze jazykové verze se jednak nastaveny `Culture` a `UICulture`:

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

Produkční aplikace bude často poskytují mechanismus pro nastavit jazykovou verzi pomocí souboru cookie jazykovou verzi ASP.NET Core. Použití `MakeCookieValue` metodu pro vytvoření souboru cookie.

`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název souboru cookie používá ke sledování uživatel upřednostňované jazykové verze. Výchozí název souboru cookie je `.AspNetCore.Culture`.

Formát souboru cookie je `c=%LANGCODE%|uic=%LANGCODE%`, kde `c` je `Culture` a `uic` je `UICulture`, například:

    c=en-UK|uic=en-US

Pokud zadáte pouze jeden z informace o jazykové verzi a jazykové verze uživatelského rozhraní, zadané jazykové verze se použije pro informace o jazykové verzi a jazykové verze uživatelského rozhraní.

### <a name="the-accept-language-http-header"></a>Hlavička Accept-Language protokolu HTTP

[Hlavičku Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) nastavit u většiny prohlížečů a byla původně určena k určení jazyk daného uživatele. Toto nastavení určuje, co v prohlížeči je nastavená na odeslání nebo dědí ze základního operačního systému. Hlavičku Accept-Language HTTP z prohlížeče požadavku není spolehlivý způsob, jak zjistit preferovaný jazyk uživatele (viz [nastavení předvoleb jazyka v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Produkční aplikace by měla obsahovat způsob, jak uživatelům přizpůsobit si sami vyberou jazykové verze.

### <a name="set-the-accept-language-http-header-in-ie"></a>Nastavit hlavičku Accept-Language HTTP v Internet Exploreru

1. Pomocí ikony ozubeného kola, klepněte na **Možnosti Internetu**.

2. Klepněte na **jazyky**.

    ![Možnosti Internetu](localization/_static/lang.png)

3. Klepněte na **nastavit předvolby jazyka**.

4. Klepněte na **přidat jazyk**.

5. Přidáte jazyk.

6. Klepněte na jazyk a potom klepněte na **nahoru**.

### <a name="use-a-custom-provider"></a>Použití vlastního zprostředkovatele

Předpokládejme, že chcete, aby vaši zákazníci ukládají svůj jazyk a jazykovou verzi ve vašich databázích. Můžete napsat zprostředkovatele k vyhledání těchto hodnot pro uživatele. Následující kód ukazuje, jak přidat vlastního zprostředkovatele:

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

Použití `RequestLocalizationOptions` přidat nebo odebrat zprostředkovatele lokalizace.

### <a name="set-the-culture-programmatically"></a>Nastavit jazykovou verzi prostřednictvím kódu programu

Tato ukázka **Localization.StarterWeb** projekt [Githubu](https://github.com/aspnet/entropy) obsahuje uživatelského rozhraní pro nastavení `Culture`. *Views/Shared/_SelectLanguagePartial.cshtml* souboru můžete vybrat ze seznamu podporovaných jazykových verzí jazykovou verzi:


[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Views/Shared/_SelectLanguagePartial.cshtml* přidá soubor `footer` rozložení souboru tak bude k dispozici pro všechna zobrazení:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage` Metoda nastaví soubor cookie jazykovou verzi.

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

Nelze zařadit *_SelectLanguagePartial.cshtml* na ukázkový kód pro tento projekt. **Localization.StarterWeb** projekt [Githubu](https://github.com/aspnet/entropy) obsahuje kód pro flow `RequestLocalizationOptions` pro Razor, která se částečné prostřednictvím [injektáž závislostí](dependency-injection.md) kontejneru.

## <a name="globalization-and-localization-terms"></a>Globalizace a lokalizace podmínky

Proces lokalizace aplikace také vyžaduje základní znalost relevantní znakových sad, které se běžně používají při vývoji moderních softwaru a porozumění problémy spojené s nimi. I když všechny počítače ukládání textu jako čísla (kódy), jiné systémy uchovávají stejný text pomocí různých čísel. Proces lokalizace odkazuje na překladu aplikace uživatelského rozhraní (UI) pro konkrétní jazykovou verzi a národní prostředí.

[Lokalizovatelnost](/dotnet/standard/globalization-localization/localizability-review) je dílčí proces pro ověření, že globalizovaná aplikace je připravená pro lokalizaci.

[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) formátu pro název jazykové verze je `<languagecode2>-<country/regioncode2>`, kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` subkulturu kód. Například `es-CL` pro španělština (Chile) `en-US` pro angličtinu (Spojené státy) a `en-AU` pro angličtinu (Austrálie). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací ISO 639 dvoupísmenné malá jazykovou verzi kódu související s jazykem a ISO 3166 kódu velká písmena subkulturu dvoupísmenné přidružené zemi nebo oblast. Zobrazit [název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).

Internacionalizace je často se zkracuje na "I18N". Zkratka přijímá první a poslední písmena a počet písmena mezi nimi tedy 18 zastupuje počet písmena první "I" a poslední "N". Totéž platí pro (G11N) globalizace a lokalizace (L10N).

Podmínky:

* Globalizace (G11N): Proces zpřístupnění aplikace podporují různé jazyky a oblastech.
* Lokalizace (L10N): Proces přizpůsobení aplikace pro daný jazyk a oblast.
* Internacionalizace (I18N): Popisuje globalizace a lokalizace.
* Jazyková verze: Je jazyk a volitelně oblast.
* Neutrální jazykové verze: jazyková verze, která má zadaný jazyk, ale ne v oblasti. (například "en", "es")
* Konkrétní jazykové verze: jazyková verze, který má zadaný jazyk a oblast. (například "en US", "en-GB", "es-CL")
* Nadřazená jazykové verze: neutrální jazykovou verzi, která obsahuje konkrétní jazykovou verzi. (například "en" je nadřazenou jazykovou verzi "en US" a "en-GB")
* Národní prostředí: Národní prostředí je stejný jako jazykovou verzi.

[!INCLUDE[](~/includes/currency.md)]

## <a name="additional-resources"></a>Další zdroje

* [Projekt Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) použité v tomto článku.
* [Globalizace a lokalizace aplikací .NET](/dotnet/standard/globalization-localization/index)
* [Prostředky v souborech .resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft Multilingual App Toolkit](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
