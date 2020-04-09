---
title: Úvod do Žiletky stránky v ASP.NET Core
author: Rick-Anderson
description: Zjistěte, jak Razor Pages v ASP.NET Core usnadňuje a ztráží scénáře zaměřené na stránky než při používání MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 42ffb0d4d2e49663dd53ffeee5d9fa2a931ee5b7
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667578"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Úvod do Žiletky stránky v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)

Razor Pages může usnadnit a zefektivnit scénáře zaměřené na stránky než použití ovladačů a zobrazení.

Pokud hledáte kurz, který používá model-View-Controller přístup, najdete [v tématu Začínáme s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Tento dokument obsahuje úvod do Razor Pages. Není to krok za krokem tutorial. Pokud vám některé oddíly přijdou příliš pokročilé, přečtěte si část [Začínáme s razor pages](xref:tutorials/razor-pages/razor-pages-start). Přehled ASP.NET core najdete v [článku Úvod do ASP.NET jádra](xref:index).

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Vytvoření projektu Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Podrobné pokyny k vytvoření projektu Razor Pages najdete v tématu [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) Pages.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte `dotnet new webapp` z příkazového řádku.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Spusťte `dotnet new webapp` z příkazového řádku.

Otevřete vygenerovaný soubor *.csproj* z Visual Studia for Mac.

---

## <a name="razor-pages"></a>Razor Pages

Razor Stránky je povolena v *Startup.cs*:

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Vezměme si základní stránku:<a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Předchozí kód vypadá hodně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používané v aplikaci ASP.NET Core s řadiči a zobrazeními. To, čím se [`@page`](xref:mvc/views/razor#page) liší, je směrnice. `@page`vytvoří soubor do akce MVC – což znamená, že zpracovává požadavky přímo, aniž by procházel řadičem. `@page`musí být první direktivou Razor na stránce. `@page`ovlivňuje chování jiných [konstrukce Razor.](xref:mvc/views/razor) Názvy souborů Razor Pages mají příponu *.cshtml.*

Podobná stránka pomocí `PageModel` třídy je zobrazena v následujících dvou souborech. Soubor *Pages/Index2.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Pages/Index2.cshtml.cs* model stránky:

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Podle konvence `PageModel` má soubor třídy stejný název jako soubor Razor Page s připojeným *souborem .cs.* Například předchozí stránka Razor je *Pages/Index2.cshtml*. Soubor obsahující třídu `PageModel` se nazývá *Pages/Index2.cshtml.cs*.

Přidružení cest adres URL ke stránkám jsou určena umístěním stránky v systému souborů. V následující tabulce je uvedena cesta Razor Page a odpovídající adresa URL:

| Název souboru a cesta               | odpovídající adresa URL |
| ----------------- | ------------ |
| */Stránky/Index.cshtml* | `/` nebo `/Index` |
| */Stránky/Kontakt.cshtml* | `/Contact` |
| */Stránky/Obchod/Kontakt.cshtml* | `/Store/Contact` |
| */Stránky/Obchod/Index.cshtml* | `/Store` nebo `/Store/Index` |

Poznámky:

* Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *Stránky.*
* `Index`je výchozí stránka, pokud adresa URL stránku neobsahuje.

## <a name="write-a-basic-form"></a>Napsat základní formulář

Razor Pages je navržen tak, aby běžné vzory používané s webovými prohlížeči snadno implementovat při vytváření aplikace. [Vazby modelu](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro)a HTML pomocníky *všechny pracovat s* vlastnostmi definovanými ve třídě Razor Page. Vezměme si stránku, která implementuje `Contact` základní "kontaktujte nás" formulář pro model:

Pro ukázky v tomto `DbContext` dokumentu je inicializován v [souboru Startup.cs.](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24)

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Datový model:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Kontext db:

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Soubor zobrazení *Pages/Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Model stránky *Pages/Create.cshtml.cs:*

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Podle konvence `PageModel` je třída `<PageName>Model` volána a je ve stejném oboru názvů jako stránka.

Třída `PageModel` umožňuje oddělení logiky stránky od jeho prezentace. Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky. Toto oddělení umožňuje:

* Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).
* [Testování jednotek](xref:test/razor-pages-tests)

Stránka má `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` požadavky (když uživatel zaúčtuje formulář). Lze přidat metody obslužné rutiny pro libovolné sloveso HTTP. Nejběžnější obslužné rutiny jsou:

* `OnGet` pro inicializaci stavu potřebného pro stránku. V předchozím kódu `OnGet` metoda zobrazí *CreateModel.cshtml* Razor Page.
* `OnPost` pro zpracování odesílání formulářů.

Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce. Předchozí kód je typický pro Razor Pages.

Pokud jste obeznámeni s ASP.NET aplikacemi pomocí ovladačů a zobrazení:

* Kód `OnPostAsync` v předchozím příkladu vypadá podobně jako typický kód řadiče.
* Většina primitiv MVC, jako je [vazba modelu](xref:mvc/models/model-binding), [ověření](xref:mvc/models/validation)a výsledky akce, funguje stejně s řadiči a razor pages. 

Předchozí `OnPostAsync` metoda:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Základní tok `OnPostAsync`:

Zkontrolujte chyby ověření.

* Pokud nejsou žádné chyby, uložte data a přesměrovat.
* Pokud dojde k chybám, zobrazte stránku znovu se zprávami ověření. V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.

Soubor zobrazení *Pages/Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Vykreslený HTML ze *stránek/Create.cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

V předchozím kódu zaúčtování formuláře:

* S platnými údaji:

  * Metoda `OnPostAsync` obslužné rutiny volá pomocnou <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodu. `RedirectToPage` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Je výsledek akce.
    * Je podobný `RedirectToAction` `RedirectToRoute` nebo (používá se v řadičích a zobrazeních).
    * Je přizpůsoben pro stránky. V předchozím vzorku přesměruje na kořenovou stránku indexu (`/Index`). `RedirectToPage`je podrobně popsána v části [Generování adres URL pro stránky.](#url_gen)

* S chybami ověření, které jsou předány serveru:

  * Metoda `OnPostAsync` obslužné rutiny volá pomocnou <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodu. `Page` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Vrácení `Page` je podobné jako jak se `View`vrátí akce v řadičích . `PageResult`je výchozí návratový typ metody obslužné rutiny. Metoda obslužné rutiny, která vrátí `void` vykreslí stránku.
  * V předchozím příkladu zaúčtování formuláře bez hodnoty výsledky [v ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) vrácení false. V této ukázce nejsou na straně klienta zobrazeny žádné chyby ověření. Předání chyby ověření je popsáno dále v tomto dokumentu.

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* S chybami ověření zjištěnými ověřením na straně klienta:

  * Data **nejsou** zaúčtována na server.
  * Ověření na straně klienta je vysvětleno dále v tomto dokumentu.

Vlastnost `Customer` používá [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atribut k přihlášení k vazby modelu:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`by **neměl** být používán na modely obsahující vlastnosti, které by neměly být změněny klientem. Další informace naleznete v tématu [Overpost](xref:data/ef-rp/crud#overposting).

Razor Pages ve výchozím nastavení vázají vlastnosti pouze s neslovesy.`GET` Vazba na vlastnosti odebere potřebu psaní kódu pro převod dat HTTP na typ modelu. Vazba snižuje kód pomocí stejné vlastnosti`<input asp-for="Customer.Name">`k vykreslení polí formuláře ( ) a přijetí vstupu.

[!INCLUDE[](~/includes/bind-get.md)]

Kontrola souboru zobrazení *Pages/Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* V předchozím kódu sváže [pomocný](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` prvek vstupní `<input>` značky `Customer.Name` element HTML s výrazem modelu.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)zpřístupní pomocné spoje značek.

### <a name="the-home-page"></a>Domovská stránka

*Index.cshtml* je domovská stránka:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

Přidružená `PageModel` třída (*Index.cshtml.cs*):

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Soubor *Index.cshtml* obsahuje následující značky:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

Pomocník `<a /a>` [značky kotvy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použil `asp-route-{value}` atribut ke generování odkazu na stránku Úpravy. Spojení obsahuje data trasy s ID kontaktu. Například, `https://localhost:5001/Edit/1`. [Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.

Soubor *Index.cshtml* obsahuje značky pro vytvoření tlačítka pro odstranění pro každý kontakt zákazníka:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

Vykreslený KÓD HTML:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Když je tlačítko delete vykresleno v HTML, jeho [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) obsahuje parametry pro:

* ID kontaktu zákazníka určené `asp-route-id` atributem.
* . `handler`určené atributem. `asp-page-handler`

Je-li tlačítko vybráno, je na server odeslán požadavek na formulář. `POST` Podle konvence je název metody obslužné rutiny vybrán na základě hodnoty parametru `handler` podle schématu `OnPost[handler]Async`.

Vzhledem `handler` `delete` k tomu, `OnPostDeleteAsync` že je v tomto `POST` příkladu, metoda obslužné rutiny se používá ke zpracování požadavku. Pokud `asp-page-handler` je nastavena na jinou `remove`hodnotu, například `OnPostRemoveAsync` , je vybrána metoda obslužné rutiny s názvem.

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

Metoda: `OnPostDeleteAsync`

* Získá `id` z řetězce dotazu.
* Dotazuje databázi pro `FindAsync`kontakt zákazníka s .
* Pokud je kontakt zákazníka nalezen, je odebrán a databáze je aktualizována.
* Volání <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> k přesměrování na kořenovou`/Index`stránku indexu ( ).

### <a name="the-editcshtml-file"></a>Soubor Edit.cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

První řádek obsahuje `@page "{id:int}"` směrnice. Omezení`"{id:int}"` směrování informuje stránku o přijetí požadavků `int` na stránku, která obsahuje data trasy. Pokud požadavek na stránku neobsahuje data trasy, která `int`lze převést na , vrátí za běhu chyba HTTP 404 (nebyl nalezen). Chcete-li, aby id `?` volitelné, připojit k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Soubor *Edit.cshtml.cs:*

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Ověřování

Ověřovací pravidla:

* Jsou deklarativně zadány ve třídě modelu.
* Jsou vynuceny všude v aplikaci.

Obor <xref:System.ComponentModel.DataAnnotations> názvů poskytuje sadu předdefinovaných ověřovacích atributů, které jsou deklarativně použity na třídu nebo vlastnost. DataAnnotations také obsahuje atributy [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) formátování, jako je to, které pomáhají s formátováním a neposkytují žádné ověření.

Vezměme `Customer` si model:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Pomocí následujícího souboru zobrazení *Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Předcházející kód:

* Zahrnuje skripty pro ověření jQuery a jQuery.
* Pomocí `<div />` `<span />` [pomocníků a značek](xref:mvc/views/tag-helpers/intro) povolte:

  * Ověření na straně klienta.
  * Vykreslování chyb ověření.

* Generuje následující HTML:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Zaúčtováním formuláře Vytvořit bez hodnoty názvu se zobrazí chybová zpráva "Pole Název je povinné". na formuláři. Pokud je v klientovi povolen JavaScript, prohlížeč zobrazí chybu bez odeslání na server.

Atribut `[StringLength(10)]` generuje `data-val-length-max="10"` na vykreslenéhtml. `data-val-length-max`zabrání prohlížečům v zadávání více, než je zadaná maximální délka. Pokud nástroj, jako je [Šumař](https://www.telerik.com/fiddler) se používá k úpravě a přehrání post:

* S názvem delším než 10.
* Chybová zpráva "Název pole musí být řetězec s maximální délkou 10." je vrácena.

Zvažte `Movie` následující model:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atributy ověření určují chování, které se má vynutit u vlastností modelu, na které se aplikují:

* `Required` Atributy `MinimumLength` a označují, že vlastnost musí mít hodnotu, ale nic nebrání uživateli v zadání prázdného místa, aby bylo možné vyhovět tomuto ověření.
* Atribut `RegularExpression` se používá k omezení, jaké znaky lze zadat. V předchozím kódu "Žánr":

  * Musí používat pouze písmena.
  * První písmeno musí být velkými písmeny. Prázdné znaky, čísla a speciální znaky nejsou povoleny.

* "Hodnocení": `RegularExpression`

  * Vyžaduje, aby první znak byl velké písmeno.
  * Umožňuje speciální znaky a čísla v následujících mezerách. "PG-13" je platný pro hodnocení, ale selže pro "Žánr".

* Atribut `Range` omezuje hodnotu v konkrétním rozsahu.
* Atribut `StringLength` nastaví maximální délku vlastnosti řetězce a volitelně jeho minimální délku.
* Typy `decimal`hodnot (například `float` `DateTime`, `int`, ) jsou ze své `[Required]` podstaty povinné a nepotřebují atribut.

Na stránce Vytvořit `Movie` pro model se zobrazí chyby s neplatnými hodnotami:

![Formulář zobrazení filmu s více chybami ověření na straně klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

Další informace naleznete v tématu:

* [Přidání ověření do aplikace Film](xref:tutorials/razor-pages/validation)
* [Ověření modelu v ASP.NET jádra](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Zpracování požadavků HEAD s záložní obslužnou rutinou OnGet

`HEAD`požadavky umožňují načítání záhlaví pro konkrétní prostředek. Na `GET` rozdíl `HEAD` od požadavků požadavky nevracejí tělo odpovědi.

Obvykle je `OnHead` vytvořena obslužná rutina a volána pro `HEAD` požadavky:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages přejde `OnGet` zpět na `OnHead` volání obslužné rutiny, pokud není definována žádná obslužná rutina.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF a Razor stránky

Razor Stránky jsou [chráněny antiforgery validace](xref:security/anti-request-forgery). [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vloží tokeny antiforgery do prvků formuláře HTML.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Použití rozložení, částečných částek, šablon a pomocných spoje značek se stránkami Razor

Stránky pracují se všemi možnostmi modulu Razor View. Rozložení, částečné součásti, šablony, tag helpery, *_ViewStart.cshtml*a *_ViewImports.cshtml* pracovat stejným způsobem, jako to dělají pro konvenční zobrazení Razor.

Pojďme declutter této stránce s využitím některých z těchto schopností.

Přidání [stránky rozložení](xref:mvc/views/layout) do *stránky/Shared/_Layout.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

[Rozložení](xref:mvc/views/layout):

* Určuje rozložení každé stránky (pokud se stránka neodhlásí z rozložení).
* Importuje struktury HTML, jako je JavaScript a styly.
* Obsah razor stránky jsou vykresleny, kde `@RenderBody()` je volána.

Další informace naleznete na [stránce rozložení](xref:mvc/views/layout).

Vlastnost [Layout](xref:mvc/views/layout#specifying-a-layout) je nastavena v *pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Rozložení je ve složce *Stránky/Sdílené.* Stránky vyhledává jiná zobrazení (rozložení, šablony, částečné části) hierarchicky, počínaje stejnou složkou jako aktuální stránka. Rozložení ve složce *Stránky/Sdílené* lze použít z libovolné stránky Razor ve složce *Stránky.*

Soubor rozložení by měl být ve složce *Stránky/Sdílené.*

Doporučujeme **not** neumisužuje soubor rozložení do složky *Zobrazení nebo Sdílené.* *Zobrazení/Sdílené* je vzor zobrazení MVC. Razor Stránky jsou určeny k spoléhání se na hierarchii složek, nikoli na konvence cest.

Zobrazení hledání ze stránky Razor obsahuje složku *Stránky.* Rozložení, šablony a částečné použití s mvc řadiče a konvenční razor zobrazení *prostě funguje*.

Přidejte soubor *Pages/_ViewImports.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`je vysvětleno dále v tutoriálu. Směrnice `@addTagHelper` přináší [integrované pomocné názvy značek](xref:mvc/views/tag-helpers/builtin-th/Index) na všechny stránky ve složce *Stránky.*

<a name="namespace"></a>

Směrnice `@namespace` nastavená na stránce:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Směrnice `@namespace` nastaví obor názvů pro stránku. Směrnice `@model` nemusí obsahovat obor názvů.

Pokud `@namespace` je směrnice obsažena v *souboru _ViewImports.cshtml*, zadaný obor názvů poskytuje předponu `@namespace` pro generovaný obor názvů na stránce, která importuje direktivu. Zbytek generovaného oboru názvů (přípona) je relativní cesta oddělená tečkami mezi složkou obsahující *_ViewImports.cshtml* a složkou obsahující stránku.

Například `PageModel` třída *Pages/Customers/Edit.cshtml.cs* explicitně nastaví obor názvů:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Soubor *Pages/_ViewImports.cshtml* nastavuje následující obor názvů:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Generovaný obor názvů pro *stránku Pages/Customers/Edit.cshtml* `PageModel` Razor Page je stejný jako třída.

`@namespace`*také pracuje s konvenčními pohledy na Břitva.*

Zvažte soubor zobrazení *Pages/Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Aktualizovaný soubor *zobrazení Pages/Create.cshtml* s *souborem _ViewImports.cshtml* a předchozím souborem rozložení:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

V předchozím kódu *_ViewImports.cshtml* importoval obor názvů a pomocné názvy značek. Soubor rozvržení importoval soubory JavaScriptu.

[Počáteční projekt Razor Pages](#rpvs17) obsahuje *pages/_ValidationScriptsPartial.cshtml*, který zavěsí ověření na straně klienta.

Další informace o částečných <xref:mvc/views/partial>zobrazeních naleznete v tématu .

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generování adres URL pro stránky

Stránka, zobrazená `Create` dříve, používá `RedirectToPage`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Aplikace má následující strukturu souborů a složek:

* */Stránky*

  * *Soubor Index.cshtml*
  * *Ochrana osobních údajů.cshtml*
  * */Zákazníci*

    * *Vytvořit.cshtml*
    * *Upravit.cshtml*
    * *Soubor Index.cshtml*

Stránky *Stránky/Zákazníci/Create.cshtml* a *Stránky/Zákazníci/Edit.cshtml* se po úspěchu přesměrují na *Stránky/Zákazníky/Index.cshtml.* Řetězec `./Index` je relativní název stránky používaný pro přístup k předchozí stránce. Používá se ke generování adres URL na stránce *Stránky/Zákazníci/Index.cshtml.* Příklad:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Absolutní název `/Index` stránky se používá ke generování adres URL na stránce *Pages/Index.cshtml.* Příklad:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Název stránky je cesta ke stránce ze složky root `/` */Pages* `/Index`včetně prokladu (například). Předchozí ukázky generování adres URL nabízejí rozšířené možnosti a funkční funkce oproti pevnému kódování adresy URL. Generování adresy URL používá [směrování](xref:mvc/controllers/routing) a může generovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.

Generování adres URL pro stránky podporuje relativní názvy. V následující tabulce je uvedeno, `RedirectToPage` která stránka Rejstřík je vybrána pomocí různých parametrů v *části Stránky/Zákazníci/Create.cshtml*.

| PřesměrovatToPage(x)| stránka |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Stránky/rejstřík* |
| RedirectToPage("./Index"); | *Stránky/Zákazníci/Index* |
| RedirectToPage(".. /Index") | *Stránky/rejstřík* |
| RedirectToPage("Index")  | *Stránky/Zákazníci/Index* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")`a `RedirectToPage("../Index")` jsou *relativní názvy*. Parametr `RedirectToPage` je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.

Relativní název propojení je užitečné při vytváření lokalit se složitou strukturou. Při použití relativních názvů k propojení stránek ve složce:

* Přejmenování složky nepřeruší relativní odkazy.
* Odkazy nejsou přerušeny, protože neobsahují název složky.

Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Další informace naleznete v tématech <xref:mvc/controllers/areas> a <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Atribut ViewData

Data mohou být předána <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>na stránku s . Vlastnosti `[ViewData]` s atributem mají své <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>hodnoty uložené a načtené z .

V následujícím příkladu `AboutModel` použije `[ViewData]` atribut vlastnosti: `Title`

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

Na stránce O aplikaci přistupujte k vlastnosti `Title` jako k vlastnosti modelu:

```cshtml
<h1>@Model.Title</h1>
```

V rozložení se název čte ze slovníku ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Tato vlastnost ukládá data, dokud se číst. Metody <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> a lze použít ke kontrole dat bez odstranění. `TempData`je užitečné pro přesměrování, když jsou data potřebná pro více než jeden požadavek.

Následující kód nastaví `Message` hodnotu použití `TempData`:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Následující značky v souboru *Pages/Customers/Index.cshtml* `Message` zobrazují `TempData`hodnotu použití .

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model stránky *Pages/Customers/Index.cshtml.cs* použije `[TempData]` atribut `Message` na vlastnost.

```csharp
[TempData]
public string Message { get; set; }
```

Další informace naleznete v tématu [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Více obslužných rutin na stránku

Následující stránka generuje značky pro dvě `asp-page-handler` obslužné rutiny pomocí pomocníka pro označení:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Formulář v předchozím příkladu má dvě tlačítka `FormActionTagHelper` pro odeslání, z nichž každé používá k odeslání na jinou adresu URL. Atribut `asp-page-handler` je společníkem `asp-page`aplikace . `asp-page-handler`generuje adresy URL, které se odesílají ke každé metodě obslužné rutiny definované stránkou. `asp-page`není zadán, protože ukázka odkazuje na aktuální stránku.

Model stránky:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Předchozí kód používá *metody pojmenované obslužné rutiny*. Pojmenované metody obslužné rutiny `On<HTTP Verb>` jsou `Async` vytvořeny tak, že text v názvu za a před (pokud je k dispozici). V předchozím příkladu jsou metody stránky OnPost**JoinList**Async a OnPost**JoinListUC**Async. Při odebrání *onpost* a *async* `JoinList` jsou `JoinListUC`názvy obslužných rutin a .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Pomocí předchozího kódu je `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList`cesta url, která se odešle, . Cesta url, do `OnPostJoinListUCAsync` které `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`se odešle, je .

## <a name="custom-routes"></a>Vlastní trasy

Pomocí `@page` směrnice můžete:

* Zadejte vlastní trasu ke stránce. Například trasu na stránku Informace `/Some/Other/Path` lze `@page "/Some/Other/Path"`nastavit pomocí .
* Připojujte segmenty k výchozí trase stránky. Segment "položka" lze například přidat k výchozí trase `@page "item"`stránky s .
* Připojujte parametry k výchozí trase stránky. Například parametr ID `id`, může být vyžadován `@page "{id}"`pro stránku s .

Je podporována cesta relativní ke`~`kořenu určená vlnovkou ( ) na začátku cesty. Například `@page "~/Some/Other/Path"` je stejný `@page "/Some/Other/Path"`jako .

Pokud se vám nelíbí řetězec `?handler=JoinList` dotazu v adrese URL, změňte trasu tak, aby název obslužné rutiny v části cesty adresy URL. Trasu lze přizpůsobit přidáním šablony trasy uzavřené v `@page` uvozovkách za direktivu.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Pomocí předchozího kódu je `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList`cesta url, která se odešle, . Cesta url, do `OnPostJoinListUCAsync` které `https://localhost:5001/Customers/CreateFATH/JoinListUC`se odešle, je .

Následující `?` `handler` znamená, že parametr trasy je volitelný.

## <a name="advanced-configuration-and-settings"></a>Pokročilá konfigurace a nastavení

Konfigurace a nastavení v následujících částech není vyžadováno většinou aplikací.

Chcete-li konfigurovat rozšířené možnosti, použijte metodu <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>rozšíření :

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> nastavení kořenového adresáře pro stránky nebo k přidání konvencí aplikačního modelu pro stránky. Další informace o konvencích naleznete v tématu [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).

Chcete-li předkompilovat zobrazení, viz [kompilace zobrazení razor](xref:mvc/views/view-compilation).

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Určete, že Razor Pages jsou v kořenovém adresáři obsahu

Ve výchozím nastavení jsou Razor Pages zakořeněny v adresáři */Pages.* Přidejte <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> a určete, že vaše stránky Razor jsou v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) aplikace:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Určete, že Razor Pages jsou ve vlastním kořenovém adresáři

Přidat <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> určit, že Razor Stránky jsou na vlastní kořenový adresář v aplikaci (poskytnout relativní cestu):

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* Viz [Začínáme se stránkami Razor ,](xref:tutorials/razor-pages/razor-pages-start)které vycházejí z tohoto úvodu
* [Stažení nebo zobrazení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)

Razor Pages je nový aspekt ASP.NET Core MVC, který usnadňuje a zprostředkovává kódování scénářů zaměřených na stránky.

Pokud hledáte kurz, který používá model-View-Controller přístup, najdete [v tématu Začínáme s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Tento dokument obsahuje úvod do Razor Pages. Není to krok za krokem tutorial. Pokud vám některé oddíly přijdou příliš pokročilé, přečtěte si část [Začínáme s razor pages](xref:tutorials/razor-pages/razor-pages-start). Přehled ASP.NET core najdete v [článku Úvod do ASP.NET jádra](xref:index).

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Vytvoření projektu Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Podrobné pokyny k vytvoření projektu Razor Pages najdete v tématu [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) Pages.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Spusťte `dotnet new webapp` z příkazového řádku.

Otevřete vygenerovaný soubor *.csproj* z Visual Studia for Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte `dotnet new webapp` z příkazového řádku.

---

## <a name="razor-pages"></a>Razor Pages

Razor Stránky je povolena v *Startup.cs*:

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Vezměme si základní stránku:<a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Předchozí kód vypadá hodně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používané v aplikaci ASP.NET Core s řadiči a zobrazeními. To, čím se `@page` liší, je směrnice. `@page`vytvoří soubor do akce MVC – což znamená, že zpracovává požadavky přímo, aniž by procházel řadičem. `@page`musí být první direktivou Razor na stránce. `@page`ovlivňuje chování jiných konstrukce Razor.

Podobná stránka pomocí `PageModel` třídy je zobrazena v následujících dvou souborech. Soubor *Pages/Index2.cshtml:*

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Pages/Index2.cshtml.cs* model stránky:

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Podle konvence `PageModel` má soubor třídy stejný název jako soubor Razor Page s připojeným *souborem .cs.* Například předchozí stránka Razor je *Pages/Index2.cshtml*. Soubor obsahující třídu `PageModel` se nazývá *Pages/Index2.cshtml.cs*.

Přidružení cest adres URL ke stránkám jsou určena umístěním stránky v systému souborů. V následující tabulce je uvedena cesta Razor Page a odpovídající adresa URL:

| Název souboru a cesta               | odpovídající adresa URL |
| ----------------- | ------------ |
| */Stránky/Index.cshtml* | `/` nebo `/Index` |
| */Stránky/Kontakt.cshtml* | `/Contact` |
| */Stránky/Obchod/Kontakt.cshtml* | `/Store/Contact` |
| */Stránky/Obchod/Index.cshtml* | `/Store` nebo `/Store/Index` |

Poznámky:

* Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *Stránky.*
* `Index`je výchozí stránka, pokud adresa URL stránku neobsahuje.

## <a name="write-a-basic-form"></a>Napsat základní formulář

Razor Pages je navržen tak, aby běžné vzory používané s webovými prohlížeči snadno implementovat při vytváření aplikace. [Vazby modelu](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro)a HTML pomocníky *všechny pracovat s* vlastnostmi definovanými ve třídě Razor Page. Vezměme si stránku, která implementuje `Contact` základní "kontaktujte nás" formulář pro model:

Pro ukázky v tomto `DbContext` dokumentu je inicializován v [souboru Startup.cs.](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16)

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Datový model:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Kontext db:

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Soubor zobrazení *Pages/Create.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Model stránky *Pages/Create.cshtml.cs:*

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Podle konvence `PageModel` je třída `<PageName>Model` volána a je ve stejném oboru názvů jako stránka.

Třída `PageModel` umožňuje oddělení logiky stránky od jeho prezentace. Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky. Toto oddělení umožňuje:

* Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).
* [Jednotka testuje](xref:test/razor-pages-tests) stránky.

Stránka má `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` požadavky (když uživatel zaúčtuje formulář). Můžete přidat metody obslužné rutiny pro jakýkoli příkaz HTTP. Nejběžnější obslužné rutiny jsou:

* `OnGet` pro inicializaci stavu potřebného pro stránku. [OnGet](#OnGet) ukázku.
* `OnPost` pro zpracování odesílání formulářů.

Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce. Předchozí kód je typický pro Razor Pages.

Pokud jste obeznámeni s ASP.NET aplikacemi pomocí ovladačů a zobrazení:

* Kód `OnPostAsync` v předchozím příkladu vypadá podobně jako typický kód řadiče.
* Většina primitiv MVC, jako je [vazba modelu](xref:mvc/models/model-binding), [ověření](xref:mvc/models/validation), [ověření](xref:mvc/models/validation)a výsledky akce jsou sdíleny.

Předchozí `OnPostAsync` metoda:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Základní tok `OnPostAsync`:

Zkontrolujte chyby ověření.

* Pokud nejsou žádné chyby, uložte data a přesměrovat.
* Pokud dojde k chybám, zobrazte stránku znovu se zprávami ověření. Ověření na straně klienta je shodné s tradičními aplikacemi ASP.NET Core MVC. V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.

Když jsou data úspěšně zadána, `OnPostAsync` metoda `RedirectToPage` obslužné rutiny zavolá pomocnou metodu k vrácení instance `RedirectToPageResult`aplikace . `RedirectToPage`je nový výsledek akce, `RedirectToAction` `RedirectToRoute`podobný nebo , ale přizpůsobené pro stránky. V předchozím vzorku přesměruje na kořenovou stránku indexu (`/Index`). `RedirectToPage`je podrobně popsána v části [Generování adres URL pro stránky.](#url_gen)

Pokud odeslaný formulář má chyby ověření (které`OnPostAsync` jsou předány serveru), metoda obslužné rutiny volá pomocnou metodu. `Page` `Page` vrací instanci `PageResult`. Vrácení `Page` je podobné jako jak se `View`vrátí akce v řadičích . `PageResult`je výchozí návratový typ metody obslužné rutiny. Metoda obslužné rutiny, která vrátí `void` vykreslí stránku.

Vlastnost `Customer` používá `[BindProperty]` atribut vyjádřit se k přihlášení k vazby modelu.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor Pages ve výchozím nastavení vázají vlastnosti pouze s neslovesy.`GET` Vazba na vlastnosti může snížit množství kódu, který musíte napsat. Vazba snižuje kód pomocí stejné vlastnosti`<input asp-for="Customer.Name">`k vykreslení polí formuláře ( ) a přijetí vstupu.

[!INCLUDE[](~/includes/bind-get.md)]

Domovská stránka (*Index.cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Přidružená `PageModel` třída (*Index.cshtml.cs*):

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Soubor *Index.cshtml* obsahuje následující značky pro vytvoření odkazu pro úpravy pro každý kontakt:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

Pomocník `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [značky kotvy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použil `asp-route-{value}` atribut ke generování odkazu na stránku Úpravy. Spojení obsahuje data trasy s ID kontaktu. Například, `https://localhost:5001/Edit/1`. [Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML. Pomocné spoje tagů jsou povoleny`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Soubor *Stránky/Edit.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

První řádek obsahuje `@page "{id:int}"` směrnice. Omezení`"{id:int}"` směrování informuje stránku o přijetí požadavků `int` na stránku, která obsahuje data trasy. Pokud požadavek na stránku neobsahuje data trasy, která `int`lze převést na , vrátí za běhu chyba HTTP 404 (nebyl nalezen). Chcete-li, aby id `?` volitelné, připojit k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Soubor *Pages/Edit.cshtml.cs:*

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Soubor *Index.cshtml* také obsahuje značky pro vytvoření tlačítka pro odstranění pro každý kontakt zákazníka:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Když je tlačítko odstranit vykresleno `formaction` v HTML, obsahuje jeho parametry pro:

* ID kontaktu zákazníka `asp-route-id` určené atributem.
* Určené `handler` atributem. `asp-page-handler`

Zde je příklad vykresleného tlačítka odstranění s ID `1`kontaktu zákazníka :

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Je-li tlačítko vybráno, je na server odeslán požadavek na formulář. `POST` Podle konvence je název metody obslužné rutiny vybrán na základě hodnoty parametru `handler` podle schématu `OnPost[handler]Async`.

Vzhledem `handler` `delete` k tomu, `OnPostDeleteAsync` že je v tomto `POST` příkladu, metoda obslužné rutiny se používá ke zpracování požadavku. Pokud `asp-page-handler` je nastavena na jinou `remove`hodnotu, například `OnPostRemoveAsync` , je vybrána metoda obslužné rutiny s názvem. Následující kód ukazuje `OnPostDeleteAsync` obslužnou rutinu:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

Metoda: `OnPostDeleteAsync`

* Přijme `id` řetězec dotazu. Pokud *index.cshtml* směrnice stránky obsahovalomezení směrování `"{id:int?}"`, `id` by pocházet z dat trasy. Data trasy `id` pro je určena v `https://localhost:5001/Customers/2`identifikátoru URI, například .
* Dotazuje databázi pro `FindAsync`kontakt zákazníka s .
* Pokud je kontakt zákazníka nalezen, bude odebrán ze seznamu kontaktů zákazníků. Databáze je aktualizována.
* Volání `RedirectToPage` k přesměrování na kořenovou`/Index`stránku indexu ( ).

## <a name="mark-page-properties-as-required"></a>Označit vlastnosti stránky podle potřeby

Vlastnosti `PageModel` na a lze označit [atributem Povinné:](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Další informace naleznete v [tématu Ověření modelu](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Zpracování požadavků HEAD s záložní obslužnou rutinou OnGet

`HEAD`požadavky umožňují načíst záhlaví pro konkrétní prostředek. Na `GET` rozdíl `HEAD` od požadavků požadavky nevracejí tělo odpovědi.

Obvykle je `OnHead` vytvořena obslužná rutina a volána pro `HEAD` požadavky: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

V ASP.NET Jádrem 2.1 nebo novějším, `OnGet` Razor `OnHead` Pages přejde zpět na volání obslužné rutiny, pokud není definována žádná obslužná rutina. Toto chování je povoleno volání [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Výchozí šablony generovat `SetCompatibilityVersion` volání v ASP.NET Core 2.1 a 2.2. `SetCompatibilityVersion`efektivně nastaví volbu `AllowMappingHeadRequestsToGetHandler` `true`Razor Pages na .

Místo toho, abyste se přihlásili ke všem chováním pomocí `SetCompatibilityVersion`, můžete se explicitně přihlásit ke *konkrétnímu* chování. Následující kód se přihlásí k `HEAD` povolení požadavků, které `OnGet` mají být mapovány na obslužnou rutinu:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF a Razor stránky

Nemusíte psát žádný kód pro [ověření antiforgery](xref:security/anti-request-forgery). Generování a validace tokenů antiforgery jsou automaticky zahrnuty do stránek Razor.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Použití rozložení, částečných částek, šablon a pomocných spoje značek se stránkami Razor

Stránky pracují se všemi možnostmi modulu Razor View. Rozložení, částečné, šablony, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* fungují stejným způsobem jako pro konvenční zobrazení Razor.

Pojďme declutter této stránce s využitím některých z těchto schopností.

Přidání [stránky rozložení](xref:mvc/views/layout) do *stránky/Shared/_Layout.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Rozložení](xref:mvc/views/layout):

* Určuje rozložení každé stránky (pokud se stránka neodhlásí z rozložení).
* Importuje struktury HTML, jako je JavaScript a styly.

Další informace najdete na [stránce rozložení.](xref:mvc/views/layout)

Vlastnost [Layout](xref:mvc/views/layout#specifying-a-layout) je nastavena v *pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Rozložení je ve složce *Stránky/Sdílené.* Stránky vyhledává jiná zobrazení (rozložení, šablony, částečné části) hierarchicky, počínaje stejnou složkou jako aktuální stránka. Rozložení ve složce *Stránky/Sdílené* lze použít z libovolné stránky Razor ve složce *Stránky.*

Soubor rozložení by měl být ve složce *Stránky/Sdílené.*

Doporučujeme **not** neumisužuje soubor rozložení do složky *Zobrazení nebo Sdílené.* *Zobrazení/Sdílené* je vzor zobrazení MVC. Razor Stránky jsou určeny k spoléhání se na hierarchii složek, nikoli na konvence cest.

Zobrazení hledání ze stránky Razor obsahuje složku *Stránky.* Rozložení, šablony a částečné části, které používáte s řadiči MVC a konvenční zobrazení Razor *prostě funguje*.

Přidejte soubor *Pages/_ViewImports.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`je vysvětleno dále v tutoriálu. Směrnice `@addTagHelper` přináší [integrované pomocné názvy značek](xref:mvc/views/tag-helpers/builtin-th/Index) na všechny stránky ve složce *Stránky.*

<a name="namespace"></a>

Při `@namespace` použití směrnice explicitně na stránce:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Směrnice nastaví obor názvů pro stránku. Směrnice `@model` nemusí obsahovat obor názvů.

Pokud `@namespace` je směrnice obsažena v *souboru _ViewImports.cshtml*, zadaný obor názvů poskytuje předponu `@namespace` pro generovaný obor názvů na stránce, která importuje direktivu. Zbytek generovaného oboru názvů (přípona) je relativní cesta oddělená tečkami mezi složkou obsahující *_ViewImports.cshtml* a složkou obsahující stránku.

Například `PageModel` třída *Pages/Customers/Edit.cshtml.cs* explicitně nastaví obor názvů:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Soubor *Pages/_ViewImports.cshtml* nastavuje následující obor názvů:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Generovaný obor názvů pro *stránku Pages/Customers/Edit.cshtml* `PageModel` Razor Page je stejný jako třída.

`@namespace`*také pracuje s konvenčními pohledy na Břitva.*

Původní soubor zobrazení *Pages/Create.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Aktualizovaný soubor zobrazení *Pages/Create.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Počáteční projekt Razor Pages](#rpvs17) obsahuje *pages/_ValidationScriptsPartial.cshtml*, který zavěsí ověření na straně klienta.

Další informace o částečných <xref:mvc/views/partial>zobrazeních naleznete v tématu .

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generování adres URL pro stránky

Stránka, zobrazená `Create` dříve, používá `RedirectToPage`:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Aplikace má následující strukturu souborů a složek:

* */Stránky*

  * *Soubor Index.cshtml*
  * */Zákazníci*

    * *Vytvořit.cshtml*
    * *Upravit.cshtml*
    * *Soubor Index.cshtml*

Stránky *Stránky/Zákazníci/Create.cshtml* a *Stránky/Zákazníci/Edit.cshtml* se po úspěchu přesměrují na *Stránky/Index.cshtml.* Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce. Řetězec `/Index` lze použít ke generování identifikátorů URI na stránce *Pages/Index.cshtml.* Příklad:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Název stránky je cesta ke stránce ze složky root `/` */Pages* `/Index`včetně prokladu (například). Předchozí ukázky generování adres URL nabízejí rozšířené možnosti a funkční funkce oproti pevně kódování adresy URL. Generování adresy URL používá [směrování](xref:mvc/controllers/routing) a může generovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.

Generování adres URL pro stránky podporuje relativní názvy. V následující tabulce je uvedeno, `RedirectToPage` která stránka Rejstřík je vybrána s různými parametry ze *stránek/zákazníků/create.cshtml*:

| PřesměrovatToPage(x)| stránka |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Stránky/rejstřík* |
| RedirectToPage("./Index"); | *Stránky/Zákazníci/Index* |
| RedirectToPage(".. /Index") | *Stránky/rejstřík* |
| RedirectToPage("Index")  | *Stránky/Zákazníci/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")`a `RedirectToPage("../Index")` jsou *relativní názvy*. Parametr `RedirectToPage` je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Relativní název propojení je užitečné při vytváření lokalit se složitou strukturou. Pokud k propojení stránek ve složce používáte relativní názvy, můžete tuto složku přejmenovat. Všechny odkazy stále fungují (protože neobsahovaly název složky).

Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Další informace naleznete v tématu <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Atribut ViewData

Data mohou být předána na stránku pomocí [atributu ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Vlastnosti na řadičích nebo `[ViewData]` modelech Razor Page s atributem mají své hodnoty uložené a načtené z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

V následujícím příkladu `AboutModel` obsahuje `Title` vlastnost `[ViewData]`označenou . Vlastnost `Title` je nastavena na název stránky Informace:

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

Na stránce O aplikaci přistupujte k vlastnosti `Title` jako k vlastnosti modelu:

```cshtml
<h1>@Model.Title</h1>
```

V rozložení se název čte ze slovníku ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje [Vlastnost TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [řadiči](/dotnet/api/microsoft.aspnetcore.mvc.controller). Tato vlastnost ukládá data, dokud se číst. Metody `Keep` `Peek` a lze použít ke kontrole dat bez odstranění. `TempData`je užitečné pro přesměrování, když jsou data potřebná pro více než jeden požadavek.

Následující kód nastaví `Message` hodnotu použití `TempData`:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Následující značky v souboru *Pages/Customers/Index.cshtml* `Message` zobrazují `TempData`hodnotu použití .

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model stránky *Pages/Customers/Index.cshtml.cs* použije `[TempData]` atribut `Message` na vlastnost.

```csharp
[TempData]
public string Message { get; set; }
```

Další informace naleznete v tématu [TempData](xref:fundamentals/app-state#tempdata) .

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Více obslužných rutin na stránku

Následující stránka generuje značky pro dvě `asp-page-handler` obslužné rutiny pomocí pomocníka pro označení:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Formulář v předchozím příkladu má dvě tlačítka `FormActionTagHelper` pro odeslání, z nichž každé používá k odeslání na jinou adresu URL. Atribut `asp-page-handler` je společníkem `asp-page`aplikace . `asp-page-handler`generuje adresy URL, které se odesílají ke každé metodě obslužné rutiny definované stránkou. `asp-page`není zadán, protože ukázka odkazuje na aktuální stránku.

Model stránky:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Předchozí kód používá *metody pojmenované obslužné rutiny*. Pojmenované metody obslužné rutiny `On<HTTP Verb>` jsou `Async` vytvořeny tak, že text v názvu za a před (pokud je k dispozici). V předchozím příkladu jsou metody stránky OnPost**JoinList**Async a OnPost**JoinListUC**Async. Při odebrání *onpost* a *async* `JoinList` jsou `JoinListUC`názvy obslužných rutin a .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Pomocí předchozího kódu je `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList`cesta url, která se odešle, . Cesta url, do `OnPostJoinListUCAsync` které `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`se odešle, je .

## <a name="custom-routes"></a>Vlastní trasy

Pomocí `@page` směrnice můžete:

* Zadejte vlastní trasu ke stránce. Například trasu na stránku Informace `/Some/Other/Path` lze `@page "/Some/Other/Path"`nastavit pomocí .
* Připojujte segmenty k výchozí trase stránky. Segment "položka" lze například přidat k výchozí trase `@page "item"`stránky s .
* Připojujte parametry k výchozí trase stránky. Například parametr ID `id`, může být vyžadován `@page "{id}"`pro stránku s .

Je podporována cesta relativní ke`~`kořenu určená vlnovkou ( ) na začátku cesty. Například `@page "~/Some/Other/Path"` je stejný `@page "/Some/Other/Path"`jako .

Pokud se vám nelíbí řetězec `?handler=JoinList` dotazu v adrese URL, změňte trasu tak, aby název obslužné rutiny v části cesty adresy URL. Trasu lze přizpůsobit přidáním šablony trasy uzavřené v `@page` uvozovkách za direktivu.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Pomocí předchozího kódu je `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList`cesta url, která se odešle, . Cesta url, do `OnPostJoinListUCAsync` které `https://localhost:5001/Customers/CreateFATH/JoinListUC`se odešle, je .

Následující `?` `handler` znamená, že parametr trasy je volitelný.

## <a name="configuration-and-settings"></a>Konfigurace a nastavení

Chcete-li konfigurovat rozšířené možnosti, použijte metodu `AddRazorPagesOptions` rozšíření na tvůrce MVC:

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

V současné době `RazorPagesOptions` můžete použít k nastavení kořenového adresáře pro stránky nebo přidat konvence modelu aplikace pro stránky. V budoucnu umožníme větší roztažnost tímto způsobem.

Chcete-li předkompilovat zobrazení, viz [kompilace zobrazení razor](xref:mvc/views/view-compilation) .

[Stáhnout nebo zobrazit ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Viz [Začínáme se stránkami Razor ,](xref:tutorials/razor-pages/razor-pages-start)které vychází z tohoto úvodu.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Určete, že Razor Pages jsou v kořenovém adresáři obsahu

Ve výchozím nastavení jsou Razor Pages zakořeněny v adresáři */Pages.* Přidejte [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete, že vaše stránky Razor jsou v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) [(ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Určete, že Razor Pages jsou ve vlastním kořenovém adresáři

Přidejte [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete, že vaše Razor Pages jsou ve vlastním kořenovém adresáři v aplikaci (zadejte relativní cestu):

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

::: moniker-end
