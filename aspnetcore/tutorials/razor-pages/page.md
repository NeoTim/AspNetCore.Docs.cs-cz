---
title: Vygenerované stránky Razor v ASP.NET Core
author: rick-anderson
description: Vysvětluje stránky Razor generovaných generování uživatelského rozhraní.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 12/4/2018
uid: tutorials/razor-pages/page
ms.openlocfilehash: c902e9e258b4f436e2f19e2940246b80549238b8
ms.sourcegitcommit: ca5f03210bedc61c6639a734ae5674bfe095dee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55073111"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="41027-103">Vygenerované stránky Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41027-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="41027-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="41027-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="41027-105">Tento kurz zkoumá stránky Razor vytvořené generování uživatelského rozhraní v předchozím kurzu.</span><span class="sxs-lookup"><span data-stu-id="41027-105">This tutorial examines the Razor Pages created by scaffolding in the previous tutorial.</span></span>

<span data-ttu-id="41027-106">[Zobrazení nebo stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) vzorku.</span><span class="sxs-lookup"><span data-stu-id="41027-106">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="41027-107">Vytvoření, odstranění, podrobností a upravit stránky</span><span class="sxs-lookup"><span data-stu-id="41027-107">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="41027-108">Zkontrolujte *Pages/Movies/Index.cshtml.cs* Model stránky:</span><span class="sxs-lookup"><span data-stu-id="41027-108">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="41027-109">Stránky Razor jsou odvozeny z `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="41027-109">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="41027-110">Podle konvence `PageModel`-odvozené třídy se nazývá `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="41027-110">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="41027-111">Konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) přidáte `RazorPagesMovieContext` na stránku.</span><span class="sxs-lookup"><span data-stu-id="41027-111">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="41027-112">Vygenerované stránky postupovat podle tohoto vzoru.</span><span class="sxs-lookup"><span data-stu-id="41027-112">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="41027-113">Zobrazit [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) Další informace o asynchronní programování s Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="41027-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programing with Entity Framework.</span></span>

<span data-ttu-id="41027-114">Po odeslání žádosti pro stránku, `OnGetAsync` metoda vrátí seznam hodnot filmy pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="41027-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="41027-115">`OnGetAsync` nebo `OnGet` je volán na stránku Razor k inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="41027-115">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="41027-116">V takovém případě `OnGetAsync` získá seznam filmy a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="41027-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="41027-117">Když `OnGet` vrátí `void` nebo `OnGetAsync` vrátí`Task`, žádná vrácená metoda se používá.</span><span class="sxs-lookup"><span data-stu-id="41027-117">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="41027-118">Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>`, musí být zadaný příkaz return.</span><span class="sxs-lookup"><span data-stu-id="41027-118">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="41027-119">Například *Pages/Movies/Create.cshtml.cs* `OnPostAsync` metody:</span><span class="sxs-lookup"><span data-stu-id="41027-119">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="41027-120">Zkontrolujte *Pages/Movies/Index.cshtml* stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="41027-120">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="41027-121">Razor můžete přejít z HTML do jazyka C# nebo do kódu specifické pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="41027-121">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="41027-122">Když `@` následuje symbol [Razor rezervované klíčové slovo](xref:mvc/views/razor#razor-reserved-keywords), bude přecházet do kódu specifické pro Razor, jinak bude přecházet do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="41027-122">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="41027-123">`@page` Direktivu Razor vytvoří soubor do akce MVC, což znamená, že dokáže zpracovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="41027-123">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="41027-124">`@page` musí být první direktivy Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="41027-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="41027-125">`@page` je příkladem přechod do kódu specifické pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="41027-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="41027-126">Zobrazit [syntaxe Razor](xref:mvc/views/razor#razor-syntax) Další informace.</span><span class="sxs-lookup"><span data-stu-id="41027-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="41027-127">Prozkoumejte výrazu lambda použít v následujících pomocné rutiny HTML:</span><span class="sxs-lookup"><span data-stu-id="41027-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="41027-128">`DisplayNameFor` Zkontroluje pomocné rutiny HTML `Title` vlastnost se odkazuje ve výrazu lambda lze zjistit název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="41027-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="41027-129">Výraz lambda je zkontroloval spíše než vyhodnocen.</span><span class="sxs-lookup"><span data-stu-id="41027-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="41027-130">To znamená, že neexistuje žádná narušení přístupu při `model`, `model.Movie`, nebo `model.Movie[0]` jsou `null` nebo je prázdný.</span><span class="sxs-lookup"><span data-stu-id="41027-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="41027-131">Při vyhodnocování výrazu lambda (třeba index Mei `@Html.DisplayFor(modelItem => item.Title)`), jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="41027-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>
### <a name="the-model-directive"></a><span data-ttu-id="41027-132">@model – Direktiva</span><span class="sxs-lookup"><span data-stu-id="41027-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="41027-133">`@model` Direktiva Určuje typ modelu předané do stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="41027-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="41027-134">V předchozím příkladu `@model` řádek provede `PageModel`-odvozené třídy, které jsou k dispozici pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="41027-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="41027-135">Model se používá v `@Html.DisplayNameFor` a `@Html.DisplayFor` [pomocných rutin HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.</span><span class="sxs-lookup"><span data-stu-id="41027-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="41027-136">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="41027-136">The layout page</span></span>

<span data-ttu-id="41027-137">Vyberte nabídky odkazy (**RazorPagesMovie**, **Domů**, a **ochrany osobních údajů**).</span><span class="sxs-lookup"><span data-stu-id="41027-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="41027-138">Každá stránka zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="41027-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="41027-139">Rozložení nabídky je implementována v *Pages/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="41027-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="41027-140">Otevřít *Pages/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="41027-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="41027-141">[Rozložení](xref:mvc/views/layout) šablony umožňují zadat kontejner rozložení HTML vašeho webu na jednom místě a použijte ji na několika stránkách ve vaší lokalitě.</span><span class="sxs-lookup"><span data-stu-id="41027-141">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="41027-142">Najít `@RenderBody()` řádku.</span><span class="sxs-lookup"><span data-stu-id="41027-142">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="41027-143">`RenderBody` je zástupný symbol, kde všechny specifický pro stránku zobrazení, se kterými create, show *zabalené* stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="41027-143">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="41027-144">Například, pokud jste vybrali **ochrany osobních údajů** odkaz, **Pages/Privacy.cshtml** je zobrazení vykresleno uvnitř `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="41027-144">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>
### <a name="viewdata-and-layout"></a><span data-ttu-id="41027-145">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="41027-145">ViewData and layout</span></span>

