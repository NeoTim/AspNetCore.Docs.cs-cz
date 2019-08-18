# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="e02f2-101">Vygenerované Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e02f2-101">Scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="e02f2-102">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e02f2-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e02f2-103">Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v předchozím kurzu.</span><span class="sxs-lookup"><span data-stu-id="e02f2-103">This tutorial examines the Razor Pages created by scaffolding in the previous tutorial.</span></span>

<span data-ttu-id="e02f2-104">[Zobrazení nebo stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21) vzorku.</span><span class="sxs-lookup"><span data-stu-id="e02f2-104">[View or download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="e02f2-105">Stránky vytvořit, odstranit, podrobnosti a upravit.</span><span class="sxs-lookup"><span data-stu-id="e02f2-105">The Create, Delete, Details, and Edit pages.</span></span>

<span data-ttu-id="e02f2-106">Projděte si model stránky *stránky/filmy/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="e02f2-106">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index21.cshtml.cs)]

::: moniker-end

<span data-ttu-id="e02f2-107">Razor Pages jsou odvozeny `PageModel`z.</span><span class="sxs-lookup"><span data-stu-id="e02f2-107">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="e02f2-108">Podle konvence `PageModel`je volána `<PageName>Model`třída odvozená od třídy.</span><span class="sxs-lookup"><span data-stu-id="e02f2-108">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="e02f2-109">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání `MovieContext` na stránku.</span><span class="sxs-lookup"><span data-stu-id="e02f2-109">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `MovieContext` to the page.</span></span> <span data-ttu-id="e02f2-110">Všechny vygenerované stránky následují tento model.</span><span class="sxs-lookup"><span data-stu-id="e02f2-110">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="e02f2-111">Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="e02f2-111">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="e02f2-112">Když je vytvořena žádost pro stránku, `OnGetAsync` metoda vrátí seznam filmů na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="e02f2-112">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="e02f2-113">`OnGetAsync`nebo `OnGet` je volána na stránce Razor pro inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="e02f2-113">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="e02f2-114">V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="e02f2-114">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="e02f2-115">Při `OnGet`vrácení nebo`Task`vrácení se nepoužije žádná návratová metoda. `OnGetAsync` `void`</span><span class="sxs-lookup"><span data-stu-id="e02f2-115">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="e02f2-116">Pokud je `IActionResult` návratový typ nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="e02f2-116">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="e02f2-117">Například metoda Pages */video/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="e02f2-117">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="e02f2-118">Projděte stránku *stránky/filmy/index. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="e02f2-118">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="e02f2-119">Razor může přejít z formátu HTML C# do nebo do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="e02f2-119">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="e02f2-120">Je-li [](xref:mvc/views/razor#razor-reserved-keywords) C#symbol následován rezervovaným klíčovým slovem Razor, přejde do kódu specifického pro Razor, jinak přejde do. `@`</span><span class="sxs-lookup"><span data-stu-id="e02f2-120">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="e02f2-121">Direktiva &mdash; Razor vytvoří soubor do akce MVC, což znamená, že může zpracovávat požadavky. `@page`</span><span class="sxs-lookup"><span data-stu-id="e02f2-121">The `@page` Razor directive makes the file into an MVC action &mdash; which means that it can handle requests.</span></span> <span data-ttu-id="e02f2-122">`@page`musí se jednat o první direktivu Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="e02f2-122">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="e02f2-123">`@page`je příkladem přechodu do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="e02f2-123">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="e02f2-124">Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="e02f2-124">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="e02f2-125">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="e02f2-125">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="e02f2-126">Pomocník HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu. `DisplayNameFor`</span><span class="sxs-lookup"><span data-stu-id="e02f2-126">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="e02f2-127">Výraz lambda je zkontrolován namísto vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="e02f2-127">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="e02f2-128">To znamená, že nedojde k narušení přístupu `model`, `model.Movie` `model.Movie[0]` Pokud jsou `null` nebo prázdná.</span><span class="sxs-lookup"><span data-stu-id="e02f2-128">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="e02f2-129">Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="e02f2-129">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="e02f2-130">@model Direktiva</span><span class="sxs-lookup"><span data-stu-id="e02f2-130">The @model directive</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="e02f2-131">`@model` Direktiva určuje typ modelu předaného na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="e02f2-131">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="e02f2-132">V předchozím příkladu `@model` čára `PageModel`zpřístupňuje třídu odvozenou pro stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="e02f2-132">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="e02f2-133">Model se používá v `@Html.DisplayNameFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a `@Html.DisplayFor` na stránce.</span><span class="sxs-lookup"><span data-stu-id="e02f2-133">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

<!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="e02f2-134">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="e02f2-134">ViewData and layout</span></span>

<span data-ttu-id="e02f2-135">Vezměte v úvahu následující kód:</span><span class="sxs-lookup"><span data-stu-id="e02f2-135">Consider the following code:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="e02f2-136">Předchozí zvýrazněný kód je příkladem přechodu Razor do C#.</span><span class="sxs-lookup"><span data-stu-id="e02f2-136">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="e02f2-137">Znaky `{` a `}` uzavírají blok C# kódu.</span><span class="sxs-lookup"><span data-stu-id="e02f2-137">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="e02f2-138">`PageModel` Základní třída `ViewData` má vlastnost Dictionary, kterou lze použít k přidání dat, která chcete předat zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e02f2-138">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="e02f2-139">Do `ViewData` slovníku přidáte objekty pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="e02f2-139">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="e02f2-140">V předchozím příkladu je do `ViewData` slovníku přidána vlastnost title.</span><span class="sxs-lookup"><span data-stu-id="e02f2-140">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="e02f2-141">Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e02f2-141">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="e02f2-142">Následující kód ukazuje několik prvních řádků souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e02f2-142">The following markup shows the first few lines of the *Pages/Shared/_Layout.cshtml* file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e02f2-143">Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e02f2-143">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="e02f2-144">Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e02f2-144">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

