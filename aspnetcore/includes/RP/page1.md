# <a name="scaffolded-razor-pages-in-aspnet-core"></a>Vygenerované Razor Pages v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v předchozím kurzu.

[Zobrazení nebo stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21) vzorku.

## <a name="the-create-delete-details-and-edit-pages"></a>Stránky vytvořit, odstranit, podrobnosti a upravit.

Projděte si model stránky *stránky/filmy/index. cshtml. cs* :

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index21.cshtml.cs)]

::: moniker-end

Razor Pages jsou odvozeny `PageModel`z. Podle konvence `PageModel`je volána `<PageName>Model`třída odvozená od třídy. Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání `MovieContext` na stránku. Všechny vygenerované stránky následují tento model. Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .

Když je vytvořena žádost pro stránku, `OnGetAsync` metoda vrátí seznam filmů na stránku Razor. `OnGetAsync`nebo `OnGet` je volána na stránce Razor pro inicializaci stavu stránky. V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.

Při `OnGet`vrácení nebo`Task`vrácení se nepoužije žádná návratová metoda. `OnGetAsync` `void` Pokud je `IActionResult` návratový typ nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz. Například metoda Pages */video/Create. cshtml. cs* `OnPostAsync` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Projděte stránku *stránky/filmy/index. cshtml* Razor:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor může přejít z formátu HTML C# do nebo do kódu specifického pro Razor. Je-li [](xref:mvc/views/razor#razor-reserved-keywords) C#symbol následován rezervovaným klíčovým slovem Razor, přejde do kódu specifického pro Razor, jinak přejde do. `@`

Direktiva &mdash; Razor vytvoří soubor do akce MVC, což znamená, že může zpracovávat požadavky. `@page` `@page`musí se jednat o první direktivu Razor na stránce. `@page`je příkladem přechodu do kódu specifického pro Razor. Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .

Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

Pomocník HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu. `DisplayNameFor` Výraz lambda je zkontrolován namísto vyhodnocení. To znamená, že nedojde k narušení přístupu `model`, `model.Movie` `model.Movie[0]` Pokud jsou `null` nebo prázdná. Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.

<a name="md"></a>

### <a name="the-model-directive"></a>@model Direktiva

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model` Direktiva určuje typ modelu předaného na stránku Razor. V předchozím příkladu `@model` čára `PageModel`zpřístupňuje třídu odvozenou pro stránku Razor. Model se používá v `@Html.DisplayNameFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a `@Html.DisplayFor` na stránce.

<!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData a rozložení

Vezměte v úvahu následující kód:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Předchozí zvýrazněný kód je příkladem přechodu Razor do C#. Znaky `{` a `}` uzavírají blok C# kódu.

`PageModel` Základní třída `ViewData` má vlastnost Dictionary, kterou lze použít k přidání dat, která chcete předat zobrazení. Do `ViewData` slovníku přidáte objekty pomocí vzoru klíč/hodnota. V předchozím příkladu je do `ViewData` slovníku přidána vlastnost title.

::: moniker range="= aspnetcore-2.0"

Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* . Následující kód ukazuje několik prvních řádků souboru *Pages/Shared/_Layout. cshtml* .

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* . Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .

::: moniker-end

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-999)]

Řádek `@*Markup removed for brevity.*@` je komentář Razor. Na rozdíl od komentářů jazyka`<!-- -->`HTML () nejsou komentáře pro Razor odesílány klientovi.

Spusťte aplikaci a otestujte odkazy v projektu (**Domů**, **o**, **kontakt**, **vytvořit**, **Upravit**a **Odstranit**). Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku. *Stránky/index. cshtml* a *Pages/video/index. cshtml* aktuálně mají stejný název, ale můžete je upravit tak, aby měly jiné hodnoty.

> [!NOTE]
> Není možné zadat desetinné čárky v `Price` pole. Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace. Tento [problém githubu 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) najdete v pokynech k přidání desetinné čárky.

Vlastnost je nastavena v souboru *Pages/_ViewStart. cshtml:* `Layout`

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]

Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* . Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .

### <a name="update-the-layout"></a>Aktualizovat rozložení

Změňte prvek v souboru *Pages/Shared/_Layout. cshtml* na použití kratšího řetězce. `<title>`

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6)]

Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .

```cshtml
<a asp-page="/Index" class="navbar-brand">RazorPagesMovie</a>
```

Nahraďte předchozí prvek následujícím kódem.

```cshtml
<a asp-page="/Movies/Index" class="navbar-brand">RpMovie</a>
```

Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro). V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Atribut pomocník `/Movies/Index` značek a hodnota vytvoří odkaz na stránku Razor. `asp-page="/Movies/Index"`

Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** . Podívejte se na soubor [_Layout. cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Shared/_Layout.cshtml) v GitHubu.

### <a name="the-create-page-model"></a>Model vytvoření stránky

Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create21.cshtml.cs?name=snippetALL)]

::: moniker-end

`OnGet` Metoda inicializuje libovolný stav potřebný pro stránku. Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí. Později v tomto kurzu vidíte `OnGet` stav inicializace metody. Metoda vytvoří objekt, který vykresluje stránku *vytvořit. cshtml.* `Page` `PageResult`

Vlastnost používá atribut pro přihlášení k [modelu vazby.](xref:mvc/models/model-binding) `Movie` `[BindProperty]` Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.

`OnPostAsync` Metoda se spustí, když stránka publikuje data formuláře:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře. Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře. Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum. Později v tomto kurzu budeme hovořit o ověřování na straně klienta a ověřování modelu.

Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.

### <a name="the-create-razor-page"></a>Stránka vytvoření Razor

Projděte si soubor *Pages/video/Create. cshtml* Razor Page:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

<!--
Visual Studio displays the `<form method="post">` tag in a distinctive font used for Tag Helpers. The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper). The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).

![VS17 view of Create.cshtml page](page/_static/th.png)
-->
