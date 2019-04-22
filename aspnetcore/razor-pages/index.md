---
title: Úvod do služby v ASP.NET Core Razor Pages
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 04/06/2019
uid: razor-pages/index
ms.openlocfilehash: e79ce1e2ddfc0e1a4d72e3f67f702d6eb938b8d3
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468692"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Úvod do služby v ASP.NET Core Razor Pages

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryanem Nowak](https://github.com/rynowak)

Stránky Razor je nový aspekt ASP.NET Core MVC, která provádí kódování zaměřené na stránce scénáře jednodušší a produktivnější.

Pokud hledáte kurz, který používá Model-View-Controller přístup, přečtěte si téma [Začínáme s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Tento dokument obsahuje úvod do stránky Razor. Není podrobný kurz. Pokud některé části příliš pokročilý najdete v tématu [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start). Přehled ASP.NET Core, najdete v článku [Úvod do ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Vytvoření projektu pro stránky Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Zobrazit [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) podrobné pokyny o tom, jak vytvořit projekt stránky Razor.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

Spustit `dotnet new webapp` z příkazového řádku.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Spustit `dotnet new razor` z příkazového řádku.

::: moniker-end

Otevřete vygenerovaný *.csproj* souboru ze sady Visual Studio pro Mac.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-2.1"

Spustit `dotnet new webapp` z příkazového řádku.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Spustit `dotnet new razor` z příkazového řádku.

::: moniker-end

---

## <a name="razor-pages"></a>Razor Pages

Stránky Razor je povolený v *Startup.cs*:

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Vezměte v úvahu základní stránka: <a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Předcházejícího kódu vypadá mnohem [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používané v aplikaci ASP.NET Core s kontrolerů a zobrazení. Co vlastně rozdíl je `@page` směrnice. `@page` Akce MVC – to znamená, že zpracovává požadavky přímo, bez nutnosti kontaktovat řadič vytvoří soubor. `@page` musí být první direktivy Razor na stránce. `@page` má vliv na chování jiných objektů, které Razor.

Podobná stránka, pomocí `PageModel` třídy, se zobrazí v následujících dvou souborech. *Pages/Index2.cshtml* souboru:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Pages/Index2.cshtml.cs* model stránky:

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Podle konvence `PageModel` soubor třídy má stejný název jako soubor stránky Razor s *.cs* připojí. Je třeba na předchozí stránku Razor *Pages/Index2.cshtml*. Soubor obsahující `PageModel` třída se nazývá *Pages/Index2.cshtml.cs*.

Přidružení cesty adresy URL na stránky jsou určeny na stránce umístění v systému souborů. Následující tabulka uvádí cesty stránky Razor a odpovídající adresy URL:

| Název souboru a cestu               | odpovídající adresy URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` Nebo `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` Nebo `/Store/Index` |

Poznámky:

* Modul runtime vyhledá soubory Razor Pages *stránky* složky ve výchozím nastavení.
* `Index` Když neobsahuje adresu URL stránky, je výchozí stránka.

## <a name="write-a-basic-form"></a>Zápis základní formulář

Stránky Razor je navržená tak, aby běžné vzory, které se používají u webových prohlížečů snadno se implementuje při sestavování aplikace. [Vazby modelu](xref:mvc/models/model-binding), [pomocných rutin značek](xref:mvc/views/tag-helpers/intro)a všechny pomocných rutin HTML *jenom pracovní* pomocí vlastnosti definované ve třídě stránky Razor. Vezměte v úvahu, který implementuje "kontaktujte nás" formuláře pro základní stránku `Contact` modelu:

Ukázky v tomto dokumentu `DbContext` se inicializuje [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) souboru.

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Datový model:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Kontext databáze:

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

*Pages/Create.cshtml* zobrazení souboru:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

*Pages/Create.cshtml.cs* model stránky:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Podle konvence `PageModel` třída se nazývá `<PageName>Model` a je v oboru názvů stejný jako stránka.

`PageModel` Třída umožňuje oddělení logiky stránku od jeho prezentaci. Definuje stránky obslužné rutiny pro požadavky odeslané na stránce a data použitá k vykreslení stránky. Toto oddělení vám umožní spravovat závislosti stránky prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection) a [Jednotkový test](xref:test/razor-pages-tests) stránky.

Na stránce `OnPostAsync` *metodu obslužné rutiny*, která se spouští `POST` požadavků (když uživatel odešle formulář). Můžete přidat metody obslužné rutiny pro libovolný příkaz protokolu HTTP. Nejčastěji používané rutiny jsou:

* `OnGet` Inicializace stavu potřebné pro stránku. [OnGet](#OnGet) vzorku.
* `OnPost` zpracování formulářů.

`Async` Přípona je volitelné, ale často používá konvence pro asynchronní funkce. `OnPostAsync` Kód v předchozím příkladu vypadá podobně jako byste normálně psaní v kontroleru. Předchozí kód je typický pro stránky Razor. Většina primitiv MVC, jako jsou [vazby modelu](xref:mvc/models/model-binding), [ověření](xref:mvc/models/validation), výsledky akcí se sdílejí.  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

Předchozí `OnPostAsync` metody:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Základní tok `OnPostAsync`:

Zkontrolován výskyt chyb ověření.

* Pokud nejsou žádné chyby, uložit data a přesměrování.
* Pokud chyby existují, zobrazit stránku znovu pomocí ověřovacích zpráv. Ověřování na straně klienta se shoduje s tradiční aplikace ASP.NET Core MVC. V mnoha případech se chyby ověření by být zjištěn v klientském počítači a nikdy odeslána na server.

Při zadání dat se úspěšně, `OnPostAsync` volání metody obslužné rutiny `RedirectToPage` Pomocná metoda vrátit instanci `RedirectToPageResult`. `RedirectToPage` je nový výsledek akce, podobně jako `RedirectToAction` nebo `RedirectToRoute`, ale přizpůsobené pro stránky. V předchozím příkladu, je přesměrován na indexovou stránku kořenové (`/Index`). `RedirectToPage` podrobně popisuje [generování adresy URL pro stránky](#url_gen) oddílu.

Když odeslané formuláře došlo k chybám ověřování, (, které se předá serveru),`OnPostAsync` volání metody obslužné rutiny `Page` Pomocná metoda. `Page` Vrátí instanci `PageResult`. Vrací `Page` je podobný jak vrátit akce v řadiče `View`. `PageResult` je výchozí nastavení <!-- Review  --> Návratový typ pro metodu obslužné rutiny. Metoda obslužné rutiny, která vrací `void` vykreslující danou stránku.

`Customer` Používá vlastnost `[BindProperty]` atribut můžete vyjádřit výslovný souhlas vazby modelu.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Stránky Razor, ve výchozím nastavení, vázat vlastnosti pouze s příkazy bez GET. Vytvoření vazby na vlastnosti může snížit množství kódu, který musíte napsat. Vazba snižuje kódu pomocí stejné vlastnosti k vykreslení pole formuláře (`<input asp-for="Customer.Name">`) a přijměte vstupu.

[!INCLUDE[](~/includes/bind-get.md)]

Na domovské stránce (*Index.cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Přidružené `PageModel` třídy (*Index.cshtml.cs*):

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

*Index.cshtml* soubor obsahuje následující kód k vytvoření odkaz pro úpravy pro každého kontaktu:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

[Ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použít `asp-route-{value}` atribut pro vygenerování odkazu na stránku pro úpravy. Odkaz obsahuje data trasy s kontaktu ID. Například, `http://localhost:5000/Edit/1`. Použití `asp-area` atribut oblast. Další informace naleznete v tématu <xref:mvc/controllers/areas>.

*Pages/Edit.cshtml* souboru:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

První řádek obsahuje `@page "{id:int}"` směrnice. Omezení směrování`"{id:int}"` říká stránce tak, aby přijímal požadavky na stránku, které obsahují `int` směrovat data. Pokud požadavek na stránku neobsahuje data trasy, který lze převést na `int`, modul runtime vrátí chybu HTTP 404 (Nenalezeno). Chcete-li nastavit ID volitelný, přidejte `?` pro dané omezení trasy:

 ```cshtml
@page "{id:int?}"
```

*Pages/Edit.cshtml.cs* souboru:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

*Index.cshtml* soubor obsahuje také značky, chcete-li vytvořit tlačítko pro odstranění pro každého zákazníka kontaktu:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Když tlačítko pro odstranění se vykreslí v HTML, jeho `formaction` obsahuje parametry pro:

* Aby se zákazník obrátil zadané podle ID `asp-route-id` atribut.
* `handler` Určená `asp-page-handler` atribut.

Tady je příklad tlačítko pro odstranění vykreslené zákazníků obraťte se na ID `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Při výběru tlačítka, formulář `POST` odeslán požadavek na server. Podle konvence je vybrán název metody obslužné rutiny založené na hodnotě `handler` parametr podle schéma `OnPost[handler]Async`.

Vzhledem k tomu, `handler` je `delete` v tomto příkladu `OnPostDeleteAsync` obslužná rutina se používá k procesu `POST` požadavku. Pokud `asp-page-handler` je nastavena na jinou hodnotu, jako například `remove`, metodu obslužné rutiny stránky s názvem `OnPostRemoveAsync` zaškrtnuto.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

`OnPostDeleteAsync` Metody:

* Přijímá `id` z řetězce dotazu.
* Dotazuje databázi pro kontakt zákazníka s `FindAsync`.
* Pokud je nalezen kontakt zákazníka, budou odebráni ze seznamu kontaktů zákazníka. Databáze se aktualizuje.
* Volání `RedirectToPage` přesměrovat na indexovou stránku root (`/Index`).

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-as-required"></a>Označení vlastností stránky podle potřeby

Vlastnosti `PageModel` může být doplněny pomocí [vyžaduje](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atribut:

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Další informace najdete v tématu [ověření modelu](xref:mvc/models/validation).

## <a name="manage-head-requests-with-the-onget-handler"></a>Spravovat požadavky HEAD s obslužnou rutinou OnGet

Požadavky HEAD umožňují načíst hlavičky pro konkrétní prostředek. Na rozdíl od požadavků GET HEAD požadavky nevracejte tělo odpovědi.

Obslužnou rutinu HEAD je obvykle vytvořen a volat pro požadavky HEAD: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

Pokud žádná obslužná rutina HEAD (`OnHead`) je definovaný, stránky Razor spadne zpět na volání obslužné rutiny GET stránky (`OnGet`) v ASP.NET Core 2.1 nebo novější. V ASP.NET Core 2.1 a 2.2 tomuto chování dochází u [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.Configure`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1);
```

Generovat výchozí šablony `SetCompatibilityVersion` volání v ASP.NET Core 2.1 a 2.2.

`SetCompatibilityVersion` Nastavuje možnost Razor Pages `AllowMappingHeadRequestsToGetHandler` k `true`.

Místo výběru všech 2.1 chování s `SetCompatibilityVersion`, je můžete explicitně vyjádřit výslovný souhlas pro konkrétní chování. Požádá o následující kód do Požadavky HEAD mapování na obslužnou rutinu GET.

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF a stránky Razor

Nemusíte psát kód pro [antiforgery ověření](xref:security/anti-request-forgery). Antiforgery generování tokenů a ověření jsou automaticky součástí stránky Razor.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Pomocí rozložení, částečných zobrazení, šablon a pomocných rutin značek se stránkami Razor

Stránky fungovat se všemi funkcemi zobrazovací modul Razor. Rozložení, částečných zobrazení, šablony, pomocných rutin značek *soubor _ViewStart.cshtml*, *_ViewImports.cshtml* pracovat stejným způsobem, jako je tomu u konvenční zobrazení syntaxe Razor.

Tato stránka umožňuje declutter s využitím některé z těchto možností.

::: moniker range=">= aspnetcore-2.1"

Přidat [stránku rozložení](xref:mvc/views/layout) k *Pages/Shared/_Layout.cshtml*:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Přidat [stránku rozložení](xref:mvc/views/layout) k *Pages/_Layout.cshtml*:

::: moniker-end

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Rozložení](xref:mvc/views/layout):

* Určuje rozložení jednotlivých stránek (Pokud na stránce výslovný rozložení).
* Importuje HTML struktury, jako jsou JavaScript a šablony stylů.

Zobrazit [stránku rozložení](xref:mvc/views/layout) Další informace.

[Rozložení](xref:mvc/views/layout#specifying-a-layout) je nastavena *Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

::: moniker range=">= aspnetcore-2.1"

Rozložení se *stránek/Shared* složky. Stránky vyhledejte jiným zobrazením (rozložení, šablony, částečných zobrazení) hierarchicky, spouští se ve stejné složce jako aktuální stránka. Rozložení v *stránek/Shared* složky je možné z jakékoli stránky Razor pod *stránky* složky.

Soubor rozložení by měl Přejít *stránek/Shared* složky.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Rozložení se *stránky* složky. Stránky vyhledejte jiným zobrazením (rozložení, šablony, částečných zobrazení) hierarchicky, spouští se ve stejné složce jako aktuální stránka. Rozložení v *stránky* složky je možné z jakékoli stránky Razor pod *stránky* složky.

::: moniker-end

Doporučujeme **není** vložit ho do rozložení *zobrazení/Shared* složky. *Zobrazení/Shared* je vzorek zobrazení MVC. Spoléhat na hierarchii složek, ne cestu konvence jsou určené pro stránky Razor.

Obsahuje zobrazení hledání ze stránky Razor *stránky* složky. Rozložení, šablony a částečných zobrazení, které používáte s konvenčním Razor zobrazení a kontrolery MVC *jenom pracovní*.

Přidat *Pages/_ViewImports.cshtml* souboru:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` je vysvětlen později v tomto kurzu. `@addTagHelper` – Direktiva přináší [integrovaných pomocných rutin značek](xref:mvc/views/tag-helpers/builtin-th/Index) na všechny stránky v *stránky* složky.

<a name="namespace"></a>

Když `@namespace` explicitně použít direktiva na stránce:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Direktiva Nastaví obor názvů pro stránku. `@model` – Direktiva není nutné, aby obsahoval obor názvů.

Když `@namespace` – direktiva je obsažen v *_ViewImports.cshtml*, určený obor názvů poskytuje předponu pro obor názvů generovaného na stránce, která importuje `@namespace` – direktiva. Zbytek vygenerovaného oboru názvů (přípona) je relativní cesta mezi složku obsahující tečkou oddělená *_ViewImports.cshtml* a složku, která obsahuje na stránce.

Například `PageModel` třídy *Pages/Customers/Edit.cshtml.cs* explicitně nastaví obor názvů:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

*Pages/_ViewImports.cshtml* souboru nastaví následující obor názvů:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Vygenerovaný obor názvů pro *Pages/Customers/Edit.cshtml* stránky Razor je stejný jako `PageModel` třídy.

`@namespace` *taky spolupracuje s konvenčním zobrazení syntaxe Razor.*

Původní *Pages/Create.cshtml* zobrazení souboru:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Aktualizovaný *Pages/Create.cshtml* zobrazení souboru:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Razor Pages starter projektu](#rpvs17) obsahuje *Pages/_ValidationScriptsPartial.cshtml*, který zachytí nahoru ověřování na straně klienta.

Další informace o částečné zobrazení, naleznete v tématu <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generování adresy URL pro stránky

`Create` Stránky uvedenému výše používá `RedirectToPage`:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Aplikace má následující strukturu souboru nebo složky:

* */ Stránky*

  * *Index.cshtml*
  * */ Zákazníků*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

*Pages/Customers/Create.cshtml* a *Pages/Customers/Edit.cshtml* stránek přesměrování na *Pages/Index.cshtml* po úspěšném provedení. Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce. Řetězec `/Index` slouží ke generování identifikátorů URI pro *Pages/Index.cshtml* stránky. Příklad:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Název stránky je cesta ke stránce z kořenového adresáře */stránky* složku včetně úvodní `/` (například `/Index`). Předchozí generace vzorky adresy URL umožňují rozšířené možnosti a funkční možnosti nad hardcoding adresy URL. Generování adresy URL používá [směrování](xref:mvc/controllers/routing) a může generovat a kódování parametry podle v cílové cestě bude definován takhle trasy.

Generování adresy URL pro stránky podporuje relativních názvů. V následující tabulce jsou uvedeny je vybrána na stránce, které Index s různými `RedirectToPage` parametry z *Pages/Customers/Create.cshtml*:

| RedirectToPage(x)| Stránka |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Stránky/indexu* |
| RedirectToPage("./Index"); | *Stránky/zákazníci/indexu* |
| RedirectToPage(".. / Index") | *Stránky/indexu* |
| RedirectToPage("Index")  | *Stránky/zákazníci/indexu* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")`, a `RedirectToPage("../Index")` jsou *relativní názvy*. `RedirectToPage` Parametr *kombinovat* s cestou k aktuální stránky pro výpočet název cílové stránky.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Relativní název propojení je užitečné, když vytváření webů s komplexní strukturou. Pokud používáte relativní názvy propojení mezi stránkami ve složce, můžete přejmenovat tuto složku. Všechny odkazy i nadále fungovat (vzhledem k tomu patří mezi ně neměli název složky).

::: moniker range=">= aspnetcore-2.1"

Přesměrování na stránku jinou [oblasti](xref:mvc/controllers/areas), určete oblast:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Další informace naleznete v tématu <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Atribut viewData

Data mohou být předána na stránku s [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Upravené vlastnosti v kontrolerech a modelech stránky Razor pomocí `[ViewData]` hodnoty uložené a načíst z [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

V následujícím příkladu `AboutModel` obsahuje `Title` vlastnost upravené pomocí `[ViewData]`. `Title` Je nastavena na název stránky o:

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

Na stránce o přístup `Title` vlastnost jako vlastnost modelu:

```cshtml
<h1>@Model.Title</h1>
```

Název je v rozložení pro čtení ze slovníku ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

## <a name="tempdata"></a>TempData

Zpřístupňuje ASP.NET Core [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) vlastnosti [řadič](/dotnet/api/microsoft.aspnetcore.mvc.controller). Tato vlastnost ukládá data, dokud je pro čtení. `Keep` a `Peek` metody můžete použít k prozkoumání dat bez potřeby jejich odstranění. `TempData` je užitečné pro přesměrování, když je potřeba data pro více než jeden požadavek.

`[TempData]` Atribut je nového v ASP.NET Core 2.0 a je podporován v řadiče a stránky.

Následující kód nastaví hodnotu `Message` pomocí `TempData`:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Následující kód v *Pages/Customers/Index.cshtml* souboru zobrazí hodnotu `Message` pomocí `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

*Pages/Customers/Index.cshtml.cs* modelu stránka se vztahuje `[TempData]` atribut `Message` vlastnost.

```cs
[TempData]
public string Message { get; set; }
```

Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata) .

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Na stránce více obslužných rutin

Na následující stránce generuje kód pro dvě stránky obslužné rutiny `asp-page-handler` pomocné rutiny značky:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Formuláře v předchozím příkladu má dva odeslání tlačítka, každý použití `FormActionTagHelper` pro odeslání do jinou adresu URL. `asp-page-handler` Atribut, který je k `asp-page`. `asp-page-handler` generuje adresy URL, které odesílají na jednotlivé metody obslužné rutiny, které jsou definované na stránce. `asp-page` není zadaný, protože vzorku je odkazování na aktuální stránce.

Model stránky:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Předchozí kód používá *s názvem metody obslužné rutiny*. Metody s názvem obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (pokud existuje). V předchozím příkladu jsou metody stránky OnPost**JoinList**Async a OnPost**JoinListUC**asynchronní. S *OnPost* a *asynchronní* odebrat, jsou názvy obslužných rutin `JoinList` a `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Použití předcházející kódu, cesty URL, která odešle `OnPostJoinListAsync` je `http://localhost:5000/Customers/CreateFATH?handler=JoinList`. Cesta URL, která odešle `OnPostJoinListUCAsync` je `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Vlastní trasy

Použití `@page` direktivu do:

* Zadejte vlastní trasu na stránku. Například lze nastavit trasy, která má na stránce o `/Some/Other/Path` s `@page "/Some/Other/Path"`.
* Připojte segmenty na stránce výchozí trasu. Například můžete přidat segmentu "item" výchozí trasu na stránce s `@page "item"`.
* Na stránce výchozí trasu připojení parametrů. Například pomocí parametru ID `id`, může být třeba stránka s `@page "{id}"`.

Relativní kořenové cesty určené tildou (`~`) se podporuje na začátku cesty. Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"`.

Můžete změnit řetězec dotazu `?handler=JoinList` v adrese URL trasy segmentu `/JoinList` zadáním šablonu trasy `@page "{handler?}"`.

Pokud vám nevyhovují řetězec dotazu `?handler=JoinList` v adrese URL, můžete změnit trasy, které chcete změnit název obslužné rutiny na část cesty adresy URL. Trasy můžete přizpůsobit tak, že přidáte šablonu trasy, který je umístěn do dvojitých uvozovek po `@page` směrnice.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Použití předcházející kódu, cesty URL, která odešle `OnPostJoinListAsync` je `http://localhost:5000/Customers/CreateFATH/JoinList`. Cesta URL, která odešle `OnPostJoinListUCAsync` je `http://localhost:5000/Customers/CreateFATH/JoinListUC`.

`?` Následující `handler` znamená, že je parametr trasy nepovinný.

## <a name="configuration-and-settings"></a>Konfigurace a nastavení

Rozšířené možnosti nakonfigurujete, použijte metodu rozšíření `AddRazorPagesOptions` na tvůrce MVC:

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Nyní můžete použít `RazorPagesOptions` nastavit kořenový adresář pro stránky, nebo přidat aplikaci modelu konvence pro stránky. Jsme budou moci další rozšíření takto v budoucnu.

Předkompilace zobrazení, najdete v článku [kompilace zobrazení Razor](xref:mvc/views/view-compilation) .

[Stažení nebo zobrazení vzorového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/index/sample).

Zobrazit [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start), které navazuje na tento úvod.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Určete, že pro stránky Razor se v kořenovém adresáři obsahu

Ve výchozím nastavení, je integrován Razor Pages */stránky* adresáře. Přidat [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) k [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) k určení, že vaše stránky Razor se v kořenovém adresáři obsahu ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Určit, že Razor Pages na vlastní kořenový adresář

Přidat [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) k [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) k určení, že jsou Razor Pages na vlastní kořenovým adresářem v aplikaci (zadejte relativní cestu):

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