::: moniker-end

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-999)]

<span data-ttu-id="e02f2-145">Řádek `@*Markup removed for brevity.*@` je komentář Razor.</span><span class="sxs-lookup"><span data-stu-id="e02f2-145">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="e02f2-146">Na rozdíl od komentářů jazyka`<!-- -->`HTML () nejsou komentáře pro Razor odesílány klientovi.</span><span class="sxs-lookup"><span data-stu-id="e02f2-146">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

<span data-ttu-id="e02f2-147">Spusťte aplikaci a otestujte odkazy v projektu (**Domů**, **o**, **kontakt**, **vytvořit**, **Upravit**a **Odstranit**).</span><span class="sxs-lookup"><span data-stu-id="e02f2-147">Run the app and test the links in the project (**Home**, **About**, **Contact**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="e02f2-148">Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.</span><span class="sxs-lookup"><span data-stu-id="e02f2-148">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span> <span data-ttu-id="e02f2-149">*Stránky/index. cshtml* a *Pages/video/index. cshtml* aktuálně mají stejný název, ale můžete je upravit tak, aby měly jiné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e02f2-149">*Pages/Index.cshtml* and *Pages/Movies/Index.cshtml* currently have the same title, but you can modify them to have different values.</span></span>

> [!NOTE]
> <span data-ttu-id="e02f2-150">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="e02f2-150">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e02f2-151">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e02f2-151">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="e02f2-152">Tento [problém githubu 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) najdete v pokynech k přidání desetinné čárky.</span><span class="sxs-lookup"><span data-stu-id="e02f2-152">This [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="e02f2-153">Vlastnost je nastavena v souboru *Pages/_ViewStart. cshtml:* `Layout`</span><span class="sxs-lookup"><span data-stu-id="e02f2-153">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]

<span data-ttu-id="e02f2-154">Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="e02f2-154">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="e02f2-155">Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="e02f2-155">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="e02f2-156">Aktualizovat rozložení</span><span class="sxs-lookup"><span data-stu-id="e02f2-156">Update the layout</span></span>

<span data-ttu-id="e02f2-157">Změňte prvek v souboru *Pages/Shared/_Layout. cshtml* na použití kratšího řetězce. `<title>`</span><span class="sxs-lookup"><span data-stu-id="e02f2-157">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to use a shorter string.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="e02f2-158">Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e02f2-158">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a asp-page="/Index" class="navbar-brand">RazorPagesMovie</a>
```

<span data-ttu-id="e02f2-159">Nahraďte předchozí prvek následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="e02f2-159">Replace the preceding element with the following markup.</span></span>

```cshtml
<a asp-page="/Movies/Index" class="navbar-brand">RpMovie</a>
```

<span data-ttu-id="e02f2-160">Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e02f2-160">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e02f2-161">V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e02f2-161">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="e02f2-162">Atribut pomocník `/Movies/Index` značek a hodnota vytvoří odkaz na stránku Razor. `asp-page="/Movies/Index"`</span><span class="sxs-lookup"><span data-stu-id="e02f2-162">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span>

<span data-ttu-id="e02f2-163">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="e02f2-163">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="e02f2-164">Podívejte se na soubor [_Layout. cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Shared/_Layout.cshtml) v GitHubu.</span><span class="sxs-lookup"><span data-stu-id="e02f2-164">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Shared/_Layout.cshtml) file in GitHub.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="e02f2-165">Model vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="e02f2-165">The Create page model</span></span>

<span data-ttu-id="e02f2-166">Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="e02f2-166">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create21.cshtml.cs?name=snippetALL)]

::: moniker-end

<span data-ttu-id="e02f2-167">`OnGet` Metoda inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="e02f2-167">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="e02f2-168">Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí.</span><span class="sxs-lookup"><span data-stu-id="e02f2-168">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="e02f2-169">Později v tomto kurzu vidíte `OnGet` stav inicializace metody.</span><span class="sxs-lookup"><span data-stu-id="e02f2-169">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="e02f2-170">Metoda vytvoří objekt, který vykresluje stránku *vytvořit. cshtml.* `Page` `PageResult`</span><span class="sxs-lookup"><span data-stu-id="e02f2-170">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="e02f2-171">Vlastnost používá atribut pro přihlášení k [modelu vazby.](xref:mvc/models/model-binding) `Movie` `[BindProperty]`</span><span class="sxs-lookup"><span data-stu-id="e02f2-171">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e02f2-172">Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="e02f2-172">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="e02f2-173">`OnPostAsync` Metoda se spustí, když stránka publikuje data formuláře:</span><span class="sxs-lookup"><span data-stu-id="e02f2-173">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="e02f2-174">Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="e02f2-174">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="e02f2-175">Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="e02f2-175">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="e02f2-176">Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="e02f2-176">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="e02f2-177">Později v tomto kurzu budeme hovořit o ověřování na straně klienta a ověřování modelu.</span><span class="sxs-lookup"><span data-stu-id="e02f2-177">We'll talk more about client-side validation and model validation later in the tutorial.</span></span>

<span data-ttu-id="e02f2-178">Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="e02f2-178">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="e02f2-179">Stránka vytvoření Razor</span><span class="sxs-lookup"><span data-stu-id="e02f2-179">The Create Razor Page</span></span>

<span data-ttu-id="e02f2-180">Projděte si soubor *Pages/video/Create. cshtml* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="e02f2-180">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

<!--
Visual Studio displays the `<form method="post">` tag in a distinctive font used for Tag Helpers. The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper). The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).

![VS17 view of Create.cshtml page](page/_static/th.png)
-->
