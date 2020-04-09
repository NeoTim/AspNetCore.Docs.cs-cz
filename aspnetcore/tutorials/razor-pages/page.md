---
title: Šanek žiletky stránky v ASP.NET Core
author: rick-anderson
description: Vysvětluje Razor Stránky generované lešení.
ms.author: riande
ms.date: 08/17/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: cec4295a2c08c89db0975808583f41c7d09bfc88
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662447"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a>Šanek žiletky stránky v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz zkoumá Razor stránky vytvořené lešení v [předchozím kurzu](xref:tutorials/razor-pages/model).

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Stránky Vytvořit, Odstranit, Podrobnosti a Upravit

Prohlédněte si stránkový model *Pages/Movies/Index.cshtml.cs:*

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor Stránky jsou `PageModel`odvozeny od . Podle konvence `PageModel`-derived třídy `<PageName>Model`se nazývá . Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) `RazorPagesMovieContext` k přidání na stránku. Všechny lešení stránky následovat tento vzor. Další informace o asynchronním programování pomocí entity Framework naleznete v [tématu Asynchronní kód.](xref:data/ef-rp/intro#asynchronous-code)

Když je požadavek na stránku, `OnGetAsync` metoda vrátí seznam filmů na razor page. `OnGetAsync`nebo `OnGet` je volána k inicializaci stavu stránky. V tomto `OnGetAsync` případě získá seznam filmů a zobrazí je.

Při `OnGet` `void` vrácení `OnGetAsync` `Task`nebo vrácení , není použit žádný příkaz return. Pokud je `IActionResult` návratový `Task<IActionResult>`typ nebo , musí být zadán příkaz return. Například metoda *Pages/Movies/Create.cshtml.cs:* `OnPostAsync`

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Prohlédněte si *stránku Pages/Movies/Index.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Holicí strojek může přejít z HTML do Jazyka C# nebo do značky specifické pro razor. Pokud `@` symbol následuje [razor vyhrazené klíčové slovo](xref:mvc/views/razor#razor-reserved-keywords), přechody do razor-specifické značky, jinak se přejde do C#.

### <a name="the-page-directive"></a>Směrnice @page

Razor `@page` směrnice umožňuje soubor akce MVC, což znamená, že může zpracovávat požadavky. `@page`musí být první direktivou Razor na stránce. `@page`je příkladem přechodu na značky specifické pro Razor. Další informace naleznete v [tématu Syntaxe břitvy.](xref:mvc/views/razor#razor-syntax)

Zkontrolujte výraz lambda použitý v následujícím pomocném prvku HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Pomocník `DisplayNameFor` HTML zkontroluje `Title` vlastnost, na kterou odkazuje výraz lambda, a určí tak zobrazovaný název. Lambda výraz je kontrolována spíše než vyhodnocena. To znamená, že neexistuje `model` `model.Movie`žádné `model.Movie[0]` narušení `null` přístupu, když , , nebo je prázdný. Při vyhodnocení výrazu lambda (například s `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnoceny hodnoty vlastností modelu.

<a name="md"></a>

### <a name="the-model-directive"></a>Směrnice @model

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Směrnice `@model` určuje typ modelu předaného razor Page. V předchozím příkladu `@model` řádek zpřístupňuje `PageModel`-derived třídy Razor Page. Model se používá `@Html.DisplayNameFor` v `@Html.DisplayFor` a [HTML pomocníky](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.

### <a name="the-layout-page"></a>Stránka rozložení

Vyberte odkazy na nabídky **(RazorPagesMovie**, **Home**a **Privacy**). Každá stránka zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout.cshtml.* Otevřete soubor *Pages/Shared/_Layout.cshtml.*

[Šablony rozložení](xref:mvc/views/layout) umožňují rozložení kontejneru HTML:

* Specifikováno na jednom místě.
* Použito na více stránkách webu.

Najděte `@RenderBody()` linku. `RenderBody`je zástupný symbol, ve kterém se zobrazí všechna zobrazení specifická pro *stránku, zabalená* na stránce rozložení. Vyberte například odkaz **Soukromí** a zobrazení *Stránky/Privacy.cshtml* `RenderBody` se vykreslí uvnitř metody.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData a rozložení

Vezměte v úvahu následující značky ze souboru *Pages/Movies/Index.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Předchozí zvýrazněné značky je příkladem Razor přechodu do C#. Znaky `{` `}` a uzavřete blok kódu Jazyka C#.

Základní `PageModel` třída obsahuje `ViewData` vlastnost dictionary, kterou lze použít k předání dat do zobrazení. Objekty jsou `ViewData` přidány do slovníku pomocí klíče/hodnoty vzoru. V předchozí ukázce je `"Title"` vlastnost `ViewData` přidána do slovníku.

Vlastnost `"Title"` se používá v souboru *Pages/Shared/_Layout.cshtml.* Následující značky zobrazují několik prvních řádků souboru *_Layout.cshtml.*

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

Linka `@*Markup removed for brevity.*@` je razor komentář. Na rozdíl`<!-- -->`od html komentáře ( ), Razor komentáře nejsou odesílány klientovi.

### <a name="update-the-layout"></a>Aktualizace rozložení

Změňte `<title>` prvek v souboru *Pages/Shared/_Layout.cshtml* tak, aby zobrazoval **film** místo **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

V souboru *Stránky/Sdílené/_Layout.cshtml* najdete následující prvek ukotvení.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Nahraďte předchozí prvek následujícími značkami:

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Předchozí prvek ukotvení je [Tag Helper](xref:mvc/views/tag-helpers/intro). V tomto případě je to [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Tag `asp-page="/Movies/Index"` Helper atribut a hodnota vytvoří `/Movies/Index` odkaz na holicí strojek stránku. Hodnota `asp-area` atributu je prázdná, takže oblast není použita v odkazu. Další informace naleznete v [části Oblasti.](xref:mvc/controllers/areas)

Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie.** Pokud máte nějaké problémy, podívejte se na soubor [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) v GitHubu.

Otestujte ostatní odkazy **(Domů**, **RpMovie**, **Vytvořit**, **Upravit**a **Odstranit).** Každá stránka nastaví název, který můžete vidět na kartě prohlížeče. Když záložku přidáte do záložek, použije se pro záložku název.

> [!NOTE]
> Je možné, že do pole nebudete moci `Price` zadat desetinná čárka. Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a formáty dat mimo americkou angličtinu, musíte podniknout kroky ke globalizaci aplikace. Pokyny k přidání desetinné čárky najdete v tomto [problému GitHub 4076.](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)

Vlastnost `Layout` je nastavena v souboru *Pages/_ViewStart.cshtml:*

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Předchozí značky nastaví soubor rozvržení na *Pages/Shared/_Layout.cshtml* pro všechny soubory Razor ve složce *Pages.* Další informace najdete [v tématu Rozložení.](xref:razor-pages/index#layout)

### <a name="the-create-page-model"></a>Vytvořit model stránky

Prohlédněte si model stránky *Pages/Movies/Create.cshtml.cs:*

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Metoda `OnGet` inicializuje libovolný stav potřebný pro stránku. Create stránka nemá žádný stav inicializovat, `Page` tak je vrácena. Později v kurzu je `OnGet` zobrazen příklad inicializace stavu. Metoda `Page` vytvoří `PageResult` objekt, který vykreslí *create.cshtml* stránku.

Vlastnost `Movie` používá `[BindProperty]` atribut opt-in pro [vazby modelu](xref:mvc/models/model-binding). Když formulář Vytvořit zaúčtuje hodnoty formuláře, ASP.NET core runtime `Movie` sváže zaúčtované hodnoty s modelem.

Metoda `OnPostAsync` je spuštěna, když stránka odešle data formuláře:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Pokud dojde k chybám modelu, formulář se znovu zobrazí spolu se všemi zaúčtovanými daty formuláře. Většina chyb modelu může být zachycena na straně klienta před zaúčtováním formuláře. Příkladem chyby modelu je zaúčtování hodnoty pro pole data, které nelze převést na datum. Ověření na straně klienta a ověření modelu jsou popsány dále v kurzu.

Pokud nejsou žádné chyby modelu, data se uloží a prohlížeč je přesměrován na stránku Index.

### <a name="the-create-razor-page"></a>Stránka Vytvořit holicí strojek

Prohlédněte si stránkovací soubor *Pages/Movies/Create.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio zobrazí následující značky v rozlišovací tučné písmo používané pro tag pomocné síly:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Zobrazení VS17 na stránce Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V předchozích značkách jsou zobrazeny následující pomocné spoje značek:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Visual Studio zobrazí následující značky v rozlišovací tučné písmo používané pro tag pomocné síly:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

Tento `<form method="post">` prvek je [pomocník značek formulářů](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocník s značkami formuláře automaticky obsahuje [token antiforgery](xref:security/anti-request-forgery).

Modul lešení vytvoří značky Razor pro každé pole v modelu (kromě ID) podobné následujícímu:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

Pomocníci [značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ověření`<div asp-validation-summary` `<span asp-validation-for`(a) zobrazují chyby ověření. Ověření je podrobněji popsáno dále v této sérii.

Pomocník [značky popisků](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje `for` popisek `Title` a atribut vlastnosti.

Pomocník [vstupníznačky](xref:mvc/views/working-with-forms) `<input asp-for="Movie.Title" class="form-control">`( ) používá [atributy DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.

Další informace o pomocné `<form method="post">`spoje značek, například , naleznete [v tématu Pomocníci značek v ASP.NET jádru](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: Databáze](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz zkoumá Razor stránky vytvořené lešení v [předchozím kurzu](xref:tutorials/razor-pages/model).

[Prohlédněte si nebo stáhněte](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) ukázku.

## <a name="the-create-delete-details-and-edit-pages"></a>Stránky Vytvořit, Odstranit, Podrobnosti a Upravit

Prohlédněte si stránkový model *Pages/Movies/Index.cshtml.cs:*

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor Stránky jsou `PageModel`odvozeny od . Podle konvence `PageModel`-derived třídy `<PageName>Model`se nazývá . Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) `RazorPagesMovieContext` k přidání na stránku. Všechny lešení stránky následovat tento vzor. Další informace o asynchronním programování pomocí entity Framework naleznete v [tématu Asynchronní kód.](xref:data/ef-rp/intro#asynchronous-code)

Když je požadavek na stránku, `OnGetAsync` metoda vrátí seznam filmů na razor page. `OnGetAsync`nebo `OnGet` je volána na Razor Page inicializovat stav stránky. V tomto `OnGetAsync` případě získá seznam filmů a zobrazí je.

Při `OnGet` `void` vrácení `OnGetAsync` `Task`nebo vrátí , je použita žádná metoda vrácení. Pokud je `IActionResult` návratový `Task<IActionResult>`typ nebo , musí být zadán příkaz return. Například metoda *Pages/Movies/Create.cshtml.cs:* `OnPostAsync`

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Prohlédněte si *stránku Pages/Movies/Index.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Holicí strojek může přejít z HTML do Jazyka C# nebo do značky specifické pro razor. Pokud `@` symbol následuje [razor vyhrazené klíčové slovo](xref:mvc/views/razor#razor-reserved-keywords), přechody do razor-specifické značky, jinak se přejde do C#.

Razor `@page` směrnice vytvoří soubor do akce MVC, což znamená, že může zpracovávat požadavky. `@page`musí být první direktivou Razor na stránce. `@page`je příkladem přechodu na značky specifické pro Razor. Další informace naleznete v [tématu Syntaxe břitvy.](xref:mvc/views/razor#razor-syntax)

Zkontrolujte výraz lambda použitý v následujícím pomocném prvku HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Pomocník `DisplayNameFor` HTML zkontroluje `Title` vlastnost, na kterou odkazuje výraz lambda, a určí tak zobrazovaný název. Lambda výraz je kontrolována spíše než vyhodnocena. To znamená, že neexistuje `model` `model.Movie`žádné `model.Movie[0]` narušení `null` přístupu, když , , nebo jsou nebo prázdné. Při vyhodnocení výrazu lambda (například s `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnoceny hodnoty vlastností modelu.

<a name="md"></a>

### <a name="the-model-directive"></a>Směrnice @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Směrnice `@model` určuje typ modelu předaného razor Page. V předchozím příkladu `@model` řádek zpřístupňuje `PageModel`-derived třídy Razor Page. Model se používá `@Html.DisplayNameFor` v `@Html.DisplayFor` a [HTML pomocníky](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.

### <a name="the-layout-page"></a>Stránka rozložení

Vyberte odkazy na nabídky **(RazorPagesMovie**, **Home**a **Privacy**). Každá stránka zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout.cshtml.* Otevřete soubor *Pages/Shared/_Layout.cshtml.*

[Šablony rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejnerů HTML webu na jednom místě a pak ho použít na více stránkách webu. Najděte `@RenderBody()` linku. `RenderBody`je zástupný symbol, ve kterém se zobrazují všechna vytvářená zobrazení specifická pro *stránku, která jsou zabalena* na stránce rozložení. Pokud například vyberete odkaz **Soukromí,** zobrazení **Stránky/Privacy.cshtml** se `RenderBody` vykreslí uvnitř metody.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData a rozložení

Zvažte následující kód ze souboru *Pages/Movies/Index.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Předchozí zvýrazněný kód je příkladem razor přechodu do Jazyka C#. Znaky `{` `}` a uzavřete blok kódu Jazyka C#.

Základní `PageModel` třída má `ViewData` vlastnost dictionary, kterou lze použít k přidání dat, která chcete předat zobrazení. Objekty přidáte `ViewData` do slovníku pomocí vzoru klíč/hodnota. V předchozí ukázce je do slovníku `ViewData` přidána vlastnost "Title".

Vlastnost "Název" se používá v souboru *Pages/Shared/_Layout.cshtml.* Následující značky zobrazují několik prvních řádků souboru *_Layout.cshtml.*

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Řádek `@*Markup removed for brevity.*@` je razor komentář, který se nezobrazí v souboru rozložení. Na rozdíl`<!-- -->`od html komentáře ( ), Razor komentáře nejsou odesílány klientovi.

### <a name="update-the-layout"></a>Aktualizace rozložení

Změňte `<title>` prvek v souboru *Pages/Shared/_Layout.cshtml* tak, aby zobrazoval **film** místo **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

V souboru *Stránky/Sdílené/_Layout.cshtml* najdete následující prvek ukotvení.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Nahraďte předchozí prvek následujícíznačkou.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Předchozí prvek ukotvení je [Tag Helper](xref:mvc/views/tag-helpers/intro). V tomto případě je to [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Tag `asp-page="/Movies/Index"` Helper atribut a hodnota vytvoří `/Movies/Index` odkaz na holicí strojek stránku. Hodnota `asp-area` atributu je prázdná, takže oblast není použita v odkazu. Další informace naleznete v [části Oblasti.](xref:mvc/controllers/areas)

Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie.** Pokud máte nějaké problémy, podívejte se na soubor [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) v GitHubu.

Otestujte ostatní odkazy **(Domů**, **RpMovie**, **Vytvořit**, **Upravit**a **Odstranit).** Každá stránka nastaví název, který můžete vidět na kartě prohlížeče. Když záložku přidáte do záložek, použije se pro záložku název.

> [!NOTE]
> Je možné, že do pole nebudete moci `Price` zadat desetinná čárka. Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a formáty dat mimo americkou angličtinu, musíte podniknout kroky ke globalizaci aplikace. Tento [problém GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) pro pokyny k přidání desetinné čárky.

Vlastnost `Layout` je nastavena v souboru *Pages/_ViewStart.cshtml:*

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Předchozí značky nastaví soubor rozvržení na *Pages/Shared/_Layout.cshtml* pro všechny soubory Razor ve složce *Pages.* Další informace najdete [v tématu Rozložení.](xref:razor-pages/index#layout)

### <a name="the-create-page-model"></a>Vytvořit model stránky

Prohlédněte si model stránky *Pages/Movies/Create.cshtml.cs:*

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Metoda `OnGet` inicializuje libovolný stav potřebný pro stránku. Create stránka nemá žádný stav inicializovat, `Page` tak je vrácena. Později v kurzu `OnGet` uvidíte, že metoda inicializovat stav. Metoda `Page` vytvoří `PageResult` objekt, který vykreslí *create.cshtml* stránku.

Vlastnost `Movie` používá `[BindProperty]` atribut opt-in pro [vazby modelu](xref:mvc/models/model-binding). Když formulář Vytvořit zaúčtuje hodnoty formuláře, ASP.NET core runtime `Movie` sváže zaúčtované hodnoty s modelem.

Metoda `OnPostAsync` je spuštěna, když stránka odešle data formuláře:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Pokud dojde k chybám modelu, formulář se znovu zobrazí spolu se všemi zaúčtovanými daty formuláře. Většina chyb modelu může být zachycena na straně klienta před zaúčtováním formuláře. Příkladem chyby modelu je zaúčtování hodnoty pro pole data, které nelze převést na datum. Ověření na straně klienta a ověření modelu jsou popsány dále v kurzu.

Pokud nejsou žádné chyby modelu, data se uloží a prohlížeč je přesměrován na stránku Index.

### <a name="the-create-razor-page"></a>Stránka Vytvořit holicí strojek

Prohlédněte si stránkovací soubor *Pages/Movies/Create.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio `<form method="post">` zobrazí značku výrazným tučným písmem používaným pro pomocné síly značek:

![Zobrazení VS17 na stránce Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Další informace o pomocné `<form method="post">`spoje značek, například , naleznete [v tématu Pomocníci značek v ASP.NET jádru](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Visual Studio pro `<form method="post">` Mac zobrazí značku výrazným tučným písmem používaným pro pomocné rutiny značek.

---

Tento `<form method="post">` prvek je [pomocník značek formulářů](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocník s značkami formuláře automaticky obsahuje [token antiforgery](xref:security/anti-request-forgery).

Modul lešení vytvoří značky Razor pro každé pole v modelu (kromě ID) podobné následujícímu:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

Pomocníci [značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ověření`<div asp-validation-summary` `<span asp-validation-for`(a) zobrazují chyby ověření. Ověření je podrobněji popsáno dále v této sérii.

Pomocník [značky popisků](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje `for` popisek `Title` a atribut vlastnosti.

Pomocník [vstupníznačky](xref:mvc/views/working-with-forms) `<input asp-for="Movie.Title" class="form-control">`( ) používá [atributy DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: Databáze](xref:tutorials/razor-pages/sql)

::: moniker-end
