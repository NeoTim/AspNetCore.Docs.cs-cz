# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="6216e-101">Vygenerované stránky Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6216e-101">Scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="6216e-102">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6216e-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6216e-103">Tento kurz zkoumá stránky Razor vytvořené generování uživatelského rozhraní v předchozím kurzu.</span><span class="sxs-lookup"><span data-stu-id="6216e-103">This tutorial examines the Razor Pages created by scaffolding in the previous tutorial.</span></span> 

<span data-ttu-id="6216e-104">[Zobrazení nebo stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21) vzorku.</span><span class="sxs-lookup"><span data-stu-id="6216e-104">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="6216e-105">Vytvoření, odstranění, podrobností a upravit stránky.</span><span class="sxs-lookup"><span data-stu-id="6216e-105">The Create, Delete, Details, and Edit pages.</span></span>

<span data-ttu-id="6216e-106">Zkontrolujte *Pages/Movies/Index.cshtml.cs* Model stránky:</span><span class="sxs-lookup"><span data-stu-id="6216e-106">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index21.cshtml.cs)]

::: moniker-end

<span data-ttu-id="6216e-107">Stránky Razor jsou odvozeny z `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="6216e-107">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="6216e-108">Podle konvence `PageModel`-odvozené třídy se nazývá `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="6216e-108">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="6216e-109">Konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) přidáte `MovieContext` na stránku.</span><span class="sxs-lookup"><span data-stu-id="6216e-109">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `MovieContext` to the page.</span></span> <span data-ttu-id="6216e-110">Vygenerované stránky postupovat podle tohoto vzoru.</span><span class="sxs-lookup"><span data-stu-id="6216e-110">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="6216e-111">Zobrazit [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) Další informace o asynchronní programování s Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="6216e-111">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programing with Entity Framework.</span></span>

<span data-ttu-id="6216e-112">Po odeslání žádosti pro stránku, `OnGetAsync` metoda vrátí seznam hodnot filmy pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="6216e-112">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="6216e-113">`OnGetAsync` nebo `OnGet` je volán na stránku Razor k inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="6216e-113">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="6216e-114">V takovém případě `OnGetAsync` získá seznam filmy a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="6216e-114">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="6216e-115">Když `OnGet` vrátí `void` nebo `OnGetAsync` vrátí`Task`, žádná vrácená metoda se používá.</span><span class="sxs-lookup"><span data-stu-id="6216e-115">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="6216e-116">Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>`, musí být zadaný příkaz return.</span><span class="sxs-lookup"><span data-stu-id="6216e-116">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="6216e-117">Například *Pages/Movies/Create.cshtml.cs* `OnPostAsync` metody:</span><span class="sxs-lookup"><span data-stu-id="6216e-117">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="6216e-118">Zkontrolujte *Pages/Movies/Index.cshtml* stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="6216e-118">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="6216e-119">Razor můžete přejít z HTML do jazyka C# nebo do kódu specifické pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="6216e-119">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="6216e-120">Když `@` následuje symbol [Razor rezervované klíčové slovo](xref:mvc/views/razor#razor-reserved-keywords), bude přecházet do kódu specifické pro Razor, jinak bude přecházet do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="6216e-120">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="6216e-121">`@page` Direktivu Razor vytvoří soubor do akce MVC &mdash; to znamená, že dokáže zpracovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="6216e-121">The `@page` Razor directive makes the file into an MVC action &mdash; which means that it can handle requests.</span></span> <span data-ttu-id="6216e-122">`@page` musí být první direktivy Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="6216e-122">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="6216e-123">`@page` je příkladem přechod do kódu specifické pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="6216e-123">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="6216e-124">Zobrazit [syntaxe Razor](xref:mvc/views/razor#razor-syntax) Další informace.</span><span class="sxs-lookup"><span data-stu-id="6216e-124">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="6216e-125">Prozkoumejte výrazu lambda použít v následujících pomocné rutiny HTML:</span><span class="sxs-lookup"><span data-stu-id="6216e-125">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="6216e-126">`DisplayNameFor` Zkontroluje pomocné rutiny HTML `Title` vlastnost se odkazuje ve výrazu lambda lze zjistit název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6216e-126">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="6216e-127">Výraz lambda je zkontroloval spíše než vyhodnocen.</span><span class="sxs-lookup"><span data-stu-id="6216e-127">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="6216e-128">To znamená, že neexistuje žádná narušení přístupu při `model`, `model.Movie`, nebo `model.Movie[0]` jsou `null` nebo je prázdný.</span><span class="sxs-lookup"><span data-stu-id="6216e-128">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="6216e-129">Při vyhodnocování výrazu lambda (třeba index Mei `@Html.DisplayFor(modelItem => item.Title)`), jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="6216e-129">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>
### <a name="the-model-directive"></a><span data-ttu-id="6216e-130">@model – Direktiva</span><span class="sxs-lookup"><span data-stu-id="6216e-130">The @model directive</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="6216e-131">`@model` Direktiva Určuje typ modelu předané do stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="6216e-131">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="6216e-132">V předchozím příkladu `@model` řádek provede `PageModel`-odvozené třídy, které jsou k dispozici pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="6216e-132">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="6216e-133">Model se používá v `@Html.DisplayNameFor` a `@Html.DisplayFor` [pomocných rutin HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.</span><span class="sxs-lookup"><span data-stu-id="6216e-133">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

<span data-ttu-id="6216e-134"><!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>
### ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="6216e-134"><!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>
### ViewData and layout</span></span>

<span data-ttu-id="6216e-135">Vezměte v úvahu následující kód:</span><span class="sxs-lookup"><span data-stu-id="6216e-135">Consider the following code:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="6216e-136">Předchozí zvýrazněný kód je příkladem Razor převádějí do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="6216e-136">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="6216e-137">`{` a `}` znaky, uzavřete blok kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="6216e-137">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="6216e-138">`PageModel` Má základní třída `ViewData` slovník vlastností, který slouží k přidání dat, které chcete předat do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6216e-138">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="6216e-139">Přidání objektů do `ViewData` slovník pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="6216e-139">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="6216e-140">V předchozím příkladu je přidána vlastnost "Title" `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="6216e-140">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span> 

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="6216e-141">Vlastnost "Title" se používá v *Pages/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="6216e-141">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="6216e-142">Následující kód ukazuje několik prvních řádků tohoto *Pages/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="6216e-142">The following markup shows the first few lines of the *Pages/Shared/_Layout.cshtml* file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6216e-143">Vlastnost "Title" se používá v *Pages/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="6216e-143">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="6216e-144">Následující kód ukazuje několik prvních řádků tohoto *_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="6216e-144">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

