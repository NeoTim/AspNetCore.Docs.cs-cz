---
title: Vygenerované Razor Pages v ASP.NET Core
author: rick-anderson
description: Vysvětluje Razor Pages generovaného pomocí uživatelského rozhraní.
ms.author: riande
ms.date: 08/17/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: 00a8458b9bee4d30c5774a980ff5c23fb8872737
ms.sourcegitcommit: 38cac2552029fc19428722bb204ff9e16eb94225
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2019
ms.locfileid: "69573140"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="ec0ca-103">Vygenerované Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec0ca-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec0ca-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ec0ca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ec0ca-105">Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="ec0ca-106">Stránky vytvořit, odstranit, podrobnosti a upravit</span><span class="sxs-lookup"><span data-stu-id="ec0ca-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="ec0ca-107">Projděte si model stránky *stránky/filmy/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="ec0ca-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="ec0ca-108">Razor Pages jsou odvozeny `PageModel`z.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="ec0ca-109">Podle konvence `PageModel`je volána `<PageName>Model`třída odvozená od třídy.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="ec0ca-110">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání `RazorPagesMovieContext` na stránku.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="ec0ca-111">Všechny vygenerované stránky následují tento model.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="ec0ca-112">Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="ec0ca-113">Když je vytvořena žádost pro stránku, `OnGetAsync` metoda vrátí seznam filmů na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="ec0ca-114">`OnGetAsync`nebo `OnGet` je volána na stránce Razor pro inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-114">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="ec0ca-115">V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="ec0ca-116">Při `OnGet`vrácení nebo`Task`vrácení se nepoužije žádná návratová metoda. `OnGetAsync` `void`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="ec0ca-117">Pokud je `IActionResult` návratový typ nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="ec0ca-118">Například metoda Pages */video/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="ec0ca-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="ec0ca-119">Projděte stránku *stránky/filmy/index. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="ec0ca-120">Razor může přejít z formátu HTML C# do nebo do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="ec0ca-121">Je-li [](xref:mvc/views/razor#razor-reserved-keywords) C#symbol následován rezervovaným klíčovým slovem Razor, přejde do kódu specifického pro Razor, jinak přejde do. `@`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="ec0ca-122">Direktiva `@page` Razor vytvoří soubor na akci MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-122">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="ec0ca-123">`@page`musí se jednat o první direktivu Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-123">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="ec0ca-124">`@page`je příkladem přechodu do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-124">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="ec0ca-125">Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-125">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="ec0ca-126">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-126">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="ec0ca-127">Pomocník HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu. `DisplayNameFor`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-127">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="ec0ca-128">Výraz lambda je zkontrolován namísto vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-128">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="ec0ca-129">To znamená, že nedojde k narušení přístupu `model`, `model.Movie` `model.Movie[0]` Pokud jsou `null` nebo prázdná.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-129">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="ec0ca-130">Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-130">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="ec0ca-131">@model Direktiva</span><span class="sxs-lookup"><span data-stu-id="ec0ca-131">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="ec0ca-132">`@model` Direktiva určuje typ modelu předaného na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-132">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="ec0ca-133">V předchozím příkladu `@model` čára `PageModel`zpřístupňuje třídu odvozenou pro stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-133">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="ec0ca-134">Model se používá v `@Html.DisplayNameFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a `@Html.DisplayFor` na stránce.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-134">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="ec0ca-135">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="ec0ca-135">The layout page</span></span>

<span data-ttu-id="ec0ca-136">Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-136">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="ec0ca-137">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-137">Each page shows the same menu layout.</span></span> <span data-ttu-id="ec0ca-138">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-138">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="ec0ca-139">Otevřete soubor *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-139">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="ec0ca-140">Šablony [rozložení](xref:mvc/views/layout) umožňují, aby rozložení kontejneru HTML bylo:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-140">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="ec0ca-141">Zadáno na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-141">Specified in one place.</span></span>
* <span data-ttu-id="ec0ca-142">Použito na více stránkách v lokalitě.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-142">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="ec0ca-143">`@RenderBody()` Najděte řádek.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-143">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="ec0ca-144">`RenderBody`je zástupný symbol, kde jsou všechna zobrazení konkrétní stránky zobrazena, *zabalena* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-144">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="ec0ca-145">Například vyberte odkaz **osobních údajů** a zobrazení *stránky/soukromí. cshtml* je `RenderBody` vykresleno uvnitř metody.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-145">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="ec0ca-146">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="ec0ca-146">ViewData and layout</span></span>

<span data-ttu-id="ec0ca-147">Vezměte v úvahu následující značky ze souboru *Pages/Movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ec0ca-147">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="ec0ca-148">Předchozí zvýrazněný kód je příkladem přechodu Razor do C#.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-148">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="ec0ca-149">Znaky `{` a `}` uzavírají blok C# kódu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-149">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="ec0ca-150">`PageModel` Základní třída `ViewData` obsahuje vlastnost Dictionary, kterou lze použít k přidání dat a jejich předání do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-150">The `PageModel` base class contains a `ViewData` dictionary property that can be used to add data that and pass it to a View.</span></span> <span data-ttu-id="ec0ca-151">Objekty jsou přidány do `ViewData` slovníku pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-151">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="ec0ca-152">V předchozí ukázce `"Title"` je vlastnost přidána `ViewData` do slovníku.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-152">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="ec0ca-153">Tato `"Title"` vlastnost se používá v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-153">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="ec0ca-154">Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-154">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="ec0ca-155">Řádek `@*Markup removed for brevity.*@` je komentář Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-155">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="ec0ca-156">Na rozdíl od komentářů jazyka`<!-- -->`HTML () nejsou komentáře pro Razor odesílány klientovi.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-156">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="ec0ca-157">Aktualizovat rozložení</span><span class="sxs-lookup"><span data-stu-id="ec0ca-157">Update the layout</span></span>

<span data-ttu-id="ec0ca-158">Změňte prvek v souboru *Pages/Shared/_Layout. cshtml* tak, aby zobrazoval **film** místo **RazorPagesMovie.** `<title>`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-158">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="ec0ca-159">Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-159">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="ec0ca-160">Nahraďte předchozí prvek následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-160">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="ec0ca-161">Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-161">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="ec0ca-162">V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-162">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="ec0ca-163">Atribut pomocník `/Movies/Index` značek a hodnota vytvoří odkaz na stránku Razor. `asp-page="/Movies/Index"`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-163">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="ec0ca-164">Hodnota `asp-area` atributu je prázdná, takže se oblast nepoužívá v odkazu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-164">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="ec0ca-165">Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-165">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="ec0ca-166">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-166">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="ec0ca-167">Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-167">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="ec0ca-168">Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-168">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="ec0ca-169">Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-169">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="ec0ca-170">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-170">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="ec0ca-171">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-171">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="ec0ca-172">Pokyny k přidání desetinné čárky najdete v tomto [problému v githubu 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-172">See this [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="ec0ca-173">Vlastnost je nastavena v souboru *Pages/_ViewStart. cshtml:* `Layout`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-173">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="ec0ca-174">Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-174">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="ec0ca-175">Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-175">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="ec0ca-176">Model vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="ec0ca-176">The Create page model</span></span>

<span data-ttu-id="ec0ca-177">Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="ec0ca-177">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="ec0ca-178">`OnGet` Metoda inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-178">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="ec0ca-179">Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-179">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="ec0ca-180">Později v tomto kurzu se zobrazuje příklad `OnGet` stavu inicializace.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-180">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="ec0ca-181">Metoda vytvoří objekt, který vykresluje stránku *vytvořit. cshtml.* `Page` `PageResult`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-181">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="ec0ca-182">Vlastnost používá atribut pro přihlášení k [modelu vazby.](xref:mvc/models/model-binding) `Movie` `[BindProperty]`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-182">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ec0ca-183">Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-183">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="ec0ca-184">`OnPostAsync` Metoda se spustí, když stránka publikuje data formuláře:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-184">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="ec0ca-185">Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-185">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="ec0ca-186">Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-186">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="ec0ca-187">Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-187">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="ec0ca-188">Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-188">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="ec0ca-189">Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-189">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="ec0ca-190">Stránka vytvoření Razor</span><span class="sxs-lookup"><span data-stu-id="ec0ca-190">The Create Razor Page</span></span>

<span data-ttu-id="ec0ca-191">Projděte si soubor *Pages/video/Create. cshtml* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-191">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ec0ca-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec0ca-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec0ca-193">Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-193">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ec0ca-195">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec0ca-195">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec0ca-196">V předchozím kódu se zobrazují následující pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-196">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ec0ca-197">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ec0ca-197">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ec0ca-198">Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-198">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="ec0ca-199">Element je Pomocník pro [značky formuláře.](xref:mvc/views/working-with-forms#the-form-tag-helper) `<form method="post">`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-199">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="ec0ca-200">Pomocná rutina značky Form zahrnuje [](xref:security/anti-request-forgery)automaticky tokeny proti padělání.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-200">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="ec0ca-201">Modul generování uživatelského rozhraní vytvoří pro každé pole v modelu označení Razor (s výjimkou ID), které je podobné následujícímu:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-201">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="ec0ca-202">[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazí chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-202">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="ec0ca-203">Ověřování je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-203">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="ec0ca-204">[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) vygeneruje popisek `Title` a `for` atribut pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-204">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="ec0ca-205">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá atributy DataAnnotations a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta. [](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)</span><span class="sxs-lookup"><span data-stu-id="ec0ca-205">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="ec0ca-206">Další informace o pomocníkech `<form method="post">`značek, jako je, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-206">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec0ca-207">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ec0ca-207">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ec0ca-208">[Předchozí Přidání modelu](xref:tutorials/razor-pages/model)
> (Další):[ Database](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="ec0ca-208">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec0ca-209">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ec0ca-209">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ec0ca-210">Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-210">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="ec0ca-211">[Zobrazení nebo stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) vzorku.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-211">[View or download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="ec0ca-212">Stránky vytvořit, odstranit, podrobnosti a upravit</span><span class="sxs-lookup"><span data-stu-id="ec0ca-212">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="ec0ca-213">Projděte si model stránky *stránky/filmy/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="ec0ca-213">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="ec0ca-214">Razor Pages jsou odvozeny `PageModel`z.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-214">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="ec0ca-215">Podle konvence `PageModel`je volána `<PageName>Model`třída odvozená od třídy.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-215">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="ec0ca-216">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání `RazorPagesMovieContext` na stránku.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-216">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="ec0ca-217">Všechny vygenerované stránky následují tento model.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-217">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="ec0ca-218">Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-218">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="ec0ca-219">Když je vytvořena žádost pro stránku, `OnGetAsync` metoda vrátí seznam filmů na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-219">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="ec0ca-220">`OnGetAsync`nebo `OnGet` je volána na stránce Razor pro inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-220">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="ec0ca-221">V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-221">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="ec0ca-222">Při `OnGet`vrácení nebo`Task`vrácení se nepoužije žádná návratová metoda. `OnGetAsync` `void`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-222">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="ec0ca-223">Pokud je `IActionResult` návratový typ nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-223">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="ec0ca-224">Například metoda Pages */video/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="ec0ca-224">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="ec0ca-225">Projděte stránku *stránky/filmy/index. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-225">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="ec0ca-226">Razor může přejít z formátu HTML C# do nebo do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-226">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="ec0ca-227">Je-li [](xref:mvc/views/razor#razor-reserved-keywords) C#symbol následován rezervovaným klíčovým slovem Razor, přejde do kódu specifického pro Razor, jinak přejde do. `@`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-227">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="ec0ca-228">Direktiva `@page` Razor vytvoří soubor na akci MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-228">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="ec0ca-229">`@page`musí se jednat o první direktivu Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-229">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="ec0ca-230">`@page`je příkladem přechodu do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-230">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="ec0ca-231">Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-231">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="ec0ca-232">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-232">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="ec0ca-233">Pomocník HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu. `DisplayNameFor`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-233">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="ec0ca-234">Výraz lambda je zkontrolován namísto vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-234">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="ec0ca-235">To znamená, že nedojde k narušení přístupu `model`, `model.Movie` `model.Movie[0]` Pokud jsou `null` nebo prázdná.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-235">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="ec0ca-236">Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-236">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="ec0ca-237">@model Direktiva</span><span class="sxs-lookup"><span data-stu-id="ec0ca-237">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="ec0ca-238">`@model` Direktiva určuje typ modelu předaného na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-238">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="ec0ca-239">V předchozím příkladu `@model` čára `PageModel`zpřístupňuje třídu odvozenou pro stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-239">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="ec0ca-240">Model se používá v `@Html.DisplayNameFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a `@Html.DisplayFor` na stránce.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-240">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="ec0ca-241">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="ec0ca-241">The layout page</span></span>

