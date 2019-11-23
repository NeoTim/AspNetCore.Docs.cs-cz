---
title: Vygenerované Razor Pages v ASP.NET Core
author: rick-anderson
description: Vysvětluje Razor Pages generovaného pomocí uživatelského rozhraní.
ms.author: riande
ms.date: 08/17/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: 594fd6186cc73aa054fc9a1478850fa01e481ef2
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034201"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a>Vygenerované Razor Pages v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Stránky vytvořit, odstranit, podrobnosti a upravit

Projděte si model stránky *stránky/filmy/index. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor Pages jsou odvozeny z `PageModel`. Podle konvence je třída odvozená od `PageModel`volána `<PageName>Model`. Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro přidání `RazorPagesMovieContext` na stránku. Všechny vygenerované stránky následují tento model. Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .

Když je pro stránku vytvořen požadavek, metoda `OnGetAsync` vrátí seznam filmů na stránku Razor. je volána `OnGetAsync` nebo `OnGet` k inicializaci stavu stránky. V takovém případě `OnGetAsync` získá seznam filmů a zobrazí je.

Když `OnGet` vrátí `void` nebo `OnGetAsync` vrátí`Task`, není použit žádný návratový příkaz. Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz. Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Projděte stránku *stránky/filmy/index. cshtml* Razor:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor může přejít z formátu HTML C# do nebo do kódu specifického pro Razor. Je-li symbol `@` následovaný [rezervovaným klíčovým slovem Razor](xref:mvc/views/razor#razor-reserved-keywords), přejde do kódu specifického pro Razor, jinak přejde C#do.

### <a name="the-page-directive"></a>Direktiva @page

Direktiva `@page` Razor vytvoří soubor akce MVC, což znamená, že může zpracovávat požadavky. `@page` musí být první direktivou Razor na stránce. `@page` je příklad přechodu do kódu specifického pro Razor. Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .

Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Pomocník pro `DisplayNameFor` HTML kontroluje vlastnost `Title`, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu. Výraz lambda je zkontrolován namísto vyhodnocení. To znamená, že při `model`, `model.Movie`nebo `model.Movie[0]` není toto porušení přístupu `null` nebo je prázdné. Při vyhodnocování výrazu lambda (například s `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.

<a name="md"></a>

### <a name="the-model-directive"></a>Direktiva @model

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Direktiva `@model` určuje typ modelu předaný na stránku Razor. V předchozím příkladu `@model` řádek zpřístupňuje třídu odvozenou `PageModel`, která je k dispozici na stránce Razor. Model se používá v `@Html.DisplayNameFor` a `@Html.DisplayFor` v [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.

### <a name="the-layout-page"></a>Stránka rozložení

Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* . Otevřete soubor *stránky/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují, aby rozložení kontejneru HTML bylo:

* Zadáno na jednom místě.
* Použito na více stránkách v lokalitě.

Najděte `@RenderBody()` řádek. `RenderBody` je zástupný symbol, ve kterém se všechna zobrazení pro konkrétní stránky zobrazí, *zabalená* na stránce rozložení. Například vyberte odkaz **osobních údajů** a zobrazení *stránky/soukromí. cshtml* se vykresluje v rámci metody `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData a rozložení

Vezměte v úvahu následující značky ze souboru *Pages/Movies/index. cshtml* :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Předchozí zvýrazněný kód je příkladem přechodu Razor do C#. Znaky `{` a `}` obklopují blok C# kódu.

Základní třída `PageModel` obsahuje vlastnost `ViewData` slovníku, která se dá použít k předání dat do zobrazení. Objekty jsou přidány do `ViewData` slovníku pomocí vzoru klíč/hodnota. V předchozím příkladu je do slovníku `ViewData` přidána vlastnost `"Title"`.

Vlastnost `"Title"` se používá v souboru *Pages/Shared/_Layout. cshtml* . Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

Řádková `@*Markup removed for brevity.*@` je komentář Razor. Na rozdíl od komentářů ve formátu HTML (`<!-- -->`) se do klienta neodesílají komentáře Razor.

### <a name="update-the-layout"></a>Aktualizovat rozložení

Změňte `<title>` prvek v souboru *Pages/Shared/_Layout. cshtml* pro zobrazení **filmu** místo **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Nahraďte předchozí prvek následujícím kódem:

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro). V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Pomocný atribut `asp-page="/Movies/Index"` značky a hodnota vytvoří odkaz na stránku `/Movies/Index` Razor. Hodnota atributu `asp-area` je prázdná, takže se oblast nepoužívá v odkazu. Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .

Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** . Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) na GitHubu.

Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**). Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.