::: moniker-end

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-999)]

<span data-ttu-id="6216e-145">Na řádku `@*Markup removed for brevity.*@` je komentáře syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="6216e-145">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="6216e-146">Na rozdíl od komentáře HTML (`<!-- -->`), klient se nebude posílat komentáře syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="6216e-146">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

<span data-ttu-id="6216e-147">Spusťte aplikaci a otestovat odkazů v projektu (**Domů**, **o**, **kontakt**, **vytvořit**, **upravit**, a **odstranit**).</span><span class="sxs-lookup"><span data-stu-id="6216e-147">Run the app and test the links in the project (**Home**, **About**, **Contact**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="6216e-148">Každá stránka nastaví nadpis, který se zobrazí na záložce prohlížeče. Když vytvoříte záložku na stránce, název se používá pro záložky.</span><span class="sxs-lookup"><span data-stu-id="6216e-148">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span> <span data-ttu-id="6216e-149">*Pages/Index.cshtml* a *Pages/Movies/Index.cshtml* aktuálně mají stejný název, ale můžete upravit tak, aby měly různé hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6216e-149">*Pages/Index.cshtml* and *Pages/Movies/Index.cshtml* currently have the same title, but you can modify them to have different values.</span></span>

> [!NOTE]
> <span data-ttu-id="6216e-150">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="6216e-150">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6216e-151">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") pro desetinné čárky a USA retweetovat neanglické formáty kalendářního data, je nutné provést kroky aplikaci poslali do světa.</span><span class="sxs-lookup"><span data-stu-id="6216e-151">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="6216e-152">To [problém Githubu 4076](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420) pokyny k přidání desetinné čárky.</span><span class="sxs-lookup"><span data-stu-id="6216e-152">This [GitHub issue 4076](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="6216e-153">`Layout` Je nastavena *Pages/_ViewStart.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="6216e-153">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]

