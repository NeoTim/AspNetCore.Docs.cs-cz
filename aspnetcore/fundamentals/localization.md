---
title: Globalizace a lokalizace v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak ASP.NET Core poskytuje služby a middleware pro lokalizaci obsahu do různých jazyků a kultur.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: 91db83eb8685c7bee5e976f386c2a12c4090b734
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440815"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalizace a lokalizace v ASP.NET jádru

[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), Bart [Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), a [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Vytvoření vícejazyčné webové stránky s ASP.NET Core umožní vaše stránky oslovit širší publikum. ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.

Internacionalizace zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization). Globalizace je proces navrhování aplikací, které podporují různé jazykové verze. Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují k určitým zeměpisným oblastem.

Lokalizace je proces přizpůsobení globalizované aplikace, kterou jste již zpracovali pro lokalizovatelnost, na konkrétní jazykovou verzi nebo národní prostředí. Další informace naleznete v **tématu Globalizace a podmínky lokalizace** na konci tohoto dokumentu.

Lokalizace aplikace zahrnuje následující:

1. Nastavení obsahu aplikace jako lokalizovatelného

2. Poskytněte lokalizované prostředky pro jazyky a jazykové verze, které podporujete

3. Implementace strategie pro výběr jazyka/jazykové verze pro každý požadavek

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="make-the-apps-content-localizable"></a>Nastavení obsahu aplikace jako lokalizovatelného

Představený v ASP.NET `IStringLocalizer` `IStringLocalizer<T>` Core a byl navržen tak, aby zlepšil produktivitu při vývoji lokalizovaných aplikací. `IStringLocalizer`Používá [ResourceManager](/dotnet/api/system.resources.resourcemanager) a [ResourceReader](/dotnet/api/system.resources.resourcereader) k poskytování prostředků specifických pro jazykovou verzi za běhu. Jednoduché rozhraní má indexer `IEnumerable` a pro vrácení lokalizovaných řetězců. `IStringLocalizer`nevyžaduje uložení výchozích jazykových řetězců do souboru prostředků. Můžete vyvinout aplikaci určenou pro lokalizaci a není nutné vytvářet soubory prostředků v rané fázi vývoje. Níže uvedený kód ukazuje, jak zabalit řetězec "O názvu" pro lokalizaci.

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

Ve výše uvedeném `IStringLocalizer<T>` kódu implementace pochází z [vkládání závislostí](dependency-injection.md). Pokud není nalezena lokalizovaná hodnota "O titulu", je vrácen klíč indexeru, to znamená řetězec "O titulu". Můžete ponechat výchozí jazyk literál řetězce v aplikaci a zabalit je v localizer, takže se můžete soustředit na vývoj aplikace. Aplikaci vyvíjíte s výchozím jazykem a připravujete ji na krok lokalizace, aniž byste nejprve vytvořili výchozí soubor prostředků. Alternativně můžete použít tradiční přístup a poskytnout klíč k načtení výchozího řetězce jazyka. Pro mnoho vývojářů nový pracovní postup, který nemá výchozí soubor *Resx* a jednoduše zalomení literály řetězce může snížit režii lokalizace aplikace. Ostatní vývojáři budou upřednostňovat tradiční pracovní tok, protože může usnadnit práci s delšími řetězcovými literály a usnadnit aktualizaci lokalizovaných řetězců.

Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují HTML. `IHtmlLocalizer`HTML kóduje argumenty, které jsou formátovány v řetězci prostředku, ale nekóduje samotný řetězec prostředku. V níže zvýrazněné ukázce `name` je kódována pouze hodnota parametru HTML.

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

**Poznámka:** Obecně chcete pouze lokalizovat text a ne HTML.

Na nejnižší úrovni se `IStringLocalizerFactory` můžete dostat z [vkládání závislostí](dependency-injection.md):

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Výše uvedený kód ukazuje každou ze dvou metod vytvoření z výroby.

Lokalizované řetězce můžete rozdělit podle řadiče, oblasti nebo mít pouze jeden kontejner. V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