> [!NOTE]
> Není možné zadat desetinné čárky v `Price` pole. Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace. Pokyny k přidání desetinné čárky najdete v tomto [problému v githubu 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .

Vlastnost `Layout` je nastavena v souboru *Pages/_ViewStart. cshtml* :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* . Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .

### <a name="the-create-page-model"></a>Model vytvoření stránky

Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Metoda `OnGet` inicializuje libovolný stav potřebný pro stránku. Stránka pro vytvoření nemá žádný stav k inicializaci, takže se vrátí `Page`. Později v tomto kurzu se zobrazí příklad `OnGet` stav inicializace. Metoda `Page` vytvoří objekt `PageResult`, který vykresluje stránku *vytvořit. cshtml* .

Vlastnost `Movie` používá atribut `[BindProperty]` pro přihlášení k [modelu vazby](xref:mvc/models/model-binding). Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do modelu `Movie`.

Metoda `OnPostAsync` se spustí, když stránka publikuje data formuláře:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře. Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře. Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum. Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.

Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.

### <a name="the-create-razor-page"></a>Stránka vytvoření Razor

Projděte si soubor *Pages/video/Create. cshtml* Razor Page:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V předchozím kódu se zobrazují následující pomocníky značek:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

Element `<form method="post">` je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).

Modul generování uživatelského rozhraní vytvoří pro každé pole v modelu označení Razor (s výjimkou ID), které je podobné následujícímu:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazuje chyby ověřování. Ověřování je podrobněji popsáno dále v této sérii.

