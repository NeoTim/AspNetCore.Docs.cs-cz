---
title: Vygenerované stránky Razor v ASP.NET Core
author: rick-anderson
description: Vysvětluje stránky Razor generovaných generování uživatelského rozhraní.
ms.author: riande
ms.date: 04/06/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: 2b8b9cde5a37a0754ca177cfc80163e2ffd2925b
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468786"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a>Vygenerované stránky Razor v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz zkoumá stránky Razor vytvořené generování uživatelského rozhraní v předchozím kurzu.

[Zobrazení nebo stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) vzorku.

## <a name="the-create-delete-details-and-edit-pages"></a>Vytvoření, odstranění, podrobností a upravit stránky

Zkontrolujte *Pages/Movies/Index.cshtml.cs* Model stránky:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Stránky Razor jsou odvozeny z `PageModel`. Podle konvence `PageModel`-odvozené třídy se nazývá `<PageName>Model`. Konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) přidáte `RazorPagesMovieContext` na stránku. Vygenerované stránky postupovat podle tohoto vzoru. Zobrazit [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) Další informace o asynchronní programování s Entity Framework.

Po odeslání žádosti pro stránku, `OnGetAsync` metoda vrátí seznam hodnot filmy pro stránky Razor. `OnGetAsync` nebo `OnGet` je volán na stránku Razor k inicializaci stavu stránky. V takovém případě `OnGetAsync` získá seznam filmy a zobrazí je.