Někteří vývojáři `Startup` používají třídu obsahovat globální nebo sdílené řetězce. V níže uvedeném `InfoController` vzorku `SharedResource` se používají lokalizátory a lokalizátory:

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Zobrazit lokalizaci

Služba `IViewLocalizer` poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview). Třída `ViewLocalizer` implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení. Následující kód ukazuje, jak používat `IViewLocalizer`výchozí implementaci aplikace :

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

Výchozí implementace `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení. Neexistuje žádná možnost použít globální soubor sdílených prostředků. `ViewLocalizer`implementuje lokalizátor pomocí `IHtmlLocalizer`, takže Razor není HTML kódovat lokalizovaný řetězec. Můžete parametrizovat řetězce prostředků `IViewLocalizer` a html kódovat parametry, ale ne řetězec prostředku. Zvažte následující značky Razor:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Francouzský soubor prostředků může obsahovat následující:

| Klíč | Hodnota |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Vykreslené zobrazení by obsahovalo značky HTML ze souboru prostředků.

**Poznámka:** Obecně chcete pouze lokalizovat text a ne HTML.

Chcete-li použít sdílený soubor prostředků `IHtmlLocalizer<T>`v zobrazení, vložte :

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalizace datových anotací

DataAnnotations chybové zprávy `IStringLocalizer<T>`jsou lokalizovány s . Pomocí této `ResourcesPath = "Resources"`možnosti mohou `RegisterViewModel` být chybové zprávy v aplikaci uloženy v jedné z následujících cest:

* *Zdroje/ViewModels.Account.RegisterViewModel.fr.resx*
* *Zdroje/ViewModels/Účet/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

V ASP.NET core MVC 1.1.0 a vyšší, jsou lokalizovány atributy bez ověření. ASP.NET Core MVC 1.0 **nevyhledává** lokalizované řetězce pro atributy bez ověření.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Použití jednoho řetězce prostředků pro více tříd

Následující kód ukazuje, jak použít jeden řetězec prostředků pro atributy ověření s více třídami:

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

V předchozím kódu `SharedResource` je třída odpovídající resx, kde jsou uloženy ověřovací zprávy. S tímto přístupem DataAnnotations `SharedResource`bude používat pouze , nikoli prostředek pro každou třídu.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Poskytněte lokalizované prostředky pro jazyky a jazykové verze, které podporujete

### <a name="supportedcultures-and-supporteduicultures"></a>Podporované kultury a podporované uikultury

ASP.NET Core umožňuje zadat dvě `SupportedCultures` hodnoty `SupportedUICultures`jazykové verze a . [Objekt CultureInfo](/dotnet/api/system.globalization.cultureinfo) `SupportedCultures` pro určuje výsledky funkcí závislých na jazykové verzi, jako je například datum, čas, číslo a formátování měny. `SupportedCultures`také určuje pořadí řazení textu, konvencí písmen a porovnávání řetězců. Viz [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) další informace o tom, jak server získá jazykovou verzi. Určuje, `SupportedUICultures` které přeložené řetězce (ze souborů *.resx)* jsou vyhledány [ResourceManager](/dotnet/api/system.resources.resourcemanager). Jednoduše `ResourceManager` vyhledá řetězce specifické pro jazykovou verzi, které jsou určeny . `CurrentUICulture` Každé vlákno v `CurrentCulture` rozhraní `CurrentUICulture` .NET má a objekty. ASP.NET Core kontroluje tyto hodnoty při vykreslování funkcí závislých na jazykové verzi. Například pokud je jazyková verze aktuálního vlákna nastavena na "en US" (angličtina, Spojené státy), `DateTime.Now.ToLongDateString()` zobrazí `CurrentCulture` se "Čtvrtek, únor 18, 2016", ale pokud je nastavena na "es-ES" (španělština, Španělsko), výstup bude "jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Soubory prostředků

Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců od kódu. Přeložené řetězce pro nevýchozí jazyk jsou izolovány v souborech prostředků *.resx.* Můžete například vytvořit španělský soubor prostředků s názvem *Welcome.es.resx* obsahující přeložené řetězce. "es" je kód jazyka pro španělštinu. Vytvoření tohoto souboru prostředků v sadě Visual Studio:

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.

    ![Vnořená kontextová nabídka: V Průzkumníku řešení je pro prostředky otevřena kontextová nabídka. Druhá kontextová nabídka je otevřena pro add příkaz uvede příkaz Nová položka zvýrazněný.](localization/_static/newi.png)

2. Do pole **Hledat nainstalované šablony** zadejte "zdroj" a pojmenujte soubor.

    ![Dialogové okno Přidat novou položku](localization/_static/res.png)

3. Zadejte hodnotu klíče (nativní řetězec) do sloupce **Název** a přeložený řetězec ve sloupci **Hodnota.**

    ![Welcome.es.resx soubor (uvítací soubor prostředků pro španělštinu) se slovem Dobrý den ve sloupci Název a slovo Hola (Hello ve španělštině) ve sloupci Hodnota](localization/_static/hola.png)

    Visual Studio zobrazuje soubor *Welcome.es.resx.*

    ![Průzkumník řešení zobrazující uvítací španělský (es) soubor prostředků](localization/_static/se.png)

## <a name="resource-file-naming"></a>Pojmenování souboru prostředků

Prostředky jsou pojmenovány pro úplný název typu své třídy mínus název sestavení. Například francouzský zdroj v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by se jmenoval *Startup.fr.resx*. Prostředek pro třídu `LocalizationWebsite.Web.Controllers.HomeController` by se jmenoval *Controllers.HomeController.fr.resx*. Pokud obor názvů cílové třídy není stejný jako název sestavení, budete potřebovat úplný název typu. Například v ukázkovém projektu by `ExtraNamespace.Tools` zdroj pro daný typ měl název *ExtraNamespace.Tools.fr.resx*.

V ukázkovém `ConfigureServices` projektu metoda `ResourcesPath` nastaví na "Zdroje", takže relativní cesta projektu pro soubor prostředků domácího řadiče je *Resources/Controllers.HomeController.fr.resx*. Případně můžete použít složky k uspořádání souborů prostředků. Pro domácí řadič by cesta byla *Resources/Controllers/HomeController.fr.resx*. Pokud `ResourcesPath` tuto možnost nepoužijete, soubor *Resx* by se přešel do základního adresáře projektu. Soubor prostředků `HomeController` pro by se jmenoval *Controllers.HomeController.fr.resx*. Volba použití tečky nebo konvence pojmenování cesty závisí na tom, jak chcete uspořádat soubory prostředků.

| Název prostředku | Pojmenování tečky nebo cesty |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Dot  |
| Zdroje/kontroléry/HomeController.fr.resx  | Cesta |
|    |     |

Soubory prostředků `@inject IViewLocalizer` používající v zobrazení razor postupujte podle podobného vzoru. Soubor prostředků pro zobrazení lze pojmenovat pomocí pojmenování tečky nebo pojmenování cesty. Soubory prostředků zobrazení holicí strojek napodobují cestu k jejich přidruženému souboru zobrazení. Za předpokladu, `ResourcesPath` že nastavíme na "Zdroje", může být francouzský soubor prostředků přidružený *k zobrazení Zobrazení/Domů/About.cshtml* některou z následujících možností:

* Zdroje/Zobrazení/Domů/About.fr.resx

* Zdroje/Views.Home.About.fr.resx

Pokud `ResourcesPath` tuto možnost nepoužijete, soubor *Resx* pro zobrazení bude umístěn ve stejné složce jako zobrazení.

### <a name="rootnamespaceattribute"></a>Atribut rootnamespace 

Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení. 

> [!WARNING]
> Tato situace může nastat, pokud název projektu není platný identifikátor .NET. Například `my-project-name.csproj` bude používat kořenový obor `my_project_name` `my-project-name` názvů a název sestavení vedoucí k této chybě. 

Pokud kořenový obor názvů sestavení se liší od názvu sestavení:

* Lokalizace nefunguje ve výchozím nastavení.
* Lokalizace se nezdaří z důvodu způsobu, jakým jsou vyhledávány prostředky v rámci sestavení. `RootNamespace`je hodnota v době sestavení, která není k dispozici pro vykonávající proces. 

Pokud `RootNamespace` se liší `AssemblyName`od , uveďte do *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Předchozí kód umožňuje úspěšné rozlišení souborů resx.

## <a name="culture-fallback-behavior"></a>Záložní chování jazykové verze

Při hledání prostředku se lokalizace zabývá "záložní kulturou". Počínaje požadovanou jazykovou verzi, pokud nebyl nalezen, vrátí se do nadřazené jazykové verze této jazykové verze. Jako stranou [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) vlastnost představuje nadřazenou jazykovou verzi. To obvykle (ale ne vždy) znamená odstranění národního signifier z ISO. Například dialekt španělštiny mluvený v Mexiku je "es-MX". Má mateřské "es"&mdash;španělština non-specifické pro všechny země.

Představte si, že váš web obdrží žádost o "Uvítací" prostředek pomocí jazykové verze "fr-CA". Lokalizační systém vyhledá následující prostředky v pořadí a vybere první shodu:

* *Welcome.fr-CA.resx*
* *Vítejte.fr.resx*
* *Welcome.resx* (pokud `NeutralResourcesLanguage` je "fr-CA")

Jako příklad pokud odeberete označení jazykové verze ".fr" a máte jazykovou verzi nastavenou na francouzštinu, výchozí soubor prostředků se čte a řetězce jsou lokalizovány. Správce prostředků určuje výchozí nebo záložní prostředek, pokud se nic nesetká s požadovanou jazykovou verzí. Pokud chcete pouze vrátit klíč při chybě prostředku pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.

### <a name="generate-resource-files-with-visual-studio"></a>Generování souborů prostředků pomocí sady Visual Studio

Pokud vytvoříte soubor prostředků v sadě Visual Studio bez jazykové verze v názvu souboru (například *Welcome.resx*), Visual Studio vytvoří třídu C# s vlastností pro každý řetězec. To obvykle není to, co chcete s ASP.NET Core. Obvykle nemáte výchozí soubor prostředků *Resx* (soubor *Resx* bez názvu jazykové verze). Doporučujeme vytvořit soubor *Resx* s názvem jazykové verze (například *Welcome.fr.resx*). Když vytvoříte soubor *Resx* s názvem jazykové verze, Visual Studio nebude generovat soubor třídy.

### <a name="add-other-cultures"></a>Přidání dalších kultur

Každá kombinace jazyka a jazykové verze (jiná než výchozí jazyk) vyžaduje jedinečný soubor prostředků. Soubory prostředků pro různé jazykové verze a národní prostředí vytvoříte vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-us**, **fr-ca**a **en-gb**). Tyto kódy ISO jsou umístěny mezi názvem souboru a příponou *.resx,* jako v *Welcome.es-MX.resx* (španělština / Mexiko).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementace strategie pro výběr jazyka/jazykové verze pro každý požadavek

### <a name="configure-localization"></a>Konfigurace lokalizace

Lokalizace je `Startup.ConfigureServices` konfigurována v metodě:

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* `AddLocalization`Přidá lokalizační služby do kontejneru služeb. Výše uvedený kód také nastaví cestu prostředků na "Zdroje".

* `AddViewLocalization`Přidá podporu pro lokalizované soubory zobrazení. V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení. Například "fr" v souboru *Index.fr.cshtml.*

* `AddDataAnnotationsLocalization`Přidává podporu pro `DataAnnotations` lokalizované ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.

### <a name="localization-middleware"></a>Middleware lokalizace

Aktuální jazyková verze na požadavek je nastavena v [middlewaru](xref:fundamentals/middleware/index)lokalizace . Middleware lokalizace je `Startup.Configure` povolena v metodě. Middleware lokalizace musí být nakonfigurován před jakýmkoli middlewarem, `app.UseMvcWithDefaultRoute()`který může zkontrolovat jazykovou verzi požadavku (například ).

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization`inicializuje `RequestLocalizationOptions` objekt. Na každém požadavku `RequestCultureProvider` `RequestLocalizationOptions` je uveden seznam v je uveden a první zprostředkovatel, který lze úspěšně určit jazykovou verzi požadavku se používá. Výchozí zprostředkovatelé pocházejí `RequestLocalizationOptions` z třídy:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Výchozí seznam přejde z nejkonkrétnějších na nejméně konkrétní. Dále v článku uvidíme, jak můžete změnit pořadí a dokonce přidat vlastní poskytovatele jazykové verze. Pokud žádný z zprostředkovatelů může určit `DefaultRequestCulture` jazykovou verzi požadavku, použije se.

