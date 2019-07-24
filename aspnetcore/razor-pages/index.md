---
title: Úvod do Razor Pages v ASP.NET Core
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 04/06/2019
uid: razor-pages/index
ms.openlocfilehash: 406e89c96ea63493091d0287077e244faee5f730
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308009"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Úvod do Razor Pages v ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)

Razor Pages je nový aspekt ASP.NET Core MVC, který usnadňuje a produktivnější vytváření kódu pro scénáře zaměřené na stránku.

Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Tento dokument představuje úvod do Razor Pages. Nejedná se o podrobný kurz. Pokud některé části obsahují příliš pokročilé, přečtěte si téma Začínáme [s Razor Pages](xref:tutorials/razor-pages/razor-pages-start). Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Vytvoření projektu Razor Pages

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Podrobné pokyny k vytvoření projektu Razor Pages najdete v tématu [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

Spusťte `dotnet new webapp` příkaz z příkazového řádku.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Spusťte `dotnet new razor` příkaz z příkazového řádku.

::: moniker-end

Otevřete vygenerovaný soubor *. csproj* z Visual Studio pro Mac.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-2.1"

Spusťte `dotnet new webapp` příkaz z příkazového řádku.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Spusťte `dotnet new razor` příkaz z příkazového řádku.

::: moniker-end

---

## <a name="razor-pages"></a>Razor Pages

Razor Pages je povolený v *Startup.cs*:

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Zvažte základní stránku:<a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Předchozí kód vypadá podobně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními. To znamená, že tato `@page` direktiva je odlišná. `@page`Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler. `@page`musí se jednat o první direktivu Razor na stránce. `@page`má vliv na chování jiných konstrukcí Razor.

Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech. Soubor *Pages/Index2. cshtml* :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Model stránky *stránky/Index2. cshtml. cs* :

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Podle konvence `PageModel` má soubor třídy stejný název jako soubor stránky Razor s *příponou. cs* . Například předchozí stránka Razor je *Pages/Index2. cshtml*. Soubor obsahující `PageModel` třídu má název Pages */Index2. cshtml. cs*.

Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů. Následující tabulka ukazuje cestu stránky Razor a adresu URL pro porovnání:

| Název souboru a cesta               | shodná adresa URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` Nebo `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` Nebo `/Store/Index` |

Poznámky:

* Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *stránky* .
* `Index`je výchozí stránka, když adresa URL neobsahuje stránku.

## <a name="write-a-basic-form"></a>Napsat základní formulář

Razor Pages je navržený tak, aby se při vytváření aplikace usnadnily běžné vzory používané s webovými prohlížeči. [Vázání modelů](xref:mvc/models/model-binding), [pomocníkům značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníkům, kteří *pracují pouze* s vlastnostmi definovanými ve třídě stránky Razor. Zvažte stránku, která pro `Contact` model implementuje základní formulář "kontaktujte nás":

Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Datový model:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Kontext databáze:

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Soubor zobrazení *Pages/Create. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Model stránky *Pages/Create. cshtml. cs* :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Podle konvence `PageModel` je třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.

`PageModel` Třída umožňuje oddělení logiky stránky od její prezentace. Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky. Toto oddělení umožňuje spravovat závislosti stránek pomocí [vkládání závislostí](xref:fundamentals/dependency-injection) a [testování částí](xref:test/razor-pages-tests) stránky.

Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle). Můžete přidat metody obslužné rutiny pro libovolný příkaz HTTP. Nejběžnější obslužné rutiny jsou:

* `OnGet`pro inicializaci stavu potřebného pro stránku. Ukázka [OnGet](#OnGet)
* `OnPost`pro zpracování odesílání formulářů.

Přípona `Async` názvu je volitelná, ale často se používá v konvenci pro asynchronní funkce. `OnPostAsync` Kód v předchozím příkladu vypadá podobně jako při normálním zápisu do kontroleru. Předchozí kód je typický pro Razor Pages. Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation)a výsledky akce, jsou sdílené.  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

Předchozí `OnPostAsync` metoda:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Základní tok `OnPostAsync`:

Kontrola chyb ověřování.

* V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.
* Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami. Ověřování na straně klienta je stejné jako tradiční aplikace ASP.NET Core MVC. V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy se neodeslaly na server.

Po úspěšném `OnPostAsync` zadání dat `RedirectToPage` zavolá metoda obslužné rutiny pomocnou metodu, `RedirectToPageResult`která vrátí instanci. `RedirectToPage`je nový výsledek akce, který `RedirectToAction` se podobá nebo `RedirectToRoute`, ale přizpůsobený pro stránky. V předchozí ukázce přesměruje na stránku kořenového indexu (`/Index`). `RedirectToPage`je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .

Když odeslaný formulář obsahuje chyby ověřování (které jsou předány serveru),`OnPostAsync` metoda obslužné rutiny `Page` volá pomocnou metodu. `Page`Vrátí instanci `PageResult`. Vrácení `Page` se podobá tomu, jak vrátí `View`akce v řadičích. `PageResult`je výchozí <!-- Review  --> návratový typ pro metodu obslužné rutiny Metoda obslužné rutiny, `void` která vrací vykreslení stránky.

`Customer` Vlastnost používá`[BindProperty]` atribut pro přihlášení k vazbě modelu.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor Pages ve výchozím nastavení vlastnosti BIND pouze s jinými než`GET` slovesy. Vazba na vlastnosti může snížit množství kódu, který musíte napsat. Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře (`<input asp-for="Customer.Name">`) a přijměte vstup.

[!INCLUDE[](~/includes/bind-get.md)]

Domovská stránka (*index. cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Přidružená `PageModel` třída (*index.cshtml.cs*):

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Soubor *index. cshtml* obsahuje následující kód pro vytvoření odkazu pro úpravy pro každý kontakt:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

[Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy. Odkaz obsahuje data směrování s ID kontaktu. Například, `http://localhost:5000/Edit/1`. `asp-area` Použijte atribut k určení oblasti. Další informace naleznete v tématu <xref:mvc/controllers/areas>.

Soubor *Pages/Edit. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

První řádek obsahuje `@page "{id:int}"` direktivu. Omezení`"{id:int}"` směrování instruuje stránku, aby přijímala požadavky na stránku, která `int` obsahuje data směrování. Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int`, modul runtime vrátí chybu HTTP 404 (Nenalezeno). Pokud chcete ID nastavit jako volitelné, `?` připojovat se k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Soubor *Pages/Edit. cshtml. cs* :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Soubor *index. cshtml* také obsahuje značky pro vytvoření tlačítka Odstranit pro kontaktování každého zákazníka:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Když je tlačítko Odstranit vykresleno ve formátu HTML, `formaction` obsahuje parametry pro:

* ID kontaktu zákazníka určené `asp-route-id` atributem
* `handler` Zadané`asp-page-handler` atributem.

Tady je příklad vygenerovaného tlačítka odstranit s ID `1`kontaktu zákazníka:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Když je vybráno tlačítko, pošle `POST` se na server požadavek na formulář. Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu. `OnPost[handler]Async`

Vzhledem k tomu `delete` , že `OnPostDeleteAsync`jev tomto příkladu, metoda obslužné rutiny se `POST` používá ke zpracování požadavku. `handler` Pokud je nastaven na jinou hodnotu, `remove`například, je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync`. `asp-page-handler`

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

`OnPostDeleteAsync` Metody:

* Akceptuje `id` z řetězce dotazu.
* Dotazuje databázi na kontaktování `FindAsync`zákazníka.
* Pokud se kontakt zákazníka najde, odebere se ze seznamu kontaktů zákazníka. Databáze je aktualizována.
* Volání `RedirectToPage` pro přesměrování na stránku kořenového indexu (`/Index`).

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-as-required"></a>Označit vlastnosti stránky jako povinné

Vlastnosti v `PageModel` lze dekorovat pomocí [požadovaného](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atributu:

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Další informace najdete v tématu [ověřování modelu](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet

`HEAD`žádosti umožňují načíst hlavičky pro konkrétní prostředek. Na rozdíl `GET` od `HEAD` požadavků požadavky nevrátí tělo odpovědi.

Obvykle je `HEAD` obslužná rutina vytvořena a volána pro požadavky: `OnHead` 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

V ASP.NET Core 2,1 nebo novější Razor Pages návrat k volání `OnGet` obslužné rutiny, pokud není definována žádná `OnHead` obslužná rutina. Toto chování je povoleno voláním [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Výchozí šablony generují `SetCompatibilityVersion` volání v ASP.NET Core 2,1 a 2,2. `SetCompatibilityVersion`efektivně nastaví možnost `AllowMappingHeadRequestsToGetHandler` Razor Pages na `true`.

Místo toho, abyste se rozhodli pro všechna `SetCompatibilityVersion`chování pomocí, se můžete výslovně vyjádřit  ke konkrétnímu chování. Následující kód výslovný v pro povolení `HEAD` mapování požadavků `OnGet` na obslužnou rutinu:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF a Razor Pages

Nemusíte psát žádný kód pro [ověřování](xref:security/anti-request-forgery)proti padělání. Generování a ověření tokenu antipadělání jsou automaticky zahrnuty do Razor Pages.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Použití rozložení, částečných, šablon a značek pomocníků s Razor Pages

Stránky fungují se všemi možnostmi modulu zobrazení Razor. Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*, *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních zobrazení Razor.

Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.

::: moniker range=">= aspnetcore-2.1"

Přidat [stránku rozložení](xref:mvc/views/layout) na *stránky/Shared/_Layout. cshtml*:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Přidat [stránku rozložení](xref:mvc/views/layout) na *stránky/_Layout. cshtml*:

::: moniker-end

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Rozložení](xref:mvc/views/layout):

* Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).
* Importuje struktury HTML, jako jsou JavaScript a StyleSheet.

Další informace najdete na [stránce rozložení](xref:mvc/views/layout) .

Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránce Pages/_ViewStart. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

::: moniker range=">= aspnetcore-2.1"

Rozložení se nachází na *stránkách nebo ve sdílené* složce. Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka. Rozložení na *stránkách nebo ve sdílené* složce se dá použít na libovolné stránce Razor ve složce *Pages* .

Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Rozložení je ve složce *stránky* . Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka. Rozložení ve složce *stránky* lze použít na libovolné stránce Razor ve složce Pages.

::: moniker-end

Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky. *Zobrazení/Shared* je vzor zobrazení MVC. Razor Pages mají spoléhat na hierarchii složek, nikoli na konvence cest.

Zobrazení hledání ze stránky Razor obsahuje složku *stránky* . Rozložení, šablony a částečně používané s řadiči MVC a konvenčními zobrazeními Razor *fungují pouze*.

Přidejte soubor *Pages/_ViewImports. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`je vysvětleno dále v tomto kurzu. Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .

<a name="namespace"></a>

V případě explicitního použití direktivynastránce:`@namespace`

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Direktiva nastaví obor názvů pro stránku. `@model` Direktiva nemusí zahrnovat obor názvů.

Pokud je `@namespace` direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje direktivu. `@namespace` Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.

Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` třída.

`@namespace`*funguje také se konvenčními zobrazeními Razor.*

Soubor zobrazení původní *stránky/vytvořit. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Aktualizovaný soubor zobrazení *stránky/vytvoření. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Projekt Razor Pages Starter](#rpvs17) obsahuje *stránku/_ValidationScriptsPartial. cshtml*, která se připojí k ověřování na straně klienta.

Další informace o částečných zobrazeních naleznete <xref:mvc/views/partial>v tématu.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generování adresy URL pro stránky

Stránka `Create` , která se zobrazuje dřív, `RedirectToPage`používá:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Aplikace má následující strukturu souborů nebo složek:

* */Pages*

  * *Index.cshtml*
  * */Customers*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit.* cshtml Pages přesměruje na *pages/index. cshtml* po úspěchu. Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce. Řetězec `/Index` lze použít ke generování identifikátorů URI na stránce *pages/index. cshtml* . Příklad:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` `/Index`(například). Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkce, které se zakódujeme na adresu URL. Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.

Generování adresy URL pro stránky podporuje relativní názvy. Následující tabulka ukazuje, která stránka indexu je vybrána s různými `RedirectToPage` parametry ze *stránky/zákazníci/vytvořit. cshtml*:

| RedirectToPage(x)| Page |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Stránky/rejstřík* |
| RedirectToPage("./Index"); | *Stránky/zákazníci/rejstřík* |
| RedirectToPage(".. /Index") | *Stránky/rejstřík* |
| RedirectToPage ("index")  | *Stránky/zákazníci/rejstřík* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` a`RedirectToPage("../Index")` jsou *relativní názvy*. Parametr je kombinován s cestou aktuální stránky k výpočtu názvu cílové stránky.  `RedirectToPage`  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou. Pokud k propojení mezi stránkami ve složce použijete relativní názvy, můžete tuto složku přejmenovat. Všechny odkazy pořád fungují (protože neobsahují název složky).

::: moniker range=">= aspnetcore-2.1"

Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Další informace naleznete v tématu <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>ViewData – atribut

Data je možné předat na stránku pomocí [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Vlastnosti na řadičích nebo modelech stránek Razor `[ViewData]` dekorované s jejich hodnotami uloženými a načtenými z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

V následujícím příkladu `AboutModel` `Title` obsahuje vlastnost dekorované pomocí `[ViewData]`. `Title` Vlastnost je nastavena na název stránky o produktu:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Na stránce o aplikaci získáte přístup `Title` k vlastnosti jako vlastnost modelu:

```cshtml
<h1>@Model.Title</h1>
```

V rozložení je název čten ze slovníku ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje vlastnost [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [řadiči](/dotnet/api/microsoft.aspnetcore.mvc.controller). Tato vlastnost ukládá data do jejich čtení. Metody `Keep` a`Peek` lze použít k prohlédnutí dat bez odstranění. `TempData`je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.

`[TempData]` Atribut je v ASP.NET Core 2,0 nový a podporuje se na řadičích a stránkách.

Následující kód nastaví hodnotu `Message` pomocí: `TempData`

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.

```cs
[TempData]
public string Message { get; set; }
```

Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata) .

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Více obslužných rutin na stránku

Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Formulář v předchozím příkladu obsahuje dvě tlačítka `FormActionTagHelper` pro odeslání, z nichž každá používá pro odeslání na jinou adresu URL. Atribut je doprovodný objekt k `asp-page`. `asp-page-handler` `asp-page-handler`generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou. `asp-page`není zadáno, protože ukázka odkazuje na aktuální stránku.

Model stránky:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Předchozí kód používá *pojmenované obslužné rutiny*. Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici). V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async. Když jsou rutiny *post* a *Async* odebrány, názvy obslužných `JoinListUC`rutin jsou `JoinList` a.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Pomocí předchozího kódu je cesta URL, na `OnPostJoinListAsync` `http://localhost:5000/Customers/CreateFATH?handler=JoinList`kterou se odesílá,. Cesta URL, na `OnPostJoinListUCAsync` kterou se `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`odesílá.

## <a name="custom-routes"></a>Vlastní trasy

Použijte tuto `@page` direktivu pro:

* Zadejte vlastní trasu na stránku. Například trasa na stránku o aplikaci `/Some/Other/Path` `@page "/Some/Other/Path"`může být nastavena na.
* Připojit segmenty k výchozí trase stránky. Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"`.
* Připojení parametrů k výchozí trase stránky. Například parametr `id`ID může být požadován pro stránku s `@page "{id}"`.

Je podporována kořenová relativní cesta, která je určena`~`vlnovkou () na začátku cesty. Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"`.

Řetězec `?handler=JoinList` dotazu v adrese URL můžete změnit na segment `/JoinList` směrování zadáním šablony `@page "{handler?}"`trasy.

Pokud se vám řetězec `?handler=JoinList` dotazu v adrese URL nelíbí, můžete změnit trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL. Trasu můžete přizpůsobit přidáním šablony trasy, která je uvedena v uvozovkách za `@page` direktivou.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Pomocí předchozího kódu je cesta URL, na `OnPostJoinListAsync` `http://localhost:5000/Customers/CreateFATH/JoinList`kterou se odesílá,. Cesta URL, na `OnPostJoinListUCAsync` kterou se `http://localhost:5000/Customers/CreateFATH/JoinListUC`odesílá.

`?` Následující`handler` údaj znamená, že parametr trasy je nepovinný.

## <a name="configuration-and-settings"></a>Konfigurace a nastavení

Pokud chcete konfigurovat pokročilé možnosti, použijte metodu `AddRazorPagesOptions` rozšíření v Tvůrci MVC:

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

V `RazorPagesOptions` současné době můžete použít k nastavení kořenového adresáře pro stránky nebo přidat konvence modelu aplikace pro stránky. Tímto způsobem v budoucnu povolíme lepší rozšíření.

Chcete-li předkompilovat zobrazení, přečtěte si téma [kompilace zobrazení Razor](xref:mvc/views/view-compilation) .

[Stažení nebo zobrazení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Další informace najdete v tématu [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start), který sestává z tohoto úvodního sestavení.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Zadejte, že Razor Pages jsou v kořenu obsahu.

Ve výchozím nastavení se Razor Pages rootem v adresáři */Pages* . Přidejte [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete tak, že Razor Pages jsou v kořenovém adresáři obsahu ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Zadejte, že Razor Pages jsou ve vlastním kořenovém adresáři

Přidejte [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete tak, že vaše Razor Pages jsou v aplikaci vlastním kořenovým adresářem (zadejte relativní cestu):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Další zdroje

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