Když `OnGet` vrátí `void` nebo `OnGetAsync` vrátí`Task`, žádná vrácená metoda se používá. Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>`, musí být zadaný příkaz return. Například *Pages/Movies/Create.cshtml.cs* `OnPostAsync` metody:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Zkontrolujte *Pages/Movies/Index.cshtml* stránky Razor:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor můžete přejít z HTML do jazyka C# nebo do kódu specifické pro syntaxi Razor. Když `@` následuje symbol [Razor rezervované klíčové slovo](xref:mvc/views/razor#razor-reserved-keywords), bude přecházet do kódu specifické pro Razor, jinak bude přecházet do jazyka C#.

`@page` Direktivu Razor vytvoří soubor do akce MVC, což znamená, že dokáže zpracovat požadavky. `@page` musí být první direktivy Razor na stránce. `@page` je příkladem přechod do kódu specifické pro syntaxi Razor. Zobrazit [syntaxe Razor](xref:mvc/views/razor#razor-syntax) Další informace.

Prozkoumejte výrazu lambda použít v následujících pomocné rutiny HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

`DisplayNameFor` Zkontroluje pomocné rutiny HTML `Title` vlastnost se odkazuje ve výrazu lambda lze zjistit název zobrazení. Výraz lambda je zkontroloval spíše než vyhodnocen. To znamená, že neexistuje žádná narušení přístupu při `model`, `model.Movie`, nebo `model.Movie[0]` jsou `null` nebo je prázdný. Při vyhodnocování výrazu lambda (třeba index Mei `@Html.DisplayFor(modelItem => item.Title)`), jsou vyhodnocovány hodnoty vlastností modelu.

<a name="md"></a>

### <a name="the-model-directive"></a>@model – Direktiva

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model` Direktiva Určuje typ modelu předané do stránky Razor. V předchozím příkladu `@model` řádek provede `PageModel`-odvozené třídy, které jsou k dispozici pro stránky Razor. Model se používá v `@Html.DisplayNameFor` a `@Html.DisplayFor` [pomocných rutin HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.

### <a name="the-layout-page"></a>Stránka rozložení

Vyberte nabídky odkazy (**RazorPagesMovie**, **Domů**, a **ochrany osobních údajů**). Každá stránka zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementována v *Pages/Shared/_Layout.cshtml* souboru. Otevřít *Pages/Shared/_Layout.cshtml* souboru.

[Rozložení](xref:mvc/views/layout) šablony umožňují zadat kontejner rozložení HTML vašeho webu na jednom místě a použijte ji na několika stránkách ve vaší lokalitě. Najít `@RenderBody()` řádku. `RenderBody` je zástupný symbol, kde všechny specifický pro stránku zobrazení, se kterými create, show *zabalené* stránce rozložení. Například, pokud jste vybrali **ochrany osobních údajů** odkaz, **Pages/Privacy.cshtml** je zobrazení vykresleno uvnitř `RenderBody` metody.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData a rozložení

Vezměte v úvahu následující kód z *Pages/Movies/Index.cshtml* souboru:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Předchozí zvýrazněný kód je příkladem Razor převádějí do jazyka C#. `{` a `}` znaky, uzavřete blok kódu jazyka C#.

`PageModel` Má základní třída `ViewData` slovník vlastností, který slouží k přidání dat, které chcete předat do zobrazení. Přidání objektů do `ViewData` slovník pomocí vzoru klíč/hodnota. V předchozím příkladu je přidána vlastnost "Title" `ViewData` slovníku.

Vlastnost "Title" se používá v *Pages/Shared/_Layout.cshtml* souboru. Následující kód ukazuje několik prvních řádků tohoto *_Layout.cshtml* souboru.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Na řádku `@*Markup removed for brevity.*@` je komentáře syntaxe Razor, která se nezobrazí v souboru rozložení. Na rozdíl od komentáře HTML (`<!-- -->`), klient se nebude posílat komentáře syntaxe Razor.

### <a name="update-the-layout"></a>Aktualizace rozložení

Změnit `<title>` element v *Pages/Shared/_Layout.cshtml* soubor k zobrazení **film** spíše než **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Vyhledejte následující element anchor v *Pages/Shared/_Layout.cshtml* souboru.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Nahraďte následující značky předchozí prvek.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Předchozí element anchor je [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro). V tomto případě má [ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). `asp-page="/Movies/Index"` Pomocné rutiny značky atribut a hodnota vytvoří odkaz `/Movies/Index` stránky Razor. `asp-area` Hodnota atributu je prázdný, takže oblasti se nepoužívá v odkazu. Zobrazit [oblasti](xref:mvc/controllers/areas) Další informace.

Uložte změny a otestujte aplikaci po kliknutí na **RpMovie** odkaz. Zobrazit [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) souboru v Githubu, pokud máte potíže s.

Testování propojení (**Domů**, **RpMovie**, **vytvořit**, **upravit**, a **odstranit**). Každá stránka nastaví nadpis, který se zobrazí na záložce prohlížeče. Když vytvoříte záložku na stránce, název se používá pro záložky.

> [!NOTE]
> Není možné zadat desetinné čárky v `Price` pole. Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") pro desetinné čárky a USA retweetovat neanglické formáty kalendářního data, je nutné provést kroky aplikaci poslali do světa. To [problém Githubu 4076](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420) pokyny k přidání desetinné čárky.

`Layout` Je nastavena *Pages/_ViewStart.cshtml* souboru:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Předchozí kód nastaví soubor rozložení *Pages/Shared/_Layout.cshtml* pro všechny soubory Razor pod *stránky* složky. Zobrazit [rozložení](xref:razor-pages/index#layout) Další informace.

### <a name="the-create-page-model"></a>Vytvořit model stránky

Zkontrolujte *Pages/Movies/Create.cshtml.cs* model stránky:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet` Metoda inicializuje některému ze stavů potřebné pro stránku. Stránka pro vytvoření nemá žádné stavu inicializace, tak `Page` je vrácena. Později v tomto kurzu uvidíte `OnGet` metodu inicializace stavu. `Page` Metoda vytvoří `PageResult` objekt, který vykreslí *Create.cshtml* stránky.

`Movie` Používá vlastnost `[BindProperty]` atribut pro přihlášení k [vazby modelu](xref:mvc/models/model-binding). Kdy vytvořit formulář pošle příspěvek s hodnot formuláře, modul runtime ASP.NET Core váže odeslaných hodnoty, které mají `Movie` modelu.

`OnPostAsync` Metody se spustí, když se publikuje data formuláře na stránce:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Pokud nejsou žádné chyby modelu, formulář se zobrazí znovu, spolu se všechna data formuláře. Většina chyb modelu může být zachycena na straně klienta, před odesláním formuláře. Příklad chybu modelu účtování hodnotu pro pole data, která se nedá převést na datum. Ověřování na straně klienta a ověření modelu jsou popsány dále v tomto kurzu.

Pokud nejsou žádné chyby modelu, data se uloží a bude prohlížeč přesměrován na indexovou stránku.

### <a name="the-create-razor-page"></a>Vytvoření stránky Razor

Zkontrolujte *Pages/Movies/Create.cshtml* Razor stránkovacího souboru:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio zobrazí `<form method="post">` značku rozlišovací tučné písmo použité pro pomocné rutiny značek:

![VS17 zobrazení Create.cshtml stránky](page/_static/th.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Další informace o pomocných rutin značek, jako `<form method="post">`, naleznete v tématu [pomocných rutin značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Visual Studio pro Mac zobrazí `<form method="post">` značku rozlišovací tučné písmo použité pro pomocné rutiny značek.

---

`<form method="post">` Prvek je [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocná rutina značky formuláře se automaticky zahrne [antiforgery token](xref:security/anti-request-forgery).

Modul generování uživatelského rozhraní vytvoří kód Razor pro každé pole v modelu (s výjimkou ID) podobný následujícímu:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocných rutin značek ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazení chyb ověřování. Ověření se budeme věnovat jednotlivě podrobněji dále v této sérii.

[Pomocné rutiny značky popisek](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje titulek popisek a `for` atribut pro `Title` vlastnost.

[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta.

## <a name="additional-resources"></a>Další zdroje

* [Verzi tohoto kurzu na webu YouTube](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: Database](xref:tutorials/razor-pages/sql)