<span data-ttu-id="41027-146">Vezměte v úvahu následující kód z *Pages/Movies/Index.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="41027-146">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="41027-147">Předchozí zvýrazněný kód je příkladem Razor převádějí do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="41027-147">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="41027-148">`{` a `}` znaky, uzavřete blok kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="41027-148">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="41027-149">`PageModel` Má základní třída `ViewData` slovník vlastností, který slouží k přidání dat, které chcete předat do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="41027-149">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="41027-150">Přidání objektů do `ViewData` slovník pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="41027-150">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="41027-151">V předchozím příkladu je přidána vlastnost "Title" `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="41027-151">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span> 

<span data-ttu-id="41027-152">Vlastnost "Title" se používá v *Pages/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="41027-152">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="41027-153">Následující kód ukazuje několik prvních řádků tohoto *_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="41027-153">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step. 
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="41027-154">Na řádku `@*Markup removed for brevity.*@` je komentáře syntaxe Razor, která se nezobrazí v souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="41027-154">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="41027-155">Na rozdíl od komentáře HTML (`<!-- -->`), klient se nebude posílat komentáře syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="41027-155">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="41027-156">Aktualizace rozložení</span><span class="sxs-lookup"><span data-stu-id="41027-156">Update the layout</span></span>

<span data-ttu-id="41027-157">Změnit `<title>` element v *Pages/Shared/_Layout.cshtml* soubor zobrazení **film** spíše než **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="41027-157">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]


<span data-ttu-id="41027-158">Vyhledejte následující element anchor v *Pages/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="41027-158">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="41027-159">Nahraďte následující značky předchozí prvek.</span><span class="sxs-lookup"><span data-stu-id="41027-159">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="41027-160">Předchozí element anchor je [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="41027-160">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="41027-161">V tomto případě má [ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="41027-161">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="41027-162">`asp-page="/Movies/Index"` Pomocné rutiny značky atribut a hodnota vytvoří odkaz `/Movies/Index` stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="41027-162">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="41027-163">`asp-area` Hodnota atributu je prázdný, takže oblasti se nepoužívá v odkazu.</span><span class="sxs-lookup"><span data-stu-id="41027-163">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="41027-164">Zobrazit [oblasti](xref:mvc/controllers/areas) Další informace.</span><span class="sxs-lookup"><span data-stu-id="41027-164">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="41027-165">Uložte změny a otestujte aplikaci po kliknutí na **RpMovie** odkaz.</span><span class="sxs-lookup"><span data-stu-id="41027-165">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="41027-166">Zobrazit [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) souboru v Githubu, pokud máte potíže s.</span><span class="sxs-lookup"><span data-stu-id="41027-166">See the [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="41027-167">Testování propojení (**Domů**, **RpMovie**, **vytvořit**, **upravit**, a **odstranit**).</span><span class="sxs-lookup"><span data-stu-id="41027-167">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="41027-168">Každá stránka nastaví nadpis, který se zobrazí na záložce prohlížeče. Když vytvoříte záložku na stránce, název se používá pro záložky.</span><span class="sxs-lookup"><span data-stu-id="41027-168">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="41027-169">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="41027-169">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="41027-170">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") pro desetinné čárky a USA retweetovat neanglické formáty kalendářního data, je nutné provést kroky aplikaci poslali do světa.</span><span class="sxs-lookup"><span data-stu-id="41027-170">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="41027-171">To [problém Githubu 4076](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420) pokyny k přidání desetinné čárky.</span><span class="sxs-lookup"><span data-stu-id="41027-171">This [GitHub issue 4076](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="41027-172">`Layout` Je nastavena *Pages/_ViewStart.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="41027-172">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="41027-173">Předchozí kód nastaví soubor rozložení *Pages/Shared/_Layout.cshtml* pro všechny soubory Razor pod *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="41027-173">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="41027-174">Zobrazit [rozložení](xref:razor-pages/index#layout) Další informace.</span><span class="sxs-lookup"><span data-stu-id="41027-174">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="41027-175">Vytvořit model stránky</span><span class="sxs-lookup"><span data-stu-id="41027-175">The Create page model</span></span>

<span data-ttu-id="41027-176">Zkontrolujte *Pages/Movies/Create.cshtml.cs* model stránky:</span><span class="sxs-lookup"><span data-stu-id="41027-176">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="41027-177">`OnGet` Metoda inicializuje některému ze stavů potřebné pro stránku.</span><span class="sxs-lookup"><span data-stu-id="41027-177">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="41027-178">Stránka pro vytvoření nemá žádné stavu inicializace, tak `Page` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="41027-178">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="41027-179">Později v tomto kurzu uvidíte `OnGet` metodu inicializace stavu.</span><span class="sxs-lookup"><span data-stu-id="41027-179">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="41027-180">`Page` Metoda vytvoří `PageResult` objekt, který vykreslí *Create.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="41027-180">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="41027-181">`Movie` Používá vlastnost `[BindProperty]` atribut pro přihlášení k [vazby modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="41027-181">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="41027-182">Kdy vytvořit formulář pošle příspěvek s hodnot formuláře, modul runtime ASP.NET Core váže odeslaných hodnoty, které mají `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="41027-182">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="41027-183">`OnPostAsync` Metody se spustí, když se publikuje data formuláře na stránce:</span><span class="sxs-lookup"><span data-stu-id="41027-183">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="41027-184">Pokud nejsou žádné chyby modelu, formulář se zobrazí znovu, spolu se všechna data formuláře.</span><span class="sxs-lookup"><span data-stu-id="41027-184">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="41027-185">Většina chyb modelu může být zachycena na straně klienta, před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="41027-185">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="41027-186">Příklad chybu modelu účtování hodnotu pro pole data, která se nedá převést na datum.</span><span class="sxs-lookup"><span data-stu-id="41027-186">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="41027-187">Ověřování na straně klienta a ověření modelu jsou popsány dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="41027-187">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="41027-188">Pokud nejsou žádné chyby modelu, data se uloží a bude prohlížeč přesměrován na indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="41027-188">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="41027-189">Vytvoření stránky Razor</span><span class="sxs-lookup"><span data-stu-id="41027-189">The Create Razor Page</span></span>

