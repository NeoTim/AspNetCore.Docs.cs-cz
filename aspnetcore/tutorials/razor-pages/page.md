---
title: Část 3, vygenerované Razor stránky v ASP.NET Core
author: rick-anderson
description: Třetí část série kurzů na Razor stránkách.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 6195982f902c17d835d2675c1231eed347d603c2
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652812"
---
# <a name="part-3-scaffolded-razor-pages-in-aspnet-core"></a>Část 3, vygenerované Razor stránky v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se podíváme na Razor stránky vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Stránky vytvořit, odstranit, podrobnosti a upravit

Projděte si model stránky *stránky/filmy/index. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

RazorStránky jsou odvozeny z `PageModel` . Podle konvence `PageModel` je volána třída odvozená od třídy `<PageName>Model` . Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání na `RazorPagesMovieContext` stránku. Všechny vygenerované stránky následují tento model. Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .

Když je vytvořena žádost pro stránku, `OnGetAsync` Metoda vrátí seznam filmů na Razor stránku. `OnGetAsync`nebo `OnGet` se volá k inicializaci stavu stránky. V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.

Při vrácení nebo vrácení se `OnGet` `void` `OnGetAsync` `Task` nepoužije žádný návratový příkaz. Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>` , je nutné poskytnout návratový příkaz. Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Projděte stránku *stránky/filmy/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razormůže přejít z formátu HTML do jazyka C# nebo do Razor specifického kódu. `@`Je-li symbol následován [ Razor vyhrazeným klíčovým slovem](xref:mvc/views/razor#razor-reserved-keywords), přechází na Razor konkrétní značku, jinak přejde do jazyka C#.

### <a name="the-page-directive"></a>@pageDirektiva

Tato `@page` Razor direktiva vytvoří soubor akce MVC, což znamená, že může zpracovávat požadavky. `@page`musí se jednat o první Razor direktivu na stránce. `@page`je příkladem přechodu na Razor konkrétní značku. Další informace najdete v tématu [ Razor syntaxe](xref:mvc/views/razor#razor-syntax) .

Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

`DisplayNameFor`Pomocník HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu. Výraz lambda je zkontrolován namísto vyhodnocení. To znamená, že nedojde k narušení přístupu `model` , pokud `model.Movie` je, nebo `model.Movie[0]` je `null` nebo prázdné. Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)` ) jsou vyhodnocovány hodnoty vlastností modelu.

<a name="md"></a>

### <a name="the-model-directive"></a>@modelDirektiva

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model`Direktiva určuje typ modelu předaného na Razor stránku. V předchozím příkladu `@model` čára zpřístupňuje `PageModel` třídu odvozenou pro Razor stránku. Model se používá v `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a na stránce.

### <a name="the-layout-page"></a>Stránka rozložení

Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* . Otevřete soubor *stránky/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují, aby rozložení kontejneru HTML bylo:

* Zadáno na jednom místě.
* Použito na více stránkách v lokalitě.

Najděte `@RenderBody()` řádek. `RenderBody`je zástupný symbol, kde jsou všechna zobrazení konkrétní stránky zobrazena, *zabalena* na stránce rozložení. Například vyberte odkaz **osobních údajů** a zobrazení *stránky/soukromí. cshtml* je vykresleno uvnitř `RenderBody` metody.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData a rozložení

Vezměte v úvahu následující značky ze souboru *Pages/Movies/index. cshtml* :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Předchozí zvýrazněný kód je příkladem Razor přechodu do jazyka C#. `{`Znaky a jsou `}` uzavřeny v bloku kódu jazyka C#.

`PageModel`Základní třída obsahuje `ViewData` vlastnost Dictionary, kterou lze použít k předání dat do zobrazení. Objekty jsou přidány do `ViewData` slovníku pomocí vzoru klíč/hodnota. V předchozí ukázce `"Title"` je vlastnost přidána do `ViewData` slovníku.

Tato `"Title"` vlastnost se používá v souboru *Pages/Shared/_Layout. cshtml* . Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

Řádek `@*Markup removed for brevity.*@` je Razor komentář. Na rozdíl od komentářů jazyka HTML ( `<!-- -->` ) nejsou Razor Komentáře odesílány klientovi.

