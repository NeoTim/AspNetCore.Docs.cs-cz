---
title: Úvod do Razor Pages v ASP.NET Core
author: Rick-Anderson
description: Přečtěte si, jak Razor Pages v ASP.NET Core usnadňuje a produktivnější vytváření kódu v různých scénářích než pomocí MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 10/07/2019
uid: razor-pages/index
ms.openlocfilehash: d12cf7f4f45c98c292b0d035c99e051d9b779e9a
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816124"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Úvod do Razor Pages v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)

Razor Pages může vytvářet kódovací scénáře zaměřené na stránky a lépe a produktivnější než používání řadičů a zobrazení.

Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Tento dokument představuje úvod do Razor Pages. Nejedná se o podrobný kurz. Pokud některé části obsahují příliš pokročilé, přečtěte si téma Začínáme [s Razor Pages](xref:tutorials/razor-pages/razor-pages-start). Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Vytvoření projektu Razor Pages

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Podrobné pokyny k vytvoření projektu Razor Pages najdete v tématu [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte `dotnet new webapp` z příkazového řádku.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Spusťte `dotnet new webapp` z příkazového řádku.

Otevřete vygenerovaný soubor *. csproj* z Visual Studio pro Mac.

---

## <a name="razor-pages"></a>Razor Pages

Razor Pages je povolený v *Startup.cs*:

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12)]