<span data-ttu-id="41027-190">Zkontrolujte *Pages/Movies/Create.cshtml* Razor stránkovacího souboru:</span><span class="sxs-lookup"><span data-stu-id="41027-190">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="41027-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41027-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41027-192">Visual Studio zobrazí `<form method="post">` značku rozlišovací tučné písmo použité pro pomocné rutiny značek:</span><span class="sxs-lookup"><span data-stu-id="41027-192">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

<span data-ttu-id="41027-193">![VS17 zobrazení Create.cshtml stránky](page/_static/th.png)
<!-- Code --------------------------></span><span class="sxs-lookup"><span data-stu-id="41027-193">![VS17 view of Create.cshtml page](page/_static/th.png)
<!-- Code --------------------------></span></span>
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="41027-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41027-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="41027-195">Další informace o pomocných rutin značek, jako `<form method="post">`, naleznete v tématu [pomocných rutin značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="41027-195">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="41027-196">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="41027-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="41027-197">Visual Studio pro Mac zobrazí `<form method="post">` značku rozlišovací tučné písmo použité pro pomocné rutiny značek.</span><span class="sxs-lookup"><span data-stu-id="41027-197">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---  
<!-- End of VS tabs -->

<span data-ttu-id="41027-198">`<form method="post">` Prvek je [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="41027-198">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="41027-199">Pomocná rutina značky formuláře se automaticky zahrne [antiforgery token](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="41027-199">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="41027-200">Modul generování uživatelského rozhraní vytvoří kód Razor pro každé pole v modelu (s výjimkou ID) podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="41027-200">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="41027-201">[Pomocných rutin značek ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a ` <span asp-validation-for`) zobrazení chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="41027-201">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and ` <span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="41027-202">Ověření se budeme věnovat jednotlivě podrobněji dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="41027-202">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="41027-203">[Pomocné rutiny značky popisek](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje titulek popisek a `for` atribut pro `Title` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="41027-203">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="41027-204">[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) používá [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="41027-204">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="41027-205">[Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: Základ dat](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="41027-205">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Data Base](xref:tutorials/razor-pages/sql)</span></span>