### <a name="update-the-layout"></a>Aktualizovat rozložení

Změňte `<title>` prvek v souboru *Pages/Shared/_Layout. cshtml* tak, aby se místo **RazorPagesMovie**zobrazoval **film** .

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Nahraďte předchozí prvek následujícím kódem:

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro). V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). `asp-page="/Movies/Index"`Atribut pomocník značek a hodnota vytvoří odkaz na `/Movies/Index` Razor stránku. `asp-area`Hodnota atributu je prázdná, takže se oblast nepoužívá v odkazu. Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .

Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** . Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) na GitHubu.

Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**). Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.

> [!NOTE]
> V poli možná nebudete moct zadat desítkové čárky `Price` . Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace. Pokyny k přidání desetinné čárky najdete v tomto [problému v githubu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .

`Layout`Vlastnost je nastavena v souboru *Pages/_ViewStart. cshtml* :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny Razor soubory ve složce *Pages* . Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .

### <a name="the-create-page-model"></a>Model vytvoření stránky

Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet`Metoda inicializuje libovolný stav potřebný pro stránku. Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí. Později v tomto kurzu `OnGet` se zobrazuje příklad stavu inicializace. `Page`Metoda vytvoří `PageResult` objekt, který vykresluje stránku *vytvořit. cshtml* .

`Movie`Vlastnost používá `[BindProperty]` atribut pro přihlášení k [modelu vazby](xref:mvc/models/model-binding). Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.

`OnPostAsync`Metoda se spustí, když stránka publikuje data formuláře:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře. Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře. Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum. Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.

Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.

### <a name="the-create-razor-page"></a>Stránka pro vytvoření Razor

Prověřte stránku *stránky/filmy/vytvořit soubor. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V předchozím kódu se zobrazují následující pomocníky značek:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

`<form method="post">`Element je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).

Modul generování uživatelského rozhraní vytvoří Razor značky pro každé pole v modelu (s výjimkou identifikátoru), který je podobný následujícímu:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` a `<span asp-validation-for` ) zobrazí chyby ověřování. Ověřování je podrobněji popsáno dále v této sérii.

[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) vygeneruje popisek a `for` atribut pro `Title` vlastnost.

[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.

Další informace o Pomocníkech značek `<form method="post">` , jako je, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: Přidání modelu](xref:tutorials/razor-pages/model) 
>  [Další: databáze](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se podíváme na Razor stránky vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).

[Zobrazit nebo stáhnout](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) ukázku.

## <a name="the-create-delete-details-and-edit-pages"></a>Stránky vytvořit, odstranit, podrobnosti a upravit

Projděte si model stránky *stránky/filmy/index. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

RazorStránky jsou odvozeny z `PageModel` . Podle konvence `PageModel` je volána třída odvozená od třídy `<PageName>Model` . Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání na `RazorPagesMovieContext` stránku. Všechny vygenerované stránky následují tento model. Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .

Když je vytvořena žádost pro stránku, `OnGetAsync` Metoda vrátí seznam filmů na Razor stránku. `OnGetAsync`nebo `OnGet` je volána na Razor stránce pro inicializaci stavu stránky. V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.

Při vrácení nebo vrácení se `OnGet` `void` `OnGetAsync` `Task` nepoužije žádná návratová metoda. Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>` , je nutné poskytnout návratový příkaz. Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Projděte stránku *stránky/filmy/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razormůže přejít z formátu HTML do jazyka C# nebo do Razor specifického kódu. `@`Je-li symbol následován [ Razor vyhrazeným klíčovým slovem](xref:mvc/views/razor#razor-reserved-keywords), přechází na Razor konkrétní značku, jinak přejde do jazyka C#.

`@page` Razor Direktiva vytvoří soubor na akci MVC, což znamená, že může zpracovávat požadavky. `@page`musí se jednat o první Razor direktivu na stránce. `@page`je příkladem přechodu na Razor konkrétní značku. Další informace najdete v tématu [ Razor syntaxe](xref:mvc/views/razor#razor-syntax) .

Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

`DisplayNameFor`Pomocník HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu. Výraz lambda je zkontrolován namísto vyhodnocení. To znamená, že nedojde k narušení přístupu `model` , pokud `model.Movie` `model.Movie[0]` jsou `null` nebo prázdná. Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)` ) jsou vyhodnocovány hodnoty vlastností modelu.

