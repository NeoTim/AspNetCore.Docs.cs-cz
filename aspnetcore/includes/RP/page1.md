# <a name="scaffolded-razor-pages-in-aspnet-core"></a>Vygenerované stránky Razor v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz zkoumá stránky Razor vytvořené generování uživatelského rozhraní v předchozím kurzu.

[Zobrazení nebo stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21) vzorku.

## <a name="the-create-delete-details-and-edit-pages"></a>Vytvoření, odstranění, podrobností a upravit stránky.

Zkontrolujte *Pages/Movies/Index.cshtml.cs* Model stránky:

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index21.cshtml.cs)]

::: moniker-end

Stránky Razor jsou odvozeny z `PageModel`. Podle konvence `PageModel`-odvozené třídy se nazývá `<PageName>Model`. Konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) přidáte `MovieContext` na stránku. Vygenerované stránky postupovat podle tohoto vzoru. Zobrazit [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) Další informace o asynchronní programování s Entity Framework.

Po odeslání žádosti pro stránku, `OnGetAsync` metoda vrátí seznam hodnot filmy pro stránky Razor. `OnGetAsync` nebo `OnGet` je volán na stránku Razor k inicializaci stavu stránky. V takovém případě `OnGetAsync` získá seznam filmy a zobrazí je.

Když `OnGet` vrátí `void` nebo `OnGetAsync` vrátí`Task`, žádná vrácená metoda se používá. Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>`, musí být zadaný příkaz return. Například *Pages/Movies/Create.cshtml.cs* `OnPostAsync` metody:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Zkontrolujte *Pages/Movies/Index.cshtml* stránky Razor:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor můžete přejít z HTML do jazyka C# nebo do kódu specifické pro syntaxi Razor. Když `@` následuje symbol [Razor rezervované klíčové slovo](xref:mvc/views/razor#razor-reserved-keywords), bude přecházet do kódu specifické pro Razor, jinak bude přecházet do jazyka C#.

`@page` Direktivu Razor vytvoří soubor do akce MVC &mdash; to znamená, že dokáže zpracovat požadavky. `@page` musí být první direktivy Razor na stránce. `@page` je příkladem přechod do kódu specifické pro syntaxi Razor. Zobrazit [syntaxe Razor](xref:mvc/views/razor#razor-syntax) Další informace.

Prozkoumejte výrazu lambda použít v následujících pomocné rutiny HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

`DisplayNameFor` Zkontroluje pomocné rutiny HTML `Title` vlastnost se odkazuje ve výrazu lambda lze zjistit název zobrazení. Výraz lambda je zkontroloval spíše než vyhodnocen. To znamená, že neexistuje žádná narušení přístupu při `model`, `model.Movie`, nebo `model.Movie[0]` jsou `null` nebo je prázdný. Při vyhodnocování výrazu lambda (třeba index Mei `@Html.DisplayFor(modelItem => item.Title)`), jsou vyhodnocovány hodnoty vlastností modelu.

<a name="md"></a>

### <a name="the-model-directive"></a>@model – Direktiva

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model` Direktiva Určuje typ modelu předané do stránky Razor. V předchozím příkladu `@model` řádek provede `PageModel`-odvozené třídy, které jsou k dispozici pro stránky Razor. Model se používá v `@Html.DisplayNameFor` a `@Html.DisplayFor` [pomocných rutin HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.

<!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData a rozložení

Vezměte v úvahu následující kód:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Předchozí zvýrazněný kód je příkladem Razor převádějí do jazyka C#. `{` a `}` znaky, uzavřete blok kódu jazyka C#.

`PageModel` Má základní třída `ViewData` slovník vlastností, který slouží k přidání dat, které chcete předat do zobrazení. Přidání objektů do `ViewData` slovník pomocí vzoru klíč/hodnota. V předchozím příkladu je přidána vlastnost "Title" `ViewData` slovníku.

::: moniker range="= aspnetcore-2.0"

Vlastnost "Title" se používá v *Pages/Shared/_Layout.cshtml* souboru. Následující kód ukazuje několik prvních řádků tohoto *Pages/Shared/_Layout.cshtml* souboru.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Vlastnost "Title" se používá v *Pages/Shared/_Layout.cshtml* souboru. Následující kód ukazuje několik prvních řádků tohoto *_Layout.cshtml* souboru.

::: moniker-end

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-999)]

