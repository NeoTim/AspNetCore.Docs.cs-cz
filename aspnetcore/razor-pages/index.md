---
title: Úvod do Razor stránek v ASP.NET Core
author: Rick-Anderson
description: Přečtěte si, jak Razor stránky v ASP.NET Core zjednodušují a produktivnější vytváření kódu na stránce, než pomocí MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: 8058cc3c3461d72c713c8be53c3667112adb2ae5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634121"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a>Úvod do Razor stránek v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)

Razor Stránky můžou vytvářet kódovací scénáře zaměřené na stránky a lépe a produktivnější než používání řadičů a zobrazení.

Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Tento dokument popisuje úvodní Razor stránky. Nejedná se o podrobný kurz. Pokud zjistíte, že některé části jsou moc pokročilé, přečtěte si téma Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start). Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Předpoklady

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Vytvořit Razor projekt stránek

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Podrobné pokyny k vytvoření projektu stránky najdete v tématu Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) Razor .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte `dotnet new webapp` příkaz z příkazového řádku.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Podrobné pokyny k vytvoření projektu stránky najdete v tématu Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) Razor .

---

## <a name="no-locrazor-pages"></a>Razor Stránky

Razor Stránky jsou povolené v *Startup.cs*:

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Zvažte základní stránku: <a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Předchozí kód vypadá jako [ Razor soubor zobrazení](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními. To znamená, že tato direktiva je odlišná [`@page`](xref:mvc/views/razor#page) . `@page` Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler. `@page` musí se jednat o první Razor direktivu na stránce. `@page` má vliv na chování jiných [Razor](xref:mvc/views/razor) konstrukcí. Razor Názvy souborů stránek mají příponu *. cshtml* .

Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech. Soubor *Pages/Index2. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Model stránky *stránky/Index2. cshtml. cs* :

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Podle konvence `PageModel` má soubor třídy stejný název jako Razor stránkovací soubor s *příponou. cs* . Například předchozí Razor stránka je *Pages/Index2. cshtml*. Soubor obsahující `PageModel` třídu má název *Pages/Index2. cshtml. cs*.

Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů. Následující tabulka ukazuje Razor cestu stránky a adresu URL pro porovnání:

| Název souboru a cesta               | shodná adresa URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` nebo `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` nebo `/Store/Index` |

Poznámky:

* Modul runtime Razor ve výchozím nastavení vyhledá soubory stránek ve složce *stránky* .
* `Index` je výchozí stránka, když adresa URL neobsahuje stránku.

## <a name="write-a-basic-form"></a>Napsat základní formulář

Razor Stránky jsou navržené tak, aby při vytváření aplikace byly běžné vzory používané s webovými prohlížeči, které se dají snadno implementovat. [Vazba modelů](xref:mvc/models/model-binding), [pomocníky značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníků pro HTML *stačí pracovat* s vlastnostmi definovanými ve Razor třídě stránky. Zvažte stránku, která pro model implementuje základní formulář "kontaktujte nás" `Contact` :

Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) .

Databáze v paměti vyžaduje `Microsoft.EntityFrameworkCore.InMemory` balíček NuGet.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Datový model:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Kontext databáze:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Soubor zobrazení *Pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Model stránky *Pages/Create. cshtml. cs* :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Podle konvence `PageModel` je třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.

`PageModel`Třída umožňuje oddělení logiky stránky od její prezentace. Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky. Toto oddělení umožňuje:

* Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).
* [Testování částí](xref:test/razor-pages-tests)

Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle). Lze přidat metody obslužné rutiny pro jakýkoli příkaz HTTP. Nejběžnější obslužné rutiny jsou:

* `OnGet` pro inicializaci stavu potřebného pro stránku. V předchozím kódu `OnGet` Metoda zobrazí stránku *CreateModel. cshtml* Razor .
* `OnPost` pro zpracování odesílání formulářů.

Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce. Předchozí kód je typický pro Razor stránky.

Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:

* `OnPostAsync`Kód v předchozím příkladu vypadá podobně jako typický kód kontroleru.
* Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation)a výsledky akce, fungují stejně jako řadiče a Razor stránky. 

Předchozí `OnPostAsync` metoda:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Základní tok `OnPostAsync` :

Kontrola chyb ověřování.

* V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.
* Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami. V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.

Soubor zobrazení *Pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Vykreslený HTML ze *stránky/vytvořit. cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

V předchozím kódu vyúčtováním formuláře:

* S platnými daty:

  * `OnPostAsync`Metoda obslužné rutiny volá <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pomocnou metodu. `RedirectToPage` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Je výsledkem akce.
    * Je podobný `RedirectToAction` nebo `RedirectToRoute` (používá se v řadičích a zobrazeních).
    * Je přizpůsoben pro stránky. V předchozí ukázce přesměruje na stránku kořenového indexu ( `/Index` ). `RedirectToPage` je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .

* S chybami ověřování, které jsou předány serveru:

  * `OnPostAsync`Metoda obslužné rutiny volá <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> pomocnou metodu. `Page` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Vrácení `Page` se podobá tomu, jak vrátí akce v řadičích `View` . `PageResult` je výchozí návratový typ pro metodu obslužné rutiny. Metoda obslužné rutiny, která vrací `void` vykreslení stránky.
  * V předchozím příkladu publikování formuláře bez výsledků hodnoty v [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) vrací hodnotu false. V této ukázce nejsou v klientovi zobrazeny žádné chyby ověřování. V tomto dokumentu se pojednává o chybách ověřování.

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* S chybami ověřování zjištěnými ověřováním na straně klienta:

  * Data **nejsou** odeslána na server.
  * Ověřování na straně klienta je vysvětleno dále v tomto dokumentu.

`Customer`Vlastnost používá [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atribut pro výslovný souhlas s vazbou modelu:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]` neměl **by se** používat pro modely obsahující vlastnosti, které by klient neměl měnit. Další informace najdete v tématu [přestavení](xref:data/ef-rp/crud#overposting).

Razor Stránky, ve výchozím nastavení vlastnosti BIND pouze bez `GET` slovesa. Vazba na vlastnosti odstraní nutnost psaní kódu pro převod dat HTTP na typ modelu. Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře ( `<input asp-for="Customer.Name">` ) a přijměte vstup.

[!INCLUDE[](~/includes/bind-get.md)]

Prohlíží se soubor zobrazení *Pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* V předchozím kódu [Pomocník vstupní značky](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` váže `<input>` prvek HTML na `Customer.Name` výraz modelu.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) zpřístupňuje pomocníkům značky.

### <a name="the-home-page"></a>Domovská stránka

*Index. cshtml* je Domovská stránka:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

Přidružená `PageModel` třída (*index.cshtml.cs*):

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Soubor *index. cshtml* obsahuje následující kód:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

`<a /a>` [Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy. Odkaz obsahuje data směrování s ID kontaktu. Například, `https://localhost:5001/Edit/1`. [Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor

Soubor *index. cshtml* obsahuje značky pro vytvoření tlačítka Odstranit pro každého kontaktu zákazníka:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

Vykreslený kód HTML:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Pokud je tlačítko Odstranit vykresleno ve formátu HTML, obsahuje [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametry pro:

* ID kontaktu zákazníka určené `asp-route-id` atributem.
* `handler`, Určené `asp-page-handler` atributem.

Když je vybráno tlačítko, `POST` pošle se na server požadavek na formulář. Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu `OnPost[handler]Async` .

Vzhledem k `handler` `delete` tomu, že je v tomto příkladu, `OnPostDeleteAsync` Metoda obslužné rutiny se používá ke zpracování `POST` požadavku. Pokud `asp-page-handler` je nastaven na jinou hodnotu, například `remove` , je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync` .

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

`OnPostDeleteAsync`Metoda:

* Získá `id` z řetězce dotazu.
* Dotazuje databázi na kontaktování zákazníka `FindAsync` .
* Pokud se kontakt zákazníka najde, odebere se a aktualizuje se databáze.
* Volání <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pro přesměrování na stránku kořenového indexu ( `/Index` ).

### <a name="the-editcshtml-file"></a>Upravit soubor. cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

První řádek obsahuje `@page "{id:int}"` direktivu. Omezení směrování `"{id:int}"` instruuje stránku, aby přijímala požadavky na stránku, která obsahuje `int` data směrování. Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int` , modul runtime vrátí chybu HTTP 404 (Nenalezeno). Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Soubor *Edit.cshtml.cs* :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Ověřování

Ověřovací pravidla:

* Jsou deklarativně určeny ve třídě modelu.
* Jsou vynutily všude v aplikaci.

<xref:System.ComponentModel.DataAnnotations>Obor názvů poskytuje sadu předdefinovaných ověřovacích atributů, které jsou aplikovány deklarativně na třídu nebo vlastnost. Tato dataanotace také obsahuje atributy formátování [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) , jako jsou tyto informace užitečné při formátování a neposkytují žádné ověřování.

Vezměte v úvahu `Customer` model:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Pomocí následujícího souboru zobrazení *Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Předcházející kód:

* Zahrnuje skripty pro ověření jQuery a jQuery.
* Používá `<div />` `<span />` [pomocníky značek](xref:mvc/views/tag-helpers/intro) a k povolení:

  * Ověřování na straně klienta.
  * Vykreslování chyby ověřování.

* Generuje následující kód HTML:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Při publikování formuláře vytvořit bez hodnoty Name se zobrazí chybová zpráva "pole název je povinné." ve formuláři. Pokud je v klientovi povolený jazyk JavaScript, zobrazí se v prohlížeči chyba bez odeslání na server.

`[StringLength(10)]`Atribut generuje `data-val-length-max="10"` na vykresleném HTML. `data-val-length-max` zabrání prohlížečům v zadávání více než maximální zadané délky. Pokud se k úpravě a přehrání příspěvku používá nástroj, například [Fiddler](https://www.telerik.com/fiddler) :

* S názvem delším než 10.
* Chybová zpráva "název pole musí být řetězec s maximální délkou 10." .

Vezměte v úvahu následující `Movie` model:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atributy ověřování určují chování, které se má vyhovět pro vlastnosti modelu, na které se aplikují:

* `Required`Atributy a `MinimumLength` označují, že vlastnost musí mít hodnotu, ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření.
* `RegularExpression`Atribut slouží k omezení znaků, které lze zadat. V předchozím kódu "Žánr":

  * Je nutné použít pouze písmena.
  * První písmeno musí být velkými písmeny. Mezery, číslice a speciální znaky nejsou povoleny.

* `RegularExpression`Hodnocení:

  * Vyžaduje, aby byl první znak velkým písmenem.
  * Umožňuje speciální znaky a čísla v následujících mezerách. "PG-13" je platné pro hodnocení, ale pro "Žánr" se nezdařilo.

* Atribut `Range` omezuje hodnotu v konkrétním rozsahu.
* `StringLength`Atribut nastaví maximální délku řetězcové vlastnosti a volitelně její minimální délku.
* Typy hodnot (například `decimal` , `int` , `float` , `DateTime` ) jsou podstatně požadovány a nepotřebují `[Required]` atribut.

Stránka vytvořit pro `Movie` model zobrazuje chyby s neplatnými hodnotami:

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

Další informace:

* [Přidání ověření do aplikace Movie](xref:tutorials/razor-pages/validation)
* [Ověřování modelu v ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet

`HEAD` žádosti umožňují načíst hlavičky pro konkrétní prostředek. Na rozdíl od `GET` požadavků `HEAD` požadavky nevrátí tělo odpovědi.

Obvykle `OnHead` je obslužná rutina vytvořena a volána pro `HEAD` požadavky:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Stránky se vrátí k volání `OnGet` obslužné rutiny, `OnHead` Pokud není definována žádná obslužná rutina.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF a Razor stránky

Razor Stránky jsou chráněny [ověřováním proti padělání](xref:security/anti-request-forgery). [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vloží tokeny proti padělání do prvků formuláře HTML.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Použití rozložení, částečných, šablon a značek pomocníka se Razor stránkami

Stránky fungují se všemi možnostmi Razor modulu zobrazení. Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*a *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních Razor zobrazení.

Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.

Přidejte [stránku rozložení](xref:mvc/views/layout) na *stránky/sdílené/_Layout. cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

[Rozložení](xref:mvc/views/layout):

* Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).
* Importuje struktury HTML, jako jsou JavaScript a StyleSheet.
* Obsah Razor stránky se vykreslí tam, kde `@RenderBody()` se volá.

Další informace najdete v tématu [rozložení stránky](xref:mvc/views/layout).

Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránky/_ViewStart. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Rozložení se nachází na *stránkách nebo ve sdílené* složce. Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka. Rozložení na *stránkách nebo ve sdílené* složce můžete použít na libovolné Razor stránce ve složce *stránky* .

Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.

Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky. *Zobrazení/Shared* je vzor zobrazení MVC. Razor Stránky mají sloužit k spoléhání se na hierarchii složek, nikoli na konvence cest.

Zobrazení hledání ze Razor stránky obsahuje složku *stránky* . Rozložení, šablony a částečné typy používané s řadiči MVC a konvenčními Razor zobrazeními *fungují pouze*.

Přidejte soubor *Pages/_ViewImports. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` je vysvětleno dále v tomto kurzu. Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .

<a name="namespace"></a>

`@namespace`Direktiva nastavená na stránce:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

`@namespace`Direktiva nastaví obor názvů pro stránku. `@model`Direktiva nemusí zahrnovat obor názvů.

Pokud `@namespace` je direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje `@namespace` direktivu. Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.

Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` Třída.

`@namespace`*funguje také s konvenčními Razor zobrazeními.*

Zvažte *stránku zobrazení stránky/vytvořit soubor. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Aktualizovaný soubor zobrazení *Pages/Create. cshtml* s *_ViewImports. cshtml* a předchozím souborem rozložení:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

V předchozím kódu *_ViewImports. cshtml* importovala obor názvů a pomocníka značek. Soubor rozložení importoval soubory JavaScriptu.

[ Razor Úvodní projekt stránky](#rpvs17) obsahuje *stránky/_ValidationScriptsPartial. cshtml*, které se připojí k ověřování na straně klienta.

Další informace o částečných zobrazeních naleznete v tématu <xref:mvc/views/partial> .

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generování adresy URL pro stránky

`Create`Stránka, která se zobrazuje dřív, používá `RedirectToPage` :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Aplikace má následující strukturu souborů nebo složek:

* */Pages*

  * *Soubor Index.cshtml*
  * *Soukromí. cshtml*
  * */Customers*

    * *Vytvořit. cshtml*
    * *Upravit. cshtml*
    * *Soubor Index.cshtml*

*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit. cshtml* Pages přesměruje na *Pages/Customers/index. cshtml* po úspěchu. Řetězec `./Index` je relativní název stránky, který slouží k přístupu na předchozí stránku. Slouží ke generování adres URL na stránce *Pages/Customers/index. cshtml* . Příklad:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Absolutní název stránky `/Index` se používá ke generování adres URL na stránce *pages/index. cshtml* . Příklad:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` (například `/Index` ). Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkční funkce v rámci hardwarového kódování adresy URL. Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.

Generování adresy URL pro stránky podporuje relativní názvy. Následující tabulka ukazuje, která stránka index je vybrána pomocí různých `RedirectToPage` parametrů na *stránkách/zákaznících/vytvořit. cshtml*.

| RedirectToPage (x)| Stránka |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Stránky/rejstřík* |
| RedirectToPage("./Index"); | *Stránky/zákazníci/rejstřík* |
| RedirectToPage(".. /Index") | *Stránky/rejstřík* |
| RedirectToPage ("index")  | *Stránky/zákazníci/rejstřík* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")` a `RedirectToPage("../Index")` jsou *relativní názvy*. `RedirectToPage`Parametr je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.

Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou. Když se k propojení mezi stránkami ve složce použijí relativní názvy:

* Přejmenování složky neruší relativní odkazy.
* Odkazy nejsou přerušeny, protože neobsahují název složky.

Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Další informace naleznete v tématech <xref:mvc/controllers/areas> a <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>ViewData – atribut

Data je možné předat stránce pomocí <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> . Vlastnosti s `[ViewData]` atributem mají své hodnoty uložené a načtené z <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .

V následujícím příkladu `AboutModel` použije `[ViewData]` atribut na `Title` vlastnost:

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

Na stránce o aplikaci získáte přístup k `Title` vlastnosti jako vlastnost modelu:

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

ASP.NET Core zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Tato vlastnost ukládá data do jejich čtení. <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Metody a <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> lze použít k prohlédnutí dat bez odstranění. `TempData` je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.

Následující kód nastaví hodnotu `Message` pomocí `TempData` :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData` .

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.

```csharp
[TempData]
public string Message { get; set; }
```

Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Více obslužných rutin na stránku

Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Formulář v předchozím příkladu obsahuje dvě tlačítka pro odeslání, z nichž každá používá `FormActionTagHelper` pro odeslání na jinou adresu URL. `asp-page-handler`Atribut je doprovodný objekt k `asp-page` . `asp-page-handler` generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou. `asp-page` není zadáno, protože ukázka odkazuje na aktuální stránku.

Model stránky:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Předchozí kód používá *pojmenované obslužné rutiny*. Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici). V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async. Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC` .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` . Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .

## <a name="custom-routes"></a>Vlastní trasy

Použijte tuto `@page` direktivu pro:

* Zadejte vlastní trasu na stránku. Například trasa na stránku o aplikaci může být nastavena na `/Some/Other/Path` `@page "/Some/Other/Path"` .
* Připojit segmenty k výchozí trase stránky. Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"` .
* Připojení parametrů k výchozí trase stránky. Například parametr ID `id` může být požadován pro stránku s `@page "{id}"` .

Je podporována kořenová relativní cesta, která je určena vlnovkou ( `~` ) na začátku cesty. Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"` .

Pokud se v adrese URL nelíbí řetězec dotazu `?handler=JoinList` , změňte trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL. Trasa se dá přizpůsobit přidáním šablony trasy, která je uzavřená do dvojitých uvozovek za `@page` direktivou.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` . Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .

`?`Následující `handler` údaj znamená, že parametr trasy je nepovinný.

## <a name="advanced-configuration-and-settings"></a>Pokročilá konfigurace a nastavení

Konfigurace a nastavení v následujících oddílech není pro většinu aplikací vyžadováno.

Pro konfiguraci pokročilých možností použijte <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> přetížení, které konfiguruje <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> můžete nastavit kořenový adresář pro stránky nebo přidat konvence modelu aplikace pro stránky. Další informace o konvencích najdete v tématu [ Razor autorizační konvence stránek](xref:security/authorization/razor-pages-authorization).

Chcete-li předkompilovat zobrazení, přečtěte si téma [ Razor zobrazení kompilace](xref:mvc/views/view-compilation).

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Zadejte, jestli Razor se stránky nacházejí v kořenu obsahu.

Ve výchozím nastavení Razor se stránky rootují v adresáři */Pages* . Přidat <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> k určení, že vaše Razor stránky jsou v [kořenu obsahu](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) aplikace:

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Zadejte, jestli Razor jsou stránky ve vlastním kořenovém adresáři.

Přidat <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> k určení, že Razor stránky jsou v aplikaci vlastním kořenovým adresářem (zadejte relativní cestu):

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje informací

* Viz Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start), které jsou založeny na tomto úvodu.
* [Autorizovat atributy a Razor stránky](xref:security/authorization/simple#aarp)
* [Stáhnout nebo zobrazit ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)

Razor Stránky jsou novým aspektem ASP.NET Core MVC, který usnadňuje a produktivnější vytváření kódu pro scénáře zaměřené na stránku.

Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Tento dokument popisuje úvodní Razor stránky. Nejedná se o podrobný kurz. Pokud zjistíte, že některé části jsou moc pokročilé, přečtěte si téma Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start). Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Předpoklady

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Vytvořit Razor projekt stránek

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Podrobné pokyny k vytvoření projektu stránky najdete v tématu Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) Razor .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Spusťte `dotnet new webapp` příkaz z příkazového řádku.

Otevřete vygenerovaný soubor *. csproj* z Visual Studio pro Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte `dotnet new webapp` příkaz z příkazového řádku.

---

## <a name="no-locrazor-pages"></a>Razor Stránky

Razor Stránky jsou povolené v *Startup.cs*:

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Zvažte základní stránku: <a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Předchozí kód vypadá jako [ Razor soubor zobrazení](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními. To znamená, že tato direktiva je odlišná `@page` . `@page` Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler. `@page` musí se jednat o první Razor direktivu na stránce. `@page` má vliv na chování jiných Razor konstrukcí.

Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech. Soubor *Pages/Index2. cshtml* :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Model stránky *stránky/Index2. cshtml. cs* :

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Podle konvence `PageModel` má soubor třídy stejný název jako Razor stránkovací soubor s *příponou. cs* . Například předchozí Razor stránka je *Pages/Index2. cshtml*. Soubor obsahující `PageModel` třídu má název *Pages/Index2. cshtml. cs*.

Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů. Následující tabulka ukazuje Razor cestu stránky a adresu URL pro porovnání:

| Název souboru a cesta               | shodná adresa URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` nebo `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` nebo `/Store/Index` |

Poznámky:

* Modul runtime Razor ve výchozím nastavení vyhledá soubory stránek ve složce *stránky* .
* `Index` je výchozí stránka, když adresa URL neobsahuje stránku.

## <a name="write-a-basic-form"></a>Napsat základní formulář

Razor Stránky jsou navržené tak, aby při vytváření aplikace byly běžné vzory používané s webovými prohlížeči, které se dají snadno implementovat. [Vazba modelů](xref:mvc/models/model-binding), [pomocníky značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníků pro HTML *stačí pracovat* s vlastnostmi definovanými ve Razor třídě stránky. Zvažte stránku, která pro model implementuje základní formulář "kontaktujte nás" `Contact` :

Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Datový model:

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Kontext databáze:

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Soubor zobrazení *Pages/Create. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Model stránky *Pages/Create. cshtml. cs* :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Podle konvence `PageModel` je třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.

`PageModel`Třída umožňuje oddělení logiky stránky od její prezentace. Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky. Toto oddělení umožňuje:

* Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).
* [Testování částí](xref:test/razor-pages-tests) stránky

Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle). Můžete přidat metody obslužné rutiny pro jakýkoli příkaz HTTP. Nejběžnější obslužné rutiny jsou:

* `OnGet` pro inicializaci stavu potřebného pro stránku. Ukázka [OnGet](#OnGet)
* `OnPost` pro zpracování odesílání formulářů.

Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce. Předchozí kód je typický pro Razor stránky.

Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:

* `OnPostAsync`Kód v předchozím příkladu vypadá podobně jako typický kód kontroleru.
* Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation), [ověřování](xref:mvc/models/validation)a výsledky akce, jsou sdílené.

Předchozí `OnPostAsync` metoda:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Základní tok `OnPostAsync` :

Kontrola chyb ověřování.

* V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.
* Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami. Ověřování na straně klienta je stejné jako tradiční aplikace ASP.NET Core MVC. V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.

Po úspěšném zadání dat `OnPostAsync` zavolá metoda obslužné rutiny `RedirectToPage` pomocnou metodu, která vrátí instanci `RedirectToPageResult` . `RedirectToPage` je nový výsledek akce, který se podobá `RedirectToAction` nebo `RedirectToRoute` , ale přizpůsobený pro stránky. V předchozí ukázce přesměruje na stránku kořenového indexu ( `/Index` ). `RedirectToPage` je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .

Když odeslaný formulář obsahuje chyby ověřování (které jsou předány serveru), `OnPostAsync` Metoda obslužné rutiny volá `Page` pomocnou metodu. `Page` vrací instanci `PageResult`. Vrácení `Page` se podobá tomu, jak vrátí akce v řadičích `View` . `PageResult` je výchozí návratový typ pro metodu obslužné rutiny. Metoda obslužné rutiny, která vrací `void` vykreslení stránky.

`Customer`Vlastnost používá `[BindProperty]` atribut pro přihlášení k vazbě modelu.

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor Stránky, ve výchozím nastavení vlastnosti BIND pouze bez `GET` slovesa. Vazba na vlastnosti může snížit množství kódu, který musíte napsat. Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře ( `<input asp-for="Customer.Name">` ) a přijměte vstup.

[!INCLUDE[](~/includes/bind-get.md)]

Domovská stránka (*index. cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Přidružená `PageModel` třída (*index.cshtml.cs*):

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Soubor *index. cshtml* obsahuje následující kód pro vytvoření odkazu pro úpravy pro každý kontakt:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy. Odkaz obsahuje data směrování s ID kontaktu. Například, `https://localhost:5001/Edit/1`. [Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor Pomocník značek je povolený nástrojem `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Soubor *Pages/Edit. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

První řádek obsahuje `@page "{id:int}"` direktivu. Omezení směrování `"{id:int}"` instruuje stránku, aby přijímala požadavky na stránku, která obsahuje `int` data směrování. Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int` , modul runtime vrátí chybu HTTP 404 (Nenalezeno). Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Soubor *Pages/Edit. cshtml. cs* :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Soubor *index. cshtml* také obsahuje značky pro vytvoření tlačítka Odstranit pro kontaktování každého zákazníka:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Když je tlačítko Odstranit vykresleno ve formátu HTML, `formaction` obsahuje parametry pro:

* ID kontaktu zákazníka určené `asp-route-id` atributem
* `handler`Zadané `asp-page-handler` atributem.

Tady je příklad vygenerovaného tlačítka odstranit s ID kontaktu zákazníka `1` :

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Když je vybráno tlačítko, `POST` pošle se na server požadavek na formulář. Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu `OnPost[handler]Async` .

Vzhledem k `handler` `delete` tomu, že je v tomto příkladu, `OnPostDeleteAsync` Metoda obslužné rutiny se používá ke zpracování `POST` požadavku. Pokud `asp-page-handler` je nastaven na jinou hodnotu, například `remove` , je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync` . Následující kód ukazuje `OnPostDeleteAsync` obslužnou rutinu:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

`OnPostDeleteAsync`Metoda:

* Akceptuje `id` z řetězce dotazu. Pokud direktiva stránky *index. cshtml* obsahuje omezení směrování `"{id:int?}"` , `id` pocházela z dat směrování. Data trasy pro `id` se zadává v identifikátoru URI, jako je například `https://localhost:5001/Customers/2` .
* Dotazuje databázi na kontaktování zákazníka `FindAsync` .
* Pokud se kontakt zákazníka najde, odebere se ze seznamu kontaktů zákazníka. Databáze je aktualizována.
* Volání `RedirectToPage` pro přesměrování na stránku kořenového indexu ( `/Index` ).

## <a name="mark-page-properties-as-required"></a>Označit vlastnosti stránky jako povinné

Vlastnosti v `PageModel` lze označit [požadovaným](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atributem:

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Další informace najdete v tématu [ověřování modelu](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet

`HEAD` žádosti umožňují načíst hlavičky pro konkrétní prostředek. Na rozdíl od `GET` požadavků `HEAD` požadavky nevrátí tělo odpovědi.

Obvykle `OnHead` je obslužná rutina vytvořena a volána pro `HEAD` požadavky: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

V ASP.NET Core 2,1 nebo novějších se Razor stránky vrátí k volání `OnGet` obslužné rutiny, pokud `OnHead` není definována žádná obslužná rutina. Toto chování je povoleno voláním [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Výchozí šablony generují `SetCompatibilityVersion` volání v ASP.NET Core 2,1 a 2,2. `SetCompatibilityVersion` efektivně nastaví Razor možnost stránky `AllowMappingHeadRequestsToGetHandler` na `true` .

Místo toho, abyste se rozhodli pro všechna chování pomocí `SetCompatibilityVersion` , se můžete výslovně vyjádřit ke *konkrétnímu* chování. Následující kód výslovný v pro povolení `HEAD` Mapování požadavků na `OnGet` obslužnou rutinu:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF a Razor stránky

Nemusíte psát žádný kód pro [ověřování proti padělání](xref:security/anti-request-forgery). Generování a ověření tokenu antipadělání jsou automaticky zahrnuty na Razor stránkách.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Použití rozložení, částečných, šablon a značek pomocníka se Razor stránkami

Stránky fungují se všemi možnostmi Razor modulu zobrazení. Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*, *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních Razor zobrazení.

Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.

Přidejte [stránku rozložení](xref:mvc/views/layout) na *stránky/sdílené/_Layout. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Rozložení](xref:mvc/views/layout):

* Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).
* Importuje struktury HTML, jako jsou JavaScript a StyleSheet.

Další informace najdete na [stránce rozložení](xref:mvc/views/layout) .

Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránky/_ViewStart. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Rozložení se nachází na *stránkách nebo ve sdílené* složce. Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka. Rozložení na *stránkách nebo ve sdílené* složce můžete použít na libovolné Razor stránce ve složce *stránky* .

Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.

Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky. *Zobrazení/Shared* je vzor zobrazení MVC. Razor Stránky mají sloužit k spoléhání se na hierarchii složek, nikoli na konvence cest.

Zobrazení hledání ze Razor stránky obsahuje složku *stránky* . Rozložení, šablony a částečné typy, které používáte s řadiči MVC a konvenčními Razor zobrazeními, *fungují pouze*.

Přidejte soubor *Pages/_ViewImports. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` je vysvětleno dále v tomto kurzu. Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .

<a name="namespace"></a>

V případě `@namespace` explicitního použití direktivy na stránce:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Direktiva nastaví obor názvů pro stránku. `@model`Direktiva nemusí zahrnovat obor názvů.

Pokud `@namespace` je direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje `@namespace` direktivu. Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.

Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` Třída.

`@namespace`*funguje také s konvenčními Razor zobrazeními.*

Soubor zobrazení původní *stránky/vytvořit. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Aktualizovaný soubor zobrazení *stránky/vytvoření. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[ Razor Úvodní projekt stránky](#rpvs17) obsahuje *stránky/_ValidationScriptsPartial. cshtml*, které se připojí k ověřování na straně klienta.

Další informace o částečných zobrazeních naleznete v tématu <xref:mvc/views/partial> .

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generování adresy URL pro stránky

`Create`Stránka, která se zobrazuje dřív, používá `RedirectToPage` :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Aplikace má následující strukturu souborů nebo složek:

* */Pages*

  * *Soubor Index.cshtml*
  * */Customers*

    * *Vytvořit. cshtml*
    * *Upravit. cshtml*
    * *Soubor Index.cshtml*

*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit.* cshtml Pages přesměruje na *pages/index. cshtml* po úspěchu. Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce. Řetězec `/Index` lze použít ke generování identifikátorů URI na stránce *pages/index. cshtml* . Příklad:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` (například `/Index` ). Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkce, které se zakódujeme na adresu URL. Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.

Generování adresy URL pro stránky podporuje relativní názvy. Následující tabulka ukazuje, která stránka indexu je vybrána s různými `RedirectToPage` parametry ze *stránky/zákazníci/vytvořit. cshtml*:

| RedirectToPage (x)| Stránka |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Stránky/rejstřík* |
| RedirectToPage("./Index"); | *Stránky/zákazníci/rejstřík* |
| RedirectToPage(".. /Index") | *Stránky/rejstřík* |
| RedirectToPage ("index")  | *Stránky/zákazníci/rejstřík* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` a `RedirectToPage("../Index")`  jsou *relativní názvy*. `RedirectToPage`Parametr je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou. Pokud k propojení mezi stránkami ve složce použijete relativní názvy, můžete tuto složku přejmenovat. Všechny odkazy pořád fungují (protože neobsahují název složky).

Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Další informace naleznete v tématu <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>ViewData – atribut

Data je možné předat na stránku pomocí [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Vlastnosti v řadičích nebo Razor modelech stránek s `[ViewData]` atributem mají své hodnoty uložené a načtené z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

V následujícím příkladu `AboutModel` obsahuje `Title` vlastnost s označením `[ViewData]` . `Title`Vlastnost je nastavena na název stránky o produktu:

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

Na stránce o aplikaci získáte přístup k `Title` vlastnosti jako vlastnost modelu:

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

ASP.NET Core zpřístupňuje vlastnost [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [řadiči](/dotnet/api/microsoft.aspnetcore.mvc.controller). Tato vlastnost ukládá data do jejich čtení. `Keep`Metody a `Peek` lze použít k prohlédnutí dat bez odstranění. `TempData` je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.

Následující kód nastaví hodnotu `Message` pomocí `TempData` :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData` .

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.

```csharp
[TempData]
public string Message { get; set; }
```

Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata) .

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Více obslužných rutin na stránku

Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Formulář v předchozím příkladu obsahuje dvě tlačítka pro odeslání, z nichž každá používá `FormActionTagHelper` pro odeslání na jinou adresu URL. `asp-page-handler`Atribut je doprovodný objekt k `asp-page` . `asp-page-handler` generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou. `asp-page` není zadáno, protože ukázka odkazuje na aktuální stránku.

Model stránky:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Předchozí kód používá *pojmenované obslužné rutiny*. Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici). V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async. Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC` .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` . Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .

## <a name="custom-routes"></a>Vlastní trasy

Použijte tuto `@page` direktivu pro:

* Zadejte vlastní trasu na stránku. Například trasa na stránku o aplikaci může být nastavena na `/Some/Other/Path` `@page "/Some/Other/Path"` .
* Připojit segmenty k výchozí trase stránky. Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"` .
* Připojení parametrů k výchozí trase stránky. Například parametr ID `id` může být požadován pro stránku s `@page "{id}"` .

Je podporována kořenová relativní cesta, která je určena vlnovkou ( `~` ) na začátku cesty. Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"` .

Pokud se v adrese URL nelíbí řetězec dotazu `?handler=JoinList` , změňte trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL. Trasa se dá přizpůsobit přidáním šablony trasy, která je uzavřená do dvojitých uvozovek za `@page` direktivou.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` . Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .

`?`Následující `handler` údaj znamená, že parametr trasy je nepovinný.

## <a name="configuration-and-settings"></a>Konfigurace a nastavení

Pokud chcete konfigurovat pokročilé možnosti, použijte metodu rozšíření `AddRazorPagesOptions` v Tvůrci MVC:

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

V současné době můžete použít `RazorPagesOptions` k nastavení kořenového adresáře pro stránky nebo přidat konvence modelu aplikace pro stránky. Tímto způsobem v budoucnu povolíme lepší rozšíření.

Chcete-li předkompilovat zobrazení, přečtěte si téma [ Razor zobrazení kompilace](xref:mvc/views/view-compilation) .

[Stažení nebo zobrazení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Viz Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start), které jsou založeny na tomto úvodu.

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Zadejte, jestli Razor se stránky nacházejí v kořenu obsahu.

Ve výchozím nastavení Razor se stránky rootují v adresáři */Pages* . Přidejte [s Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete, jestli Razor jsou vaše stránky v [kořenu obsahu](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Zadejte, jestli Razor jsou stránky ve vlastním kořenovém adresáři.

Přidejte [s Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete tak, že vaše Razor stránky jsou v aplikaci vlastním kořenovým adresářem (zadejte relativní cestu):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Další zdroje informací

* [Autorizovat atributy a Razor stránky](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