<span data-ttu-id="6216e-154">Předchozí kód nastaví soubor rozložení *Pages/Shared/_Layout.cshtml* pro všechny soubory Razor pod *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="6216e-154">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="6216e-155">Zobrazit [rozložení](xref:razor-pages/index#layout) Další informace.</span><span class="sxs-lookup"><span data-stu-id="6216e-155">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="6216e-156">Aktualizace rozložení</span><span class="sxs-lookup"><span data-stu-id="6216e-156">Update the layout</span></span>

<span data-ttu-id="6216e-157">Změnit `<title>` prvek *Pages/Shared/_Layout.cshtml* souboru použijte kratší řetězec.</span><span class="sxs-lookup"><span data-stu-id="6216e-157">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to use a shorter string.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="6216e-158">Vyhledejte následující element anchor v *Pages/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="6216e-158">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a asp-page="/Index" class="navbar-brand">RazorPagesMovie</a>
```
<span data-ttu-id="6216e-159">Nahraďte následující značky předchozí prvek.</span><span class="sxs-lookup"><span data-stu-id="6216e-159">Replace the preceding element with the following markup.</span></span>

```cshtml
<a asp-page="/Movies/Index" class="navbar-brand">RpMovie</a>
```

<span data-ttu-id="6216e-160">Předchozí element anchor je [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="6216e-160">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="6216e-161">V tomto případě má [ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="6216e-161">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="6216e-162">`asp-page="/Movies/Index"` Pomocné rutiny značky atribut a hodnota vytvoří odkaz `/Movies/Index` stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="6216e-162">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span>

<span data-ttu-id="6216e-163">Uložte změny a otestujte aplikaci po kliknutí na **RpMovie** odkaz.</span><span class="sxs-lookup"><span data-stu-id="6216e-163">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="6216e-164">Zobrazit [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Shared/_Layout.cshtml) souboru na Githubu.</span><span class="sxs-lookup"><span data-stu-id="6216e-164">See the [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Shared/_Layout.cshtml) file in GitHub.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="6216e-165">Vytvořit model stránky</span><span class="sxs-lookup"><span data-stu-id="6216e-165">The Create page model</span></span>

<span data-ttu-id="6216e-166">Zkontrolujte *Pages/Movies/Create.cshtml.cs* model stránky:</span><span class="sxs-lookup"><span data-stu-id="6216e-166">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create21.cshtml.cs?name=snippetALL)]

::: moniker-end


<span data-ttu-id="6216e-167">`OnGet` Metoda inicializuje některému ze stavů potřebné pro stránku.</span><span class="sxs-lookup"><span data-stu-id="6216e-167">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="6216e-168">Stránka pro vytvoření nemá žádné stavu inicializace, tak `Page` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="6216e-168">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="6216e-169">Později v tomto kurzu uvidíte `OnGet` metodu inicializace stavu.</span><span class="sxs-lookup"><span data-stu-id="6216e-169">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="6216e-170">`Page` Metoda vytvoří `PageResult` objekt, který vykreslí *Create.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="6216e-170">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="6216e-171">`Movie` Používá vlastnost `[BindProperty]` atribut pro přihlášení k [vazby modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="6216e-171">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="6216e-172">Kdy vytvořit formulář pošle příspěvek s hodnot formuláře, modul runtime ASP.NET Core váže odeslaných hodnoty, které mají `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="6216e-172">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="6216e-173">`OnPostAsync` Metody se spustí, když se publikuje data formuláře na stránce:</span><span class="sxs-lookup"><span data-stu-id="6216e-173">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="6216e-174">Pokud nejsou žádné chyby modelu, formulář se zobrazí znovu, spolu se všechna data formuláře.</span><span class="sxs-lookup"><span data-stu-id="6216e-174">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="6216e-175">Většina chyb modelu může být zachycena na straně klienta, před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="6216e-175">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="6216e-176">Příklad chybu modelu účtování hodnotu pro pole data, která se nedá převést na datum.</span><span class="sxs-lookup"><span data-stu-id="6216e-176">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="6216e-177">Budeme se jimi více o ověřování na straně klienta a ověření modelu v pozdější části kurzu.</span><span class="sxs-lookup"><span data-stu-id="6216e-177">We'll talk more about client-side validation and model validation later in the tutorial.</span></span>

<span data-ttu-id="6216e-178">Pokud nejsou žádné chyby modelu, data se uloží a bude prohlížeč přesměrován na indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="6216e-178">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="6216e-179">Vytvoření stránky Razor</span><span class="sxs-lookup"><span data-stu-id="6216e-179">The Create Razor Page</span></span>

<span data-ttu-id="6216e-180">Zkontrolujte *Pages/Movies/Create.cshtml* Razor stránkovacího souboru:</span><span class="sxs-lookup"><span data-stu-id="6216e-180">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

<!--
Visual Studio displays the `<form method="post">` tag in a distinctive font used for Tag Helpers. The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper). The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).


![VS17 view of Create.cshtml page](page/_static/th.png)
-->