<span data-ttu-id="ec0ca-242">Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-242">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="ec0ca-243">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-243">Each page shows the same menu layout.</span></span> <span data-ttu-id="ec0ca-244">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-244">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="ec0ca-245">Otevřete soubor *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-245">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="ec0ca-246">Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-246">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="ec0ca-247">`@RenderBody()` Najděte řádek.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-247">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="ec0ca-248">`RenderBody`je zástupný symbol, kde jsou všechna zobrazení pro konkrétní stránku, která vytvoříte, *zabalena* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-248">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="ec0ca-249">Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **stránky/soukromí. cshtml** se `RenderBody` vykreslí uvnitř metody.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-249">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="ec0ca-250">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="ec0ca-250">ViewData and layout</span></span>

<span data-ttu-id="ec0ca-251">Vezměte v úvahu následující kód ze souboru *Pages/Movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ec0ca-251">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="ec0ca-252">Předchozí zvýrazněný kód je příkladem přechodu Razor do C#.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-252">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="ec0ca-253">Znaky `{` a `}` uzavírají blok C# kódu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-253">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="ec0ca-254">`PageModel` Základní třída `ViewData` má vlastnost Dictionary, kterou lze použít k přidání dat, která chcete předat zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-254">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="ec0ca-255">Do `ViewData` slovníku přidáte objekty pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-255">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="ec0ca-256">V předchozím příkladu je do `ViewData` slovníku přidána vlastnost title.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-256">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="ec0ca-257">Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-257">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="ec0ca-258">Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-258">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="ec0ca-259">Řádek `@*Markup removed for brevity.*@` je komentář Razor, který se v souboru rozložení nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-259">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="ec0ca-260">Na rozdíl od komentářů jazyka`<!-- -->`HTML () nejsou komentáře pro Razor odesílány klientovi.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-260">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="ec0ca-261">Aktualizovat rozložení</span><span class="sxs-lookup"><span data-stu-id="ec0ca-261">Update the layout</span></span>