Na řádku `@*Markup removed for brevity.*@` je komentáře syntaxe Razor. Na rozdíl od komentáře HTML (`<!-- -->`), klient se nebude posílat komentáře syntaxe Razor.

Spusťte aplikaci a otestovat odkazů v projektu (**Domů**, **o**, **kontakt**, **vytvořit**, **upravit**, a **odstranit**). Každá stránka nastaví nadpis, který se zobrazí na záložce prohlížeče. Když vytvoříte záložku na stránce, název se používá pro záložky. *Pages/Index.cshtml* a *Pages/Movies/Index.cshtml* aktuálně mají stejný název, ale můžete upravit tak, aby měly různé hodnoty.

> [!NOTE]
> Není možné zadat desetinné čárky v `Price` pole. Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") pro desetinné čárky a USA retweetovat neanglické formáty kalendářního data, je nutné provést kroky aplikaci poslali do světa. To [problém Githubu 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) pokyny k přidání desetinné čárky.

`Layout` Je nastavena *Pages/_ViewStart.cshtml* souboru:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]

Předchozí kód nastaví soubor rozložení *Pages/Shared/_Layout.cshtml* pro všechny soubory Razor pod *stránky* složky. Zobrazit [rozložení](xref:razor-pages/index#layout) Další informace.

### <a name="update-the-layout"></a>Aktualizace rozložení

Změnit `<title>` prvek *Pages/Shared/_Layout.cshtml* souboru použijte kratší řetězec.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6)]

Vyhledejte následující element anchor v *Pages/Shared/_Layout.cshtml* souboru.

```cshtml
<a asp-page="/Index" class="navbar-brand">RazorPagesMovie</a>
```

Nahraďte následující značky předchozí prvek.

```cshtml
<a asp-page="/Movies/Index" class="navbar-brand">RpMovie</a>
```

Předchozí element anchor je [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro). V tomto případě má [ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). `asp-page="/Movies/Index"` Pomocné rutiny značky atribut a hodnota vytvoří odkaz `/Movies/Index` stránky Razor.

Uložte změny a otestujte aplikaci po kliknutí na **RpMovie** odkaz. Zobrazit [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Shared/_Layout.cshtml) souboru na Githubu.

### <a name="the-create-page-model"></a>Vytvořit model stránky

Zkontrolujte *Pages/Movies/Create.cshtml.cs* model stránky:

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create21.cshtml.cs?name=snippetALL)]

::: moniker-end

`OnGet` Metoda inicializuje některému ze stavů potřebné pro stránku. Stránka pro vytvoření nemá žádné stavu inicializace, tak `Page` je vrácena. Později v tomto kurzu uvidíte `OnGet` metodu inicializace stavu. `Page` Metoda vytvoří `PageResult` objekt, který vykreslí *Create.cshtml* stránky.

`Movie` Používá vlastnost `[BindProperty]` atribut pro přihlášení k [vazby modelu](xref:mvc/models/model-binding). Kdy vytvořit formulář pošle příspěvek s hodnot formuláře, modul runtime ASP.NET Core váže odeslaných hodnoty, které mají `Movie` modelu.

`OnPostAsync` Metody se spustí, když se publikuje data formuláře na stránce:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Pokud nejsou žádné chyby modelu, formulář se zobrazí znovu, spolu se všechna data formuláře. Většina chyb modelu může být zachycena na straně klienta, před odesláním formuláře. Příklad chybu modelu účtování hodnotu pro pole data, která se nedá převést na datum. Budeme se jimi více o ověřování na straně klienta a ověření modelu v pozdější části kurzu.

Pokud nejsou žádné chyby modelu, data se uloží a bude prohlížeč přesměrován na indexovou stránku.

### <a name="the-create-razor-page"></a>Vytvoření stránky Razor

Zkontrolujte *Pages/Movies/Create.cshtml* Razor stránkovacího souboru:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

<!--
Visual Studio displays the `<form method="post">` tag in a distinctive font used for Tag Helpers. The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper). The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).

![VS17 view of Create.cshtml page](page/_static/th.png)
-->