[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje popisek popisku a `for` atributu pro vlastnost `Title`.

[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.

Další informace o značkách pomocníka značek, jako je například `<form method="post">`, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Další materiály a zdroje informací

> [!div class="step-by-step"]
> [Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: databáze](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).

[Zobrazení nebo stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) vzorku.

## <a name="the-create-delete-details-and-edit-pages"></a>Stránky vytvořit, odstranit, podrobnosti a upravit

Projděte si model stránky *stránky/filmy/index. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor Pages jsou odvozeny z `PageModel`. Podle konvence je třída odvozená od `PageModel`volána `<PageName>Model`. Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro přidání `RazorPagesMovieContext` na stránku. Všechny vygenerované stránky následují tento model. Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .

Když je pro stránku vytvořen požadavek, metoda `OnGetAsync` vrátí seznam filmů na stránku Razor. `OnGetAsync` nebo `OnGet` je volána na stránce Razor pro inicializaci stavu stránky. V takovém případě `OnGetAsync` získá seznam filmů a zobrazí je.

Když `OnGet` vrátí `void` nebo `OnGetAsync` vrátí`Task`, není použita žádná návratová metoda. Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz. Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Projděte stránku *stránky/filmy/index. cshtml* Razor:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor může přejít z formátu HTML C# do nebo do kódu specifického pro Razor. Je-li symbol `@` následovaný [rezervovaným klíčovým slovem Razor](xref:mvc/views/razor#razor-reserved-keywords), přejde do kódu specifického pro Razor, jinak přejde C#do.

Direktiva `@page` Razor vytvoří soubor na akci MVC, což znamená, že může zpracovávat požadavky. `@page` musí být první direktivou Razor na stránce. `@page` je příklad přechodu do kódu specifického pro Razor. Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .

Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Pomocník pro `DisplayNameFor` HTML kontroluje vlastnost `Title`, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu. Výraz lambda je zkontrolován namísto vyhodnocení. To znamená, že při `model`, `model.Movie`nebo `model.Movie[0]` nejsou `null` nebo jsou prázdné žádné porušení přístupu. Při vyhodnocování výrazu lambda (například s `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.

<a name="md"></a>

### <a name="the-model-directive"></a>Direktiva @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Direktiva `@model` určuje typ modelu předaný na stránku Razor. V předchozím příkladu `@model` řádek zpřístupňuje třídu odvozenou `PageModel`, která je k dispozici na stránce Razor. Model se používá v `@Html.DisplayNameFor` a `@Html.DisplayFor` v [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.

### <a name="the-layout-page"></a>Stránka rozložení

Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* . Otevřete soubor *stránky/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu. Najděte `@RenderBody()` řádek. `RenderBody` je zástupný symbol, kde jsou všechna zobrazení, která vytvoříte, *zabalena* na stránce rozložení. Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **stránky/soukromí. cshtml** se vykreslí v rámci metody `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData a rozložení

Vezměte v úvahu následující kód ze souboru *Pages/Movies/index. cshtml* :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Předchozí zvýrazněný kód je příkladem přechodu Razor do C#. Znaky `{` a `}` obklopují blok C# kódu.

Základní třída `PageModel` obsahuje vlastnost `ViewData` slovníku, kterou lze použít k přidání dat, která chcete předat zobrazení. Do slovníku `ViewData` přidáte objekty pomocí vzoru klíč/hodnota. V předchozím příkladu je do slovníku `ViewData` přidána vlastnost title.

Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* . Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Řádková `@*Markup removed for brevity.*@` je komentář Razor, který se v souboru rozložení nezobrazí. Na rozdíl od komentářů ve formátu HTML (`<!-- -->`) se do klienta neodesílají komentáře Razor.

### <a name="update-the-layout"></a>Aktualizovat rozložení

Změňte `<title>` prvek v souboru *Pages/Shared/_Layout. cshtml* pro zobrazení **filmu** místo **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Nahraďte předchozí prvek následujícím kódem.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro). V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Pomocný atribut `asp-page="/Movies/Index"` značky a hodnota vytvoří odkaz na stránku `/Movies/Index` Razor. Hodnota atributu `asp-area` je prázdná, takže se oblast nepoužívá v odkazu. Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .

Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** . Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) na GitHubu.

Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**). Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.

> [!NOTE]
> Není možné zadat desetinné čárky v `Price` pole. Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace. Tento [problém githubu 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) najdete v pokynech k přidání desetinné čárky.

Vlastnost `Layout` je nastavena v souboru *Pages/_ViewStart. cshtml* :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* . Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .

### <a name="the-create-page-model"></a>Model vytvoření stránky

Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Metoda `OnGet` inicializuje libovolný stav potřebný pro stránku. Stránka pro vytvoření nemá žádný stav k inicializaci, takže se vrátí `Page`. Později v tomto kurzu vidíte `OnGet` stav inicializace metody. Metoda `Page` vytvoří objekt `PageResult`, který vykresluje stránku *vytvořit. cshtml* .

Vlastnost `Movie` používá atribut `[BindProperty]` pro přihlášení k [modelu vazby](xref:mvc/models/model-binding). Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do modelu `Movie`.

Metoda `OnPostAsync` se spustí, když stránka publikuje data formuláře:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře. Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře. Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum. Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.

Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.

### <a name="the-create-razor-page"></a>Stránka vytvoření Razor

Projděte si soubor *Pages/video/Create. cshtml* Razor Page:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio zobrazí značku `<form method="post">` v zřetelném tučném písmu, které se používá pro pomocníky značek:

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Další informace o značkách pomocníka značek, jako je například `<form method="post">`, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Visual Studio pro Mac zobrazuje značku `<form method="post">` v zřetelném tučném písmu, které se používá pro pomocníky značek.

---

Element `<form method="post">` je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).

Modul generování uživatelského rozhraní vytvoří pro každé pole v modelu označení Razor (s výjimkou ID), které je podobné následujícímu:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazuje chyby ověřování. Ověřování je podrobněji popsáno dále v této sérii.

[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje popisek popisku a `for` atributu pro vlastnost `Title`.

[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Verze YouTube tohoto kurzu](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: databáze](xref:tutorials/razor-pages/sql)

::: moniker-end