<span data-ttu-id="ec0ca-262">Změňte prvek v souboru *Pages/Shared/_Layout. cshtml* tak, aby zobrazoval **film** místo **RazorPagesMovie.** `<title>`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-262">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="ec0ca-263">Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-263">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="ec0ca-264">Nahraďte předchozí prvek následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-264">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="ec0ca-265">Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-265">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="ec0ca-266">V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-266">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="ec0ca-267">Atribut pomocník `/Movies/Index` značek a hodnota vytvoří odkaz na stránku Razor. `asp-page="/Movies/Index"`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-267">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="ec0ca-268">Hodnota `asp-area` atributu je prázdná, takže se oblast nepoužívá v odkazu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-268">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="ec0ca-269">Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-269">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="ec0ca-270">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-270">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="ec0ca-271">Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-271">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="ec0ca-272">Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-272">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="ec0ca-273">Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-273">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="ec0ca-274">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-274">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="ec0ca-275">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-275">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="ec0ca-276">Tento [problém githubu 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) najdete v pokynech k přidání desetinné čárky.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-276">This [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="ec0ca-277">Vlastnost je nastavena v souboru *Pages/_ViewStart. cshtml:* `Layout`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-277">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="ec0ca-278">Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-278">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="ec0ca-279">Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="ec0ca-279">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="ec0ca-280">Model vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="ec0ca-280">The Create page model</span></span>