### <a name="querystringrequestcultureprovider"></a>Zprostředkovatel QueryStringRequestCulture

Některé aplikace budou používat řetězec dotazu k nastavení [jazykové verze a jazykové verze uznané .](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx) Pro aplikace, které používají přístup záhlaví cookie nebo Přijmout jazyk, je přidání řetězce dotazu do adresy URL užitečné pro ladění a testování kódu. Ve výchozím `QueryStringRequestCultureProvider` nastavení je registrován jako první `RequestCultureProvider` zprostředkovatel lokalizace v seznamu. Předáte parametry `culture` řetězce `ui-culture`dotazu a . Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělštinu/Mexiko:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Pokud předáte pouze v jednom`culture` `ui-culture`ze dvou ( nebo ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí jedné, kterou jste předali. Například nastavení pouze jazykové verze `Culture` nastaví `UICulture`a :

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>Zprostředkovatel cookieRequestCulture

Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze pomocí souboru cookie jazykové verze ASP.NET jádra. Pomocí `MakeCookieValue` metody vytvořte soubor cookie.

Vrátí `CookieRequestCultureProvider` `DefaultCookieName` výchozí název souboru cookie používaný ke sledování informací o jazykové verzi upřednostňované uživatele. Výchozí název souboru cookie je `.AspNetCore.Culture`.

Formát souboru `c=%LANGCODE%|uic=%LANGCODE%`cookie `c` `Culture` je `uic` `UICulture`, kde je a je , například:

    c=en-UK|uic=en-US

Pokud zadáte pouze jeden z informací o jazykové verzi a jazykové verze uj.

### <a name="the-accept-language-http-header"></a>Hlavička HTTP v jazyce přijetí jazyka

Hlavička [Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) je ve většině prohlížečů nastavena a původně byla určena k určení jazyka uživatele. Toto nastavení označuje, co byl prohlížeč nastaven na odeslání nebo zděděné z podkladového operačního systému. Hlavička HTTP v jazyce přijetí z požadavku prohlížeče není neomylným způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči).](https://www.w3.org/International/questions/qa-lang-priorities.en.php) Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu jazykové verze.

### <a name="set-the-accept-language-http-header-in-ie"></a>Nastavení hlavičky HTTP v jazyce HTTP v jazyce IE

1. Na ikoně ozubeného kola klepněte na **Možnosti Internetu**.

2. Klepněte na **Jazyky**.

    ![Možnosti Internetu](localization/_static/lang.png)

3. Klepněte na **Nastavit jazykové předvolby**.

4. Klepněte **na Přidat jazyk**.

5. Přidejte jazyk.

6. Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a>Hlavička HTTP v jazyce Content-Language

Záhlaví entity [Obsah-Jazyk:](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language)

 - Používá se k popisu jazyka (jazyků) určených pro publikum.
 - Umožňuje uživateli rozlišovat podle vlastního preferovaného jazyka uživatele.

Hlavičky entit se používají v požadavcích HTTP i odpovědích.

Hlavičku `Content-Language` lze přidat nastavením `ApplyCurrentCultureToResponseHeaders`vlastnosti .

Přidání `Content-Language` záhlaví:

 - Umožňuje RequestLocalizationMiddleware nastavit `Content-Language` záhlaví s `CurrentUICulture`.
 - Eliminuje nutnost explicitně nastavit `Content-Language` hlavičku odpovědi.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a>Použití vlastního zprostředkovatele

Předpokládejme, že chcete, aby vaši zákazníci ukládat své jazyky a jazykové verze ve vašich databázích. Můžete napsat zprostředkovatele vyhledat tyto hodnoty pro uživatele. Následující kód ukazuje, jak přidat vlastního zprostředkovatele:

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

Slouží `RequestLocalizationOptions` k přidání nebo odebrání zprostředkovatelů lokalizace.

### <a name="set-the-culture-programmatically"></a>Programové nastavení kultury

Tato ukázka **Localization.StarterWeb** projektu na [GitHub](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture`. *Soubor Zobrazení/Sdílené/_SelectLanguagePartial.cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných kultur:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Soubor *Zobrazení/Sdílený/_SelectLanguagePartial.cshtml* je přidán `footer` do části souboru rozložení, takže bude k dispozici všem zobrazením:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Metoda `SetLanguage` nastaví soubor cookie jazykové verze.

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

Nelze připojit *_SelectLanguagePartial.cshtml* ukázkový kód pro tento projekt. Projekt **Localization.StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) má `RequestLocalizationOptions` kód pro tok razor částečné prostřednictvím kontejneru [vkládání závislostí.](dependency-injection.md)

## <a name="model-binding-route-data-and-query-strings"></a>Data vazby modelu a řetězce dotazů

Viz [Chování globalizace dat trasy vazby modelu a řetězců dotazu](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Podmínky globalizace a lokalizace

Proces lokalizace aplikace také vyžaduje základní znalosti relevantních znakových sad běžně používaných při vývoji moderního softwaru a pochopení problémů s nimi spojených. Přestože všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text pomocí různých čísel. Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.

[Lokalizovatelnost](/dotnet/standard/globalization-localization/localizability-review) je zprostředkující proces pro ověření, že globalizovaná aplikace je připravena k lokalizaci.

Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název `<languagecode2>-<country/regioncode2>`jazykové `<languagecode2>` verze je `<country/regioncode2>` , kde je kód jazyka a je kód subkultury. Například `es-CL` pro španělštinu `en-US` (Chile), pro angličtinu (Spojené státy) a `en-AU` pro angličtinu (Austrálie). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinace dvoupísmenného kódu jazykové verze ISO 639 s malou písmena přidruženou k jazyku a dvoupísmenného kódu subkultury ISO 3166 s velkými písmeny přidruženými k zemi nebo oblasti. Viz [Název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).

Internacionalizace je často zkrácena na "I18N". Zkratka má první a poslední písmeno a počet písmen mezi nimi, takže 18 znamená počet písmen mezi prvním "I" a posledním "N". Totéž platí pro globalizaci (G11N) a lokalizaci (L10N).

Podmínky:

* Globalizace (G11N): Proces vytváření aplikace podporuje různé jazyky a oblasti.
* Lokalizace (L10N): Proces přizpůsobení aplikace pro daný jazyk a oblast.
* Internacionalizace (I18N): Popisuje globalizaci i lokalizaci.
* Kultura: Je to jazyk a volitelně region.
* Neutrální jazyková verze: Jazyková verze, která má zadaný jazyk, ale ne oblast. (například "en", "es")
* Konkrétní jazyková verze: Jazyková verze, která má zadaný jazyk a oblast. (například "en US", "en-GB", "es-CL")
* Nadřazená jazyková verze: Neutrální jazyková verze, která obsahuje konkrétní jazykovou verzi. (například "en" je nadřazená jazyková verze "en US" a "en-GB")
* Národní prostředí: Národní prostředí je stejné jako jazyková verze.

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Localization.StarterWeb projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) použitý v článku.
* [Globalizace a lokalizace aplikací .NET](/dotnet/standard/globalization-localization/index)
* [Zdroje v souborech Resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Sada nástrojů aplikace Microsoft pro vícejazyčných aplikací](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalizace & obecných typů](http://hishambinateya.com/localization-and-generics)