Zvažte základní stránku:<a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Předchozí kód vypadá podobně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními. To znamená, že se liší od [@page](xref:mvc/views/razor#page) direktivy. `@page` vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler. `@page` musí být první direktivou Razor na stránce. `@page` ovlivňuje chování jiných konstrukcí [Razor](xref:mvc/views/razor) . Názvy souborů Razor Pages mají příponu *. cshtml* .

Podobná stránka, která používá `PageModel` třídy, je zobrazena v následujících dvou souborech. Soubor *Pages/Index2. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Model stránky *stránky/Index2. cshtml. cs* :

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Podle konvence soubor `PageModel` třídy má stejný název jako soubor stránky Razor s *příponou. cs* . Například předchozí stránka Razor je *Pages/Index2. cshtml*. Soubor obsahující třídu `PageModel` má název *Pages/Index2. cshtml. cs*.

Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů. Následující tabulka ukazuje cestu stránky Razor a adresu URL pro porovnání:

| Název souboru a cesta               | shodná adresa URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` nebo `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` nebo `/Store/Index` |

Poznámky:

* Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *stránky* .
* `Index` je výchozí stránka, když adresa URL neobsahuje stránku.

## <a name="write-a-basic-form"></a>Napsat základní formulář

Razor Pages je navržený tak, aby se při vytváření aplikace usnadnily běžné vzory používané s webovými prohlížeči. [Vázání modelů](xref:mvc/models/model-binding), [pomocníkům značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníkům, kteří *pracují pouze* s vlastnostmi definovanými ve třídě stránky Razor. Zvažte stránku, která implementuje základní formulář "kontaktujte nás" pro model `Contact`:

V případě ukázek v tomto dokumentu se `DbContext` Inicializuje v souboru [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) .

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Datový model:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Kontext databáze:

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Soubor zobrazení *Pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Model stránky *Pages/Create. cshtml. cs* :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Podle konvence je třída `PageModel` volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.

Třída `PageModel` umožňuje oddělení logiky stránky od její prezentace. Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky. Toto oddělení umožňuje:

* Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).
* [Testování jednotek](xref:test/razor-pages-tests)

Stránka obsahuje *metodu obslužné rutiny*`OnPostAsync`, která běží na žádostech `POST` (když uživatel formulář odešle). Lze přidat metody obslužné rutiny pro jakýkoli příkaz HTTP. Nejběžnější obslužné rutiny jsou:

* `OnGet` k inicializaci stavu potřebného pro stránku. V předchozím kódu `OnGet` metoda zobrazí stránku *CreateModel. cshtml* Razor.
* `OnPost` pro zpracování odesílání formulářů.

Přípona názvů `Async` je volitelná, ale často se používá v konvenci pro asynchronní funkce. Předchozí kód je typický pro Razor Pages.

Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:

* Kód `OnPostAsync` v předchozím příkladu vypadá podobně jako typický kód kontroleru.
* Většina primitivních primitiv MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation)a výsledky akce, fungují stejně jako u řadičů a Razor Pages. 

Předchozí metoda `OnPostAsync`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Základní tok `OnPostAsync`:

Kontrola chyb ověřování.

* V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.
* Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami. V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy se neodeslaly na server.

Soubor zobrazení *Pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Vykreslený HTML ze *stránky/vytvořit. cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

V předchozím kódu vyúčtováním formuláře:

* S platnými daty:

  * Metoda obslužné rutiny `OnPostAsync` volá pomocnou metodu <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>. `RedirectToPage` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Je výsledkem akce.
    * Je podobný `RedirectToAction` nebo `RedirectToRoute` (používá se v řadičích a zobrazeních).
    * Je přizpůsoben pro stránky. V předchozí ukázce přesměruje na stránku kořenového indexu (`/Index`). `RedirectToPage` je podrobně popsána v části [generování adresy URL pro stránky](#url_gen) .

* S chybami ověřování, které jsou předány serveru:

  * Metoda obslužné rutiny `OnPostAsync` volá pomocnou metodu <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>. `Page` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Vrácení `Page` se podobá tomu, jak akce v řadičích vrací `View`. `PageResult` je výchozí návratový typ pro metodu obslužné rutiny. Metoda obslužné rutiny, která vrací `void` vykreslí stránku.
  * V předchozím příkladu publikování formuláře bez výsledků hodnoty v [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) vrací hodnotu false. V této ukázce nejsou v klientovi zobrazeny žádné chyby ověřování. V tomto dokumentu se pojednává o chybách ověřování.

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* S chybami ověřování zjištěnými ověřováním na straně klienta:

  * Data **nejsou** odeslána na server.
  * Ověřování na straně klienta je vysvětleno dále v tomto dokumentu.

Vlastnost `Customer` používá atribut [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) k přihlášení k vazbě modelu:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]` by **se neměla používat** pro modely obsahující vlastnosti, které by klient neměl měnit. Další informace najdete v tématu [přestavení](xref:data/ef-rp/crud#overposting).

Razor Pages ve výchozím nastavení vlastnosti BIND pouze s příkazy, které nejsou`GET`. Vazba na vlastnosti odstraní nutnost psaní kódu pro převod dat HTTP na typ modelu. Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře (`<input asp-for="Customer.Name">`) a přijměte vstup.

[!INCLUDE[](~/includes/bind-get.md)]

Prohlíží se soubor zobrazení *Pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* V předchozím kódu [pomocné rutina vstupní značky](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` váže prvek HTML `<input>` na výraz modelu `Customer.Name`.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) zpřístupňuje pomocníky značek.

### <a name="the-home-page"></a>Domovská stránka

*Index. cshtml* je Domovská stránka:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

Přidružená třída `PageModel` (*index.cshtml.cs*):

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Soubor *index. cshtml* obsahuje následující kód:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

[Pomocná značka `<a /a>` ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila atribut `asp-route-{value}` k vygenerování odkazu na stránku pro úpravy. Odkaz obsahuje data směrování s ID kontaktu. Například `https://localhost:5001/Edit/1`. [Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.

Soubor *index. cshtml* obsahuje značky pro vytvoření tlačítka Odstranit pro každého kontaktu zákazníka:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

Vykreslený kód HTML:

```HTML
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Pokud je tlačítko Odstranit vykresleno ve formátu HTML, obsahuje [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametry pro:

* ID kontaktu zákazníka určené atributem `asp-route-id`.
* `handler`určené atributem `asp-page-handler`.

Když je vybráno tlačítko, na server se pošle požadavek `POST` formuláře. Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty parametru `handler` podle `OnPost[handler]Async`schématu.

Vzhledem k tomu, že `handler` je `delete` v tomto příkladu, metoda obslužné rutiny `OnPostDeleteAsync` slouží ke zpracování žádosti `POST`. Pokud je `asp-page-handler` nastavena na jinou hodnotu, například `remove`, je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync`.

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

Metoda `OnPostDeleteAsync`:

* Získá `id` z řetězce dotazu.
* Dotazuje databázi na kontakt zákazníka s `FindAsync`.
* Pokud se kontakt zákazníka najde, odebere se a aktualizuje se databáze.
* Volá <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pro přesměrování na stránku kořenového indexu (`/Index`).

### <a name="the-editcshtml-file"></a>Upravit soubor. cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

První řádek obsahuje direktivu `@page "{id:int}"`. Omezení směrování`"{id:int}"` instruuje stránku, aby přijímala požadavky na stránku, která obsahuje data `int` tras. Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int`, vrátí modul runtime chybu HTTP 404 (Nenalezeno). Pokud chcete ID nastavit jako volitelné, přidejte `?` do omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Soubor *Edit.cshtml.cs* :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Ověřování

Ověřovací pravidla:

* Jsou deklarativně určeny ve třídě modelu.
* Jsou vynutily všude v aplikaci.

Obor názvů <xref:System.ComponentModel.DataAnnotations> poskytuje sadu předdefinovaných ověřovacích atributů, které jsou aplikovány deklarativně na třídu nebo vlastnost. Tato dataanotace také obsahuje atributy formátování, jako [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) , které vám pomůžou s formátováním a neposkytují žádné ověřování.

Vezměte v úvahu model `Customer`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Pomocí následujícího souboru zobrazení *Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Předchozí kód:

* Zahrnuje skripty pro ověření jQuery a jQuery.
* Používá `<div />` a `<span />` [pomocníka značek](xref:mvc/views/tag-helpers/intro) k povolení:

  * Ověřování na straně klienta.
  * Vykreslování chyby ověřování.

* Generuje následující kód HTML:

  [!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Při publikování formuláře vytvořit bez hodnoty Name se zobrazí chybová zpráva "pole název je povinné." ve formuláři. Pokud je v klientovi povolený jazyk JavaScript, zobrazí se v prohlížeči chyba bez odeslání na server.

Atribut `[StringLength(10)]` generuje `data-val-length-max="10"` vykresleného kódu HTML. `data-val-length-max` zabrání prohlížečům v zadávání více než maximální zadané délky. Pokud se k úpravě a přehrání příspěvku používá nástroj, například [Fiddler](https://www.telerik.com/fiddler) :

* S názvem delším než 10.
* Chybová zpráva "název pole musí být řetězec s maximální délkou 10." je vrácen.

Vezměte v úvahu následující model `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atributy ověřování určují chování, které se má vyhovět pro vlastnosti modelu, na které se aplikují:

* Atributy `Required` a `MinimumLength` označují, že vlastnost musí mít hodnotu, ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření.
* Atribut `RegularExpression` slouží k omezení znaků, které lze zadat. V předchozím kódu "Žánr":

  * Je nutné použít pouze písmena.
  * První písmeno musí být velkými písmeny. Mezery, číslice a speciální znaky nejsou povoleny.

* Hodnocení `RegularExpression`:

  * Vyžaduje, aby byl první znak velkým písmenem.
  * Umožňuje speciální znaky a čísla v následujících mezerách. "PG-13" je platné pro hodnocení, ale pro "Žánr" se nezdařilo.

* Atribut `Range` omezuje hodnotu na v zadaném rozsahu.
* Atribut `StringLength` nastaví maximální délku řetězcové vlastnosti a volitelně její minimální délku.
* Typy hodnot (například `decimal`, `int`, `float`, `DateTime`) jsou podstatou požadovány a nepotřebují atribut `[Required]`.

Stránka vytvořit pro model `Movie` zobrazuje chyby s neplatnými hodnotami:

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

Další informace naleznete v tématu:

* [Přidání ověření do aplikace Movie](xref:tutorials/razor-pages/validation)
* [Ověřování modelu v ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet

`HEAD` žádosti umožňují načíst hlavičky pro konkrétní prostředek. Na rozdíl od požadavků na `GET` `HEAD` požadavky nevrátí tělo odpovědi.

Obvykle je vytvořena a volána obslužná rutina `OnHead` pro `HEAD` požadavky:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages se vrátí k volání obslužné rutiny `OnGet`, pokud není definována žádná obslužná rutina `OnHead`.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF a Razor Pages

Razor Pages jsou chráněny [ověřováním proti padělání](xref:security/anti-request-forgery). [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vloží tokeny proti padělání do prvků formuláře HTML.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Použití rozložení, částečných, šablon a značek pomocníků s Razor Pages

Stránky fungují se všemi možnostmi modulu zobrazení Razor. Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*a *_ViewImports. cshtml* fungují stejným způsobem jako u tradičních zobrazení Razor.

Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.

Přidat [stránku rozložení](xref:mvc/views/layout) na *stránky/Shared/_Layout. cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

[Rozložení](xref:mvc/views/layout):

* Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).
* Importuje struktury HTML, jako jsou JavaScript a StyleSheet.
* Obsah stránky Razor se vykreslí tam, kde je volána `@RenderBody()`.

Další informace najdete v tématu [rozložení stránky](xref:mvc/views/layout).

Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránce Pages/_ViewStart. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Rozložení se nachází na *stránkách nebo ve sdílené* složce. Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka. Rozložení na *stránkách nebo ve sdílené* složce se dá použít na libovolné stránce Razor ve složce *Pages* .

Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.

Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky. *Zobrazení/Shared* je vzor zobrazení MVC. Razor Pages mají spoléhat na hierarchii složek, nikoli na konvence cest.

Zobrazení hledání ze stránky Razor obsahuje složku *stránky* . Rozložení, šablony a částečné typy používané s řadiči MVC a konvenčními zobrazeními Razor *fungují pouze*.

Přidejte soubor *Pages/_ViewImports. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` je vysvětleno dále v tomto kurzu. Direktiva `@addTagHelper` přiřadí [vestavěným pomocníkům značek](xref:mvc/views/tag-helpers/builtin-th/Index) všechny stránky ve složce *stránky* .

<a name="namespace"></a>

Direktiva `@namespace` nastavená na stránce:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Direktiva `@namespace` nastaví obor názvů pro stránku. Direktiva `@model` nemusíte zahrnovat obor názvů.

Pokud je direktiva `@namespace` obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje direktivu `@namespace`. Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.

Například stránky `PageModel` třídy */Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako třída `PageModel`.

`@namespace` *také funguje se konvenčními zobrazeními Razor.*

Zvažte *stránku zobrazení stránky/vytvořit soubor. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Aktualizovaný soubor zobrazení *Pages/Create. cshtml* s *_ViewImports. cshtml* a předchozím souborem rozložení:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

V předchozím kódu *_ViewImports. cshtml* importovala obor názvů a pomocníky značek. Soubor rozložení importoval soubory JavaScriptu.

[Projekt Razor Pages Starter](#rpvs17) obsahuje *stránku/_ValidationScriptsPartial. cshtml*, která se připojí k ověřování na straně klienta.

Další informace o částečných zobrazeních naleznete v tématu <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generování adresy URL pro stránky

Stránka `Create`, která se zobrazila dřív, používá `RedirectToPage`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Aplikace má následující strukturu souborů nebo složek:

* */Pages*

  * *Index. cshtml*
  * *Soukromí. cshtml*
  * */Customers*

    * *Vytvořit. cshtml*
    * *Upravit. cshtml*
    * *Index. cshtml*

*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit. cshtml* Pages přesměruje na *Pages/Customers/index. cshtml* po úspěchu. Řetězec `./Index` je relativní název stránky, který se používá pro přístup k předchozí stránce. Slouží ke generování adres URL na stránce *Pages/Customers/index. cshtml* . Příklad:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Absolutní `/Index` název stránky se používá ke generování adres URL na stránce *pages/index. cshtml* . Příklad:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně počátečního `/` (například `/Index`). Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkční funkce v rámci hardwarového kódování adresy URL. Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.

Generování adresy URL pro stránky podporuje relativní názvy. Následující tabulka ukazuje, která stránka index je vybrána pomocí různých parametrů `RedirectToPage` na *stránce Pages/Customers/Create. cshtml*.

| RedirectToPage (x)| Page |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Stránky/rejstřík* |
| RedirectToPage("./Index"); | *Stránky/zákazníci/rejstřík* |
| RedirectToPage(".. /Index") | *Stránky/rejstřík* |
| RedirectToPage ("index")  | *Stránky/zákazníci/rejstřík* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")`a `RedirectToPage("../Index")` jsou *relativní názvy*. Parametr `RedirectToPage` je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.

Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou. Když se k propojení mezi stránkami ve složce použijí relativní názvy:

* Přejmenování složky neruší relativní odkazy.
* Odkazy nejsou přerušeny, protože neobsahují název složky.

Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Další informace naleznete v tématu <xref:mvc/controllers/areas> a <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>ViewData – atribut

Data je možné předat stránce s <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>. Vlastnosti s atributem `[ViewData]` mají své hodnoty uložené a načtené z <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.

V následujícím příkladu `AboutModel` použije atribut `[ViewData]` na vlastnost `Title`:

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

Na stránce o aplikaci získáte přístup k vlastnosti `Title` jako vlastnost modelu:

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

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Tato vlastnost ukládá data do jejich čtení. Metody <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> a <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> lze použít k prohlédnutí dat bez odstranění. `TempData` je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.

Následující kód nastaví hodnotu `Message` pomocí `TempData`:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` pomocí `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model stránky *Pages/Customers/index. cshtml. cs* používá atribut `[TempData]` pro vlastnost `Message`.

```cs
[TempData]
public string Message { get; set; }
```

Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Více obslužných rutin na stránku

Následující stránka generuje značky pro dvě obslužné rutiny pomocí pomocníka značky `asp-page-handler`:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Formulář v předchozím příkladu obsahuje dvě tlačítka pro odeslání, z nichž každá používá `FormActionTagHelper` k odeslání na jinou adresu URL. Atribut `asp-page-handler` je doplňkem pro `asp-page`. `asp-page-handler` generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou. `asp-page` není zadán, protože ukázka odkazuje na aktuální stránku.

Model stránky:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Předchozí kód používá *pojmenované obslužné rutiny*. Pojmenované obslužné rutiny jsou vytvořeny tak, že převezme text v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici). V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async. Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Pomocí předchozího kódu je adresa URL, která se odesílá do `OnPostJoinListAsync`, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Cesta URL, která se odesílá do `OnPostJoinListUCAsync`, je `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Vlastní trasy

Direktiva `@page` použijte k těmto akcím:

* Zadejte vlastní trasu na stránku. Například trasa na stránku o aplikaci může být nastavena na `/Some/Other/Path` s `@page "/Some/Other/Path"`.
* Připojit segmenty k výchozí trase stránky. Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"`.
* Připojení parametrů k výchozí trase stránky. Například parametr ID `id`může být vyžadován pro stránku s `@page "{id}"`.

Je podporována kořenová relativní cesta, která je určena vlnovkou (`~`) na začátku cesty. Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"`.

Můžete změnit řetězec dotazu `?handler=JoinList` v adrese URL na segment směrování `/JoinList` zadáním `@page "{handler?}"`šablony trasy.

Pokud nechcete, aby řetězec dotazu `?handler=JoinList` v adrese URL, můžete změnit trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL. Trasu můžete přizpůsobit přidáním šablony trasy, která je uvedena v uvozovkách po `@page` direktivě.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Pomocí předchozího kódu je adresa URL, která se odesílá do `OnPostJoinListAsync`, `https://localhost:5001/Customers/CreateFATH/JoinList`. Cesta URL, která se odesílá do `OnPostJoinListUCAsync`, je `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

`?` následující `handler` znamená, že parametr trasy je nepovinný.

## <a name="advanced-configuration-and-settings"></a>Pokročilá konfigurace a nastavení

Konfigurace a nastavení v následujících oddílech není pro většinu aplikací vyžadováno.

Chcete-li nakonfigurovat rozšířené možnosti, použijte metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> můžete nastavit kořenový adresář pro stránky nebo přidat konvence modelu aplikace pro stránky. Další informace o konvencích najdete v tématu [Razor Pages autorizačních konvencí](xref:security/authorization/razor-pages-authorization).

Chcete-li předkompilovat zobrazení, přečtěte si téma [kompilace zobrazení Razor](xref:mvc/views/view-compilation).

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Zadejte, že Razor Pages jsou v kořenu obsahu.

Ve výchozím nastavení se Razor Pages rootem v adresáři */Pages* . Přidejte <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> a určete tak, že Razor Pages jsou v [kořenu obsahu](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) aplikace:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Zadejte, že Razor Pages jsou ve vlastním kořenovém adresáři

Přidejte <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> a určete tak, že Razor Pages jsou ve vlastním kořenovém adresáři v aplikaci (zadejte relativní cestu):

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* Další informace najdete v tématu [Začínáme s Razor Pages, který sestává](xref:tutorials/razor-pages/razor-pages-start)z tohoto úvodního sestavení.
* [Stáhnout nebo zobrazit ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

Spusťte `dotnet new webapp` z příkazového řádku.

Otevřete vygenerovaný soubor *. csproj* z Visual Studio pro Mac.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte `dotnet new webapp` z příkazového řádku.

---

## <a name="razor-pages"></a>Razor Pages

Razor Pages je povolený v *Startup.cs*:

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Zvažte základní stránku:<a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Předchozí kód vypadá podobně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními. To znamená, že se liší od `@page` direktivy. `@page` vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler. `@page` musí být první direktivou Razor na stránce. `@page` ovlivňuje chování jiných konstrukcí Razor.

Podobná stránka, která používá `PageModel` třídy, je zobrazena v následujících dvou souborech. Soubor *Pages/Index2. cshtml* :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Model stránky *stránky/Index2. cshtml. cs* :

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Podle konvence soubor `PageModel` třídy má stejný název jako soubor stránky Razor s *příponou. cs* . Například předchozí stránka Razor je *Pages/Index2. cshtml*. Soubor obsahující třídu `PageModel` má název *Pages/Index2. cshtml. cs*.

Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů. Následující tabulka ukazuje cestu stránky Razor a adresu URL pro porovnání:

| Název souboru a cesta               | shodná adresa URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` nebo `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` nebo `/Store/Index` |

Poznámky:

* Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *stránky* .
* `Index` je výchozí stránka, když adresa URL neobsahuje stránku.

## <a name="write-a-basic-form"></a>Napsat základní formulář

Razor Pages je navržený tak, aby se při vytváření aplikace usnadnily běžné vzory používané s webovými prohlížeči. [Vázání modelů](xref:mvc/models/model-binding), [pomocníkům značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníkům, kteří *pracují pouze* s vlastnostmi definovanými ve třídě stránky Razor. Zvažte stránku, která implementuje základní formulář "kontaktujte nás" pro model `Contact`:

V případě ukázek v tomto dokumentu se `DbContext` Inicializuje v souboru [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Datový model:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Kontext databáze:

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Soubor zobrazení *Pages/Create. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Model stránky *Pages/Create. cshtml. cs* :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Podle konvence je třída `PageModel` volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.

Třída `PageModel` umožňuje oddělení logiky stránky od její prezentace. Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky. Toto oddělení umožňuje:

* Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).
* [Testování částí](xref:test/razor-pages-tests) stránky

Stránka obsahuje *metodu obslužné rutiny*`OnPostAsync`, která běží na žádostech `POST` (když uživatel formulář odešle). Můžete přidat metody obslužné rutiny pro libovolný příkaz HTTP. Nejběžnější obslužné rutiny jsou:

* `OnGet` k inicializaci stavu potřebného pro stránku. Ukázka [OnGet](#OnGet)
* `OnPost` pro zpracování odesílání formulářů.

Přípona názvů `Async` je volitelná, ale často se používá v konvenci pro asynchronní funkce. Předchozí kód je typický pro Razor Pages.

Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:

* Kód `OnPostAsync` v předchozím příkladu vypadá podobně jako typický kód kontroleru.
* Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation), [ověřování](xref:mvc/models/validation)a výsledky akce, jsou sdílené.

Předchozí metoda `OnPostAsync`:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Základní tok `OnPostAsync`:

Kontrola chyb ověřování.

* V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.
* Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami. Ověřování na straně klienta je stejné jako tradiční aplikace ASP.NET Core MVC. V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy se neodeslaly na server.

Po úspěšném zadání dat metoda obslužné rutiny `OnPostAsync` volá pomocnou metodu `RedirectToPage`, aby vrátila instanci `RedirectToPageResult`. `RedirectToPage` je nový výsledek akce, podobně jako `RedirectToAction` nebo `RedirectToRoute`, ale přizpůsobený pro stránky. V předchozí ukázce přesměruje na stránku kořenového indexu (`/Index`). `RedirectToPage` je podrobně popsána v části [generování adresy URL pro stránky](#url_gen) .

Když odeslaný formulář obsahuje chyby ověřování (které jsou předány serveru), metoda obslužné rutiny`OnPostAsync` volá pomocnou metodu `Page`. `Page` vrací instanci `PageResult`. Vrácení `Page` se podobá tomu, jak akce v řadičích vrací `View`. `PageResult` je výchozí návratový typ pro metodu obslužné rutiny. Metoda obslužné rutiny, která vrací `void` vykreslí stránku.

Vlastnost `Customer` používá atribut `[BindProperty]` k přihlášení k vazbě modelu.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor Pages ve výchozím nastavení vlastnosti BIND pouze s příkazy, které nejsou`GET`. Vazba na vlastnosti může snížit množství kódu, který musíte napsat. Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře (`<input asp-for="Customer.Name">`) a přijměte vstup.

[!INCLUDE[](~/includes/bind-get.md)]

Domovská stránka (*index. cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Přidružená třída `PageModel` (*index.cshtml.cs*):

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Soubor *index. cshtml* obsahuje následující kód pro vytvoření odkazu pro úpravy pro každý kontakt:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

[Pomocná značka `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila atribut `asp-route-{value}` k vygenerování odkazu na stránku pro úpravy. Odkaz obsahuje data směrování s ID kontaktu. Například `https://localhost:5001/Edit/1`. [Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML. Pomocník značek je povolený pomocí `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Soubor *Pages/Edit. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

První řádek obsahuje direktivu `@page "{id:int}"`. Omezení směrování`"{id:int}"` instruuje stránku, aby přijímala požadavky na stránku, která obsahuje data `int` tras. Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int`, vrátí modul runtime chybu HTTP 404 (Nenalezeno). Pokud chcete ID nastavit jako volitelné, přidejte `?` do omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Soubor *Pages/Edit. cshtml. cs* :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Soubor *index. cshtml* také obsahuje značky pro vytvoření tlačítka Odstranit pro kontaktování každého zákazníka:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Když se v HTML vykreslí tlačítko Odstranit, jeho `formaction` obsahuje parametry pro:

* ID kontaktu zákazníka určené atributem `asp-route-id`.
* `handler` určené atributem `asp-page-handler`.

Tady je příklad vygenerovaného tlačítka odstranit s ID kontaktu zákazníka `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Když je vybráno tlačítko, na server se pošle požadavek `POST` formuláře. Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty parametru `handler` podle `OnPost[handler]Async`schématu.

Vzhledem k tomu, že `handler` je `delete` v tomto příkladu, metoda obslužné rutiny `OnPostDeleteAsync` slouží ke zpracování žádosti `POST`. Pokud je `asp-page-handler` nastavena na jinou hodnotu, například `remove`, je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync`. Následující kód ukazuje obslužnou rutinu `OnPostDeleteAsync`:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

Metoda `OnPostDeleteAsync`:

* Přijímá `id` z řetězce dotazu. Pokud direktiva stránky *index. cshtml* obsahuje omezení směrování `"{id:int?}"`, `id` by pocházel z dat trasy. Data trasy pro `id` jsou uvedena v identifikátoru URI, jako je například `https://localhost:5001/Customers/2`.
* Dotazuje databázi na kontakt zákazníka s `FindAsync`.
* Pokud se kontakt zákazníka najde, odebere se ze seznamu kontaktů zákazníka. Databáze je aktualizována.
* Volá `RedirectToPage` pro přesměrování na stránku kořenového indexu (`/Index`).

## <a name="mark-page-properties-as-required"></a>Označit vlastnosti stránky jako povinné

Vlastnosti na `PageModel` lze dekorovat pomocí [požadovaného](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atributu:

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Další informace najdete v tématu [ověřování modelu](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet

žádosti `HEAD` umožňují načíst hlavičky pro konkrétní prostředek. Na rozdíl od požadavků na `GET` `HEAD` požadavky nevrátí tělo odpovědi.

Obvykle je vytvořena a volána obslužná rutina `OnHead` pro `HEAD` požadavky: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

V ASP.NET Core 2,1 nebo novější Razor Pages návrat k volání obslužné rutiny `OnGet`, pokud není definována žádná obslužná rutina `OnHead`. Toto chování je povoleno voláním [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Výchozí šablony generují `SetCompatibilityVersion` volání v ASP.NET Core 2,1 a 2,2. `SetCompatibilityVersion` efektivně nastaví možnost Razor Pages `AllowMappingHeadRequestsToGetHandler` na `true`.

Místo toho, abyste se rozhodli pro všechna chování s `SetCompatibilityVersion`, se můžete výslovně vyjádřit ke *konkrétnímu* chování. Následující kód výslovný v nástroji, aby bylo umožněno mapování `HEAD` požadavků na obslužnou rutinu `OnGet`:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF a Razor Pages

Nemusíte psát žádný kód pro [ověřování proti padělání](xref:security/anti-request-forgery). Generování a ověření tokenu antipadělání jsou automaticky zahrnuty do Razor Pages.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Použití rozložení, částečných, šablon a značek pomocníků s Razor Pages

Stránky fungují se všemi možnostmi modulu zobrazení Razor. Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*, *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních zobrazení Razor.

Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.

Přidat [stránku rozložení](xref:mvc/views/layout) na *stránky/Shared/_Layout. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Rozložení](xref:mvc/views/layout):

* Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).
* Importuje struktury HTML, jako jsou JavaScript a StyleSheet.

Další informace najdete na [stránce rozložení](xref:mvc/views/layout) .

Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránce Pages/_ViewStart. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Rozložení se nachází na *stránkách nebo ve sdílené* složce. Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka. Rozložení na *stránkách nebo ve sdílené* složce se dá použít na libovolné stránce Razor ve složce *Pages* .

Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.

Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky. *Zobrazení/Shared* je vzor zobrazení MVC. Razor Pages mají spoléhat na hierarchii složek, nikoli na konvence cest.

Zobrazení hledání ze stránky Razor obsahuje složku *stránky* . Rozložení, šablony a částečně používané s řadiči MVC a konvenčními zobrazeními Razor *fungují pouze*.

Přidejte soubor *Pages/_ViewImports. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` je vysvětleno dále v tomto kurzu. Direktiva `@addTagHelper` přiřadí [vestavěným pomocníkům značek](xref:mvc/views/tag-helpers/builtin-th/Index) všechny stránky ve složce *stránky* .

<a name="namespace"></a>

Při explicitním použití direktivy `@namespace` na stránce:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Direktiva nastaví obor názvů pro stránku. Direktiva `@model` nemusíte zahrnovat obor názvů.

Pokud je direktiva `@namespace` obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje direktivu `@namespace`. Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.

Například stránky `PageModel` třídy */Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako třída `PageModel`.

`@namespace` *také funguje se konvenčními zobrazeními Razor.*

Soubor zobrazení původní *stránky/vytvořit. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Aktualizovaný soubor zobrazení *stránky/vytvoření. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Projekt Razor Pages Starter](#rpvs17) obsahuje *stránku/_ValidationScriptsPartial. cshtml*, která se připojí k ověřování na straně klienta.

Další informace o částečných zobrazeních naleznete v tématu <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generování adresy URL pro stránky

Stránka `Create`, která se zobrazila dřív, používá `RedirectToPage`:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Aplikace má následující strukturu souborů nebo složek:

* */Pages*

  * *Index. cshtml*
  * */Customers*

    * *Vytvořit. cshtml*
    * *Upravit. cshtml*
    * *Index. cshtml*

*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit.* cshtml Pages přesměruje na *pages/index. cshtml* po úspěchu. Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce. Řetězec `/Index` lze použít ke generování identifikátorů URI na stránce *pages/index. cshtml* . Příklad:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně počátečního `/` (například `/Index`). Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkce, které se zakódujeme na adresu URL. Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.

Generování adresy URL pro stránky podporuje relativní názvy. Následující tabulka ukazuje, která stránka indexu je vybrána s různými parametry `RedirectToPage` ze *stránky/zákazníci/vytvořit. cshtml*:

| RedirectToPage (x)| Page |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Stránky/rejstřík* |
| RedirectToPage("./Index"); | *Stránky/zákazníci/rejstřík* |
| RedirectToPage(".. /Index") | *Stránky/rejstřík* |
| RedirectToPage ("index")  | *Stránky/zákazníci/rejstřík* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")`a `RedirectToPage("../Index")` jsou *relativní názvy*. Parametr `RedirectToPage` je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou. Pokud k propojení mezi stránkami ve složce použijete relativní názvy, můžete tuto složku přejmenovat. Všechny odkazy pořád fungují (protože neobsahují název složky).

Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Další informace najdete v tématu <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>ViewData – atribut

Data je možné předat na stránku pomocí [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Vlastnosti na řadičích nebo modelech stránek Razor upravených pomocí `[ViewData]` mají jejich hodnoty uložené a načtené z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

V následujícím příkladu `AboutModel` obsahuje vlastnost `Title` dekorovaná pomocí `[ViewData]`. Vlastnost `Title` je nastavena na název stránky o produktu:

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

Na stránce o aplikaci získáte přístup k vlastnosti `Title` jako vlastnost modelu:

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

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje vlastnost [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [řadiči](/dotnet/api/microsoft.aspnetcore.mvc.controller). Tato vlastnost ukládá data do jejich čtení. Metody `Keep` a `Peek` lze použít k prohlédnutí dat bez odstranění. `TempData` je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.

Následující kód nastaví hodnotu `Message` pomocí `TempData`:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` pomocí `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model stránky *Pages/Customers/index. cshtml. cs* používá atribut `[TempData]` pro vlastnost `Message`.

```cs
[TempData]
public string Message { get; set; }
```

Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata) .

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Více obslužných rutin na stránku

Následující stránka generuje značky pro dvě obslužné rutiny pomocí pomocníka značky `asp-page-handler`:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Formulář v předchozím příkladu obsahuje dvě tlačítka pro odeslání, z nichž každá používá `FormActionTagHelper` k odeslání na jinou adresu URL. Atribut `asp-page-handler` je doplňkem pro `asp-page`. `asp-page-handler` generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou. `asp-page` není zadán, protože ukázka odkazuje na aktuální stránku.

Model stránky:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Předchozí kód používá *pojmenované obslužné rutiny*. Pojmenované obslužné rutiny jsou vytvořeny tak, že převezme text v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici). V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async. Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Pomocí předchozího kódu je adresa URL, která se odesílá do `OnPostJoinListAsync`, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Cesta URL, která se odesílá do `OnPostJoinListUCAsync`, je `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Vlastní trasy

Direktiva `@page` použijte k těmto akcím:

* Zadejte vlastní trasu na stránku. Například trasa na stránku o aplikaci může být nastavena na `/Some/Other/Path` s `@page "/Some/Other/Path"`.
* Připojit segmenty k výchozí trase stránky. Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"`.
* Připojení parametrů k výchozí trase stránky. Například parametr ID `id`může být vyžadován pro stránku s `@page "{id}"`.

Je podporována kořenová relativní cesta, která je určena vlnovkou (`~`) na začátku cesty. Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"`.

Můžete změnit řetězec dotazu `?handler=JoinList` v adrese URL na segment směrování `/JoinList` zadáním `@page "{handler?}"`šablony trasy.

Pokud nechcete, aby řetězec dotazu `?handler=JoinList` v adrese URL, můžete změnit trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL. Trasu můžete přizpůsobit přidáním šablony trasy, která je uvedena v uvozovkách po `@page` direktivě.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Pomocí předchozího kódu je adresa URL, která se odesílá do `OnPostJoinListAsync`, `https://localhost:5001/Customers/CreateFATH/JoinList`. Cesta URL, která se odesílá do `OnPostJoinListUCAsync`, je `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

`?` následující `handler` znamená, že parametr trasy je nepovinný.

## <a name="configuration-and-settings"></a>Konfigurace a nastavení

Pokud chcete konfigurovat pokročilé možnosti, použijte metodu rozšíření `AddRazorPagesOptions` v Tvůrci MVC:

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

V současné době můžete pomocí `RazorPagesOptions` nastavit kořenový adresář pro stránky nebo přidat konvence modelu aplikace pro stránky. Tímto způsobem v budoucnu povolíme lepší rozšíření.

Chcete-li předkompilovat zobrazení, přečtěte si téma [kompilace zobrazení Razor](xref:mvc/views/view-compilation) .

[Stažení nebo zobrazení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Další informace najdete v tématu [Začínáme s Razor Pages, který sestává](xref:tutorials/razor-pages/razor-pages-start)z tohoto úvodního sestavení.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Zadejte, že Razor Pages jsou v kořenu obsahu.

Ve výchozím nastavení se Razor Pages rootem v adresáři */Pages* . Přidejte [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete tak, že Razor Pages jsou v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:

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

::: moniker-end