<span data-ttu-id="ec0ca-281">Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="ec0ca-281">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="ec0ca-282">`OnGet` Metoda inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-282">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="ec0ca-283">Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-283">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="ec0ca-284">Později v tomto kurzu vidíte `OnGet` stav inicializace metody.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-284">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="ec0ca-285">Metoda vytvoří objekt, který vykresluje stránku *vytvořit. cshtml.* `Page` `PageResult`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-285">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="ec0ca-286">Vlastnost používá atribut pro přihlášení k [modelu vazby.](xref:mvc/models/model-binding) `Movie` `[BindProperty]`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-286">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ec0ca-287">Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-287">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="ec0ca-288">`OnPostAsync` Metoda se spustí, když stránka publikuje data formuláře:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-288">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="ec0ca-289">Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-289">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="ec0ca-290">Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-290">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="ec0ca-291">Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-291">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="ec0ca-292">Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-292">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="ec0ca-293">Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-293">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="ec0ca-294">Stránka vytvoření Razor</span><span class="sxs-lookup"><span data-stu-id="ec0ca-294">The Create Razor Page</span></span>

<span data-ttu-id="ec0ca-295">Projděte si soubor *Pages/video/Create. cshtml* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-295">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ec0ca-296">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec0ca-296">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec0ca-297">Visual Studio zobrazí značku `<form method="post">` v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-297">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ec0ca-299">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec0ca-299">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec0ca-300">Další informace o pomocníkech `<form method="post">`značek, jako je, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ec0ca-300">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ec0ca-301">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ec0ca-301">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ec0ca-302">Visual Studio pro Mac zobrazí značku `<form method="post">` v zřetelném tučném písmu, které se používá pro pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-302">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="ec0ca-303">Element je Pomocník pro [značky formuláře.](xref:mvc/views/working-with-forms#the-form-tag-helper) `<form method="post">`</span><span class="sxs-lookup"><span data-stu-id="ec0ca-303">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="ec0ca-304">Pomocná rutina značky Form zahrnuje [](xref:security/anti-request-forgery)automaticky tokeny proti padělání.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-304">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="ec0ca-305">Modul generování uživatelského rozhraní vytvoří pro každé pole v modelu označení Razor (s výjimkou ID), které je podobné následujícímu:</span><span class="sxs-lookup"><span data-stu-id="ec0ca-305">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="ec0ca-306">[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazí chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-306">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="ec0ca-307">Ověřování je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-307">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="ec0ca-308">[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) vygeneruje popisek `Title` a `for` atribut pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ec0ca-308">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="ec0ca-309">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá atributy DataAnnotations a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta. [](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)</span><span class="sxs-lookup"><span data-stu-id="ec0ca-309">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec0ca-310">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ec0ca-310">Additional resources</span></span>

* [<span data-ttu-id="ec0ca-311">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="ec0ca-311">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="ec0ca-312">[Předchozí Přidání modelu](xref:tutorials/razor-pages/model)
> (Další):[ Database](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="ec0ca-312">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