<a name="md"></a>

### <a name="the-model-directive"></a>@modelDirektiva

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model`Direktiva určuje typ modelu předaného na Razor stránku. V předchozím příkladu `@model` čára zpřístupňuje `PageModel` třídu odvozenou pro Razor stránku. Model se používá v `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a na stránce.

### <a name="the-layout-page"></a>Stránka rozložení

Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* . Otevřete soubor *stránky/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu. Najděte `@RenderBody()` řádek. `RenderBody`je zástupný symbol, kde jsou všechna zobrazení pro konkrétní stránku, která vytvoříte, *zabalena* na stránce rozložení. Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **stránky/soukromí. cshtml** se vykreslí uvnitř `RenderBody` metody.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData a rozložení

Vezměte v úvahu následující kód ze souboru *Pages/Movies/index. cshtml* :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Předchozí zvýrazněný kód je příkladem Razor přechodu do jazyka C#. `{`Znaky a jsou `}` uzavřeny v bloku kódu jazyka C#.

`PageModel`Základní třída má `ViewData` vlastnost Dictionary, kterou lze použít k přidání dat, která chcete předat zobrazení. Do slovníku přidáte objekty `ViewData` pomocí vzoru klíč/hodnota. V předchozím příkladu je do slovníku přidána vlastnost title `ViewData` .

Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* . Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Řádek `@*Markup removed for brevity.*@` je komentář, Razor který se nezobrazí v souboru rozložení. Na rozdíl od komentářů jazyka HTML ( `<!-- -->` ) nejsou Razor Komentáře odesílány klientovi.

### <a name="update-the-layout"></a>Aktualizovat rozložení

Změňte `<title>` prvek v souboru *Pages/Shared/_Layout. cshtml* tak, aby se místo **RazorPagesMovie**zobrazoval **film** .

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Nahraďte předchozí prvek následujícím kódem.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro). V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). `asp-page="/Movies/Index"`Atribut pomocník značek a hodnota vytvoří odkaz na `/Movies/Index` Razor stránku. `asp-area`Hodnota atributu je prázdná, takže se oblast nepoužívá v odkazu. Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .

Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** . Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) na GitHubu.

Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**). Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.

> [!NOTE]
> V poli možná nebudete moct zadat desítkové čárky `Price` . Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace. Tento [problém githubu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) najdete v pokynech k přidání desetinné čárky.

`Layout`Vlastnost je nastavena v souboru *Pages/_ViewStart. cshtml* :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny Razor soubory ve složce *Pages* . Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .

### <a name="the-create-page-model"></a>Model vytvoření stránky

Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet`Metoda inicializuje libovolný stav potřebný pro stránku. Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí. Později v tomto kurzu vidíte `OnGet` Stav inicializace metody. `Page`Metoda vytvoří `PageResult` objekt, který vykresluje stránku *vytvořit. cshtml* .

`Movie`Vlastnost používá `[BindProperty]` atribut pro přihlášení k [modelu vazby](xref:mvc/models/model-binding). Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.

`OnPostAsync`Metoda se spustí, když stránka publikuje data formuláře:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře. Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře. Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum. Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.

Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.

### <a name="the-create-razor-page"></a>Stránka pro vytvoření Razor

Prověřte stránku *stránky/filmy/vytvořit soubor. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio zobrazí `<form method="post">` značku v zřetelném tučném písmu, které se používá pro pomocníky značek:

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Další informace o Pomocníkech značek `<form method="post">` , jako je, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Visual Studio pro Mac zobrazí `<form method="post">` značku v zřetelném tučném písmu, které se používá pro pomocníky značek.

---

`<form method="post">`Element je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).

Modul generování uživatelského rozhraní vytvoří Razor značky pro každé pole v modelu (s výjimkou identifikátoru), který je podobný následujícímu:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` a `<span asp-validation-for` ) zobrazí chyby ověřování. Ověřování je podrobněji popsáno dále v této sérii.

[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) vygeneruje popisek a `for` atribut pro `Title` vlastnost.

[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Předchozí: Přidání modelu](xref:tutorials/razor-pages/model) 
>  [Další: databáze](xref:tutorials/razor-pages/sql)

::: moniker-end
