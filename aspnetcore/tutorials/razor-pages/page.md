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
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="b653b-103">Šanek žiletky stránky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b653b-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b653b-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b653b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b653b-105">Tento kurz zkoumá Razor stránky vytvořené lešení v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="b653b-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="b653b-106">Stránky Vytvořit, Odstranit, Podrobnosti a Upravit</span><span class="sxs-lookup"><span data-stu-id="b653b-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="b653b-107">Prohlédněte si stránkový model *Pages/Movies/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="b653b-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="b653b-108">Razor Stránky jsou `PageModel`odvozeny od .</span><span class="sxs-lookup"><span data-stu-id="b653b-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="b653b-109">Podle konvence `PageModel`-derived třídy `<PageName>Model`se nazývá .</span><span class="sxs-lookup"><span data-stu-id="b653b-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="b653b-110">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) `RazorPagesMovieContext` k přidání na stránku.</span><span class="sxs-lookup"><span data-stu-id="b653b-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="b653b-111">Všechny lešení stránky následovat tento vzor.</span><span class="sxs-lookup"><span data-stu-id="b653b-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="b653b-112">Další informace o asynchronním programování pomocí entity Framework naleznete v [tématu Asynchronní kód.](xref:data/ef-rp/intro#asynchronous-code)</span><span class="sxs-lookup"><span data-stu-id="b653b-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="b653b-113">Když je požadavek na stránku, `OnGetAsync` metoda vrátí seznam filmů na razor page.</span><span class="sxs-lookup"><span data-stu-id="b653b-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="b653b-114">`OnGetAsync`nebo `OnGet` je volána k inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="b653b-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="b653b-115">V tomto `OnGetAsync` případě získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="b653b-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="b653b-116">Při `OnGet` `void` vrácení `OnGetAsync` `Task`nebo vrácení , není použit žádný příkaz return.</span><span class="sxs-lookup"><span data-stu-id="b653b-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="b653b-117">Pokud je `IActionResult` návratový `Task<IActionResult>`typ nebo , musí být zadán příkaz return.</span><span class="sxs-lookup"><span data-stu-id="b653b-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="b653b-118">Například metoda *Pages/Movies/Create.cshtml.cs:* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="b653b-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="b653b-119">Prohlédněte si *stránku Pages/Movies/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="b653b-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="b653b-120">Holicí strojek může přejít z HTML do Jazyka C# nebo do značky specifické pro razor.</span><span class="sxs-lookup"><span data-stu-id="b653b-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="b653b-121">Pokud `@` symbol následuje [razor vyhrazené klíčové slovo](xref:mvc/views/razor#razor-reserved-keywords), přechody do razor-specifické značky, jinak se přejde do C#.</span><span class="sxs-lookup"><span data-stu-id="b653b-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="b653b-122">Směrnice @page</span><span class="sxs-lookup"><span data-stu-id="b653b-122">The @page directive</span></span>

<span data-ttu-id="b653b-123">Razor `@page` směrnice umožňuje soubor akce MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="b653b-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="b653b-124">`@page`musí být první direktivou Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="b653b-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="b653b-125">`@page`je příkladem přechodu na značky specifické pro Razor.</span><span class="sxs-lookup"><span data-stu-id="b653b-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="b653b-126">Další informace naleznete v [tématu Syntaxe břitvy.](xref:mvc/views/razor#razor-syntax)</span><span class="sxs-lookup"><span data-stu-id="b653b-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="b653b-127">Zkontrolujte výraz lambda použitý v následujícím pomocném prvku HTML:</span><span class="sxs-lookup"><span data-stu-id="b653b-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="b653b-128">Pomocník `DisplayNameFor` HTML zkontroluje `Title` vlastnost, na kterou odkazuje výraz lambda, a určí tak zobrazovaný název.</span><span class="sxs-lookup"><span data-stu-id="b653b-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="b653b-129">Lambda výraz je kontrolována spíše než vyhodnocena.</span><span class="sxs-lookup"><span data-stu-id="b653b-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="b653b-130">To znamená, že neexistuje `model` `model.Movie`žádné `model.Movie[0]` narušení `null` přístupu, když , , nebo je prázdný.</span><span class="sxs-lookup"><span data-stu-id="b653b-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="b653b-131">Při vyhodnocení výrazu lambda (například s `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnoceny hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="b653b-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="b653b-132">Směrnice @model</span><span class="sxs-lookup"><span data-stu-id="b653b-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="b653b-133">Směrnice `@model` určuje typ modelu předaného razor Page.</span><span class="sxs-lookup"><span data-stu-id="b653b-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="b653b-134">V předchozím příkladu `@model` řádek zpřístupňuje `PageModel`-derived třídy Razor Page.</span><span class="sxs-lookup"><span data-stu-id="b653b-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="b653b-135">Model se používá `@Html.DisplayNameFor` v `@Html.DisplayFor` a [HTML pomocníky](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.</span><span class="sxs-lookup"><span data-stu-id="b653b-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="b653b-136">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="b653b-136">The layout page</span></span>

<span data-ttu-id="b653b-137">Vyberte odkazy na nabídky **(RazorPagesMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="b653b-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="b653b-138">Každá stránka zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="b653b-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="b653b-139">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b653b-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b653b-140">Otevřete soubor *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b653b-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b653b-141">[Šablony rozložení](xref:mvc/views/layout) umožňují rozložení kontejneru HTML:</span><span class="sxs-lookup"><span data-stu-id="b653b-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="b653b-142">Specifikováno na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="b653b-142">Specified in one place.</span></span>
* <span data-ttu-id="b653b-143">Použito na více stránkách webu.</span><span class="sxs-lookup"><span data-stu-id="b653b-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="b653b-144">Najděte `@RenderBody()` linku.</span><span class="sxs-lookup"><span data-stu-id="b653b-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b653b-145">`RenderBody`je zástupný symbol, ve kterém se zobrazí všechna zobrazení specifická pro *stránku, zabalená* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="b653b-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b653b-146">Vyberte například odkaz **Soukromí** a zobrazení *Stránky/Privacy.cshtml* `RenderBody` se vykreslí uvnitř metody.</span><span class="sxs-lookup"><span data-stu-id="b653b-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="b653b-147">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="b653b-147">ViewData and layout</span></span>

<span data-ttu-id="b653b-148">Vezměte v úvahu následující značky ze souboru *Pages/Movies/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="b653b-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="b653b-149">Předchozí zvýrazněné značky je příkladem Razor přechodu do C#.</span><span class="sxs-lookup"><span data-stu-id="b653b-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="b653b-150">Znaky `{` `}` a uzavřete blok kódu Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="b653b-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="b653b-151">Základní `PageModel` třída obsahuje `ViewData` vlastnost dictionary, kterou lze použít k předání dat do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b653b-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="b653b-152">Objekty jsou `ViewData` přidány do slovníku pomocí klíče/hodnoty vzoru.</span><span class="sxs-lookup"><span data-stu-id="b653b-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="b653b-153">V předchozí ukázce je `"Title"` vlastnost `ViewData` přidána do slovníku.</span><span class="sxs-lookup"><span data-stu-id="b653b-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="b653b-154">Vlastnost `"Title"` se používá v souboru *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b653b-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b653b-155">Následující značky zobrazují několik prvních řádků souboru *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b653b-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="b653b-156">Linka `@*Markup removed for brevity.*@` je razor komentář.</span><span class="sxs-lookup"><span data-stu-id="b653b-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="b653b-157">Na rozdíl`<!-- -->`od html komentáře ( ), Razor komentáře nejsou odesílány klientovi.</span><span class="sxs-lookup"><span data-stu-id="b653b-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="b653b-158">Aktualizace rozložení</span><span class="sxs-lookup"><span data-stu-id="b653b-158">Update the layout</span></span>

<span data-ttu-id="b653b-159">Změňte `<title>` prvek v souboru *Pages/Shared/_Layout.cshtml* tak, aby zobrazoval **film** místo **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="b653b-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="b653b-160">V souboru *Stránky/Sdílené/_Layout.cshtml* najdete následující prvek ukotvení.</span><span class="sxs-lookup"><span data-stu-id="b653b-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="b653b-161">Nahraďte předchozí prvek následujícími značkami:</span><span class="sxs-lookup"><span data-stu-id="b653b-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="b653b-162">Předchozí prvek ukotvení je [Tag Helper](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="b653b-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="b653b-163">V tomto případě je to [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b653b-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="b653b-164">Tag `asp-page="/Movies/Index"` Helper atribut a hodnota vytvoří `/Movies/Index` odkaz na holicí strojek stránku.</span><span class="sxs-lookup"><span data-stu-id="b653b-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="b653b-165">Hodnota `asp-area` atributu je prázdná, takže oblast není použita v odkazu.</span><span class="sxs-lookup"><span data-stu-id="b653b-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="b653b-166">Další informace naleznete v [části Oblasti.](xref:mvc/controllers/areas)</span><span class="sxs-lookup"><span data-stu-id="b653b-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="b653b-167">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie.**</span><span class="sxs-lookup"><span data-stu-id="b653b-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="b653b-168">Pokud máte nějaké problémy, podívejte se na soubor [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) v GitHubu.</span><span class="sxs-lookup"><span data-stu-id="b653b-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="b653b-169">Otestujte ostatní odkazy **(Domů**, **RpMovie**, **Vytvořit**, **Upravit**a **Odstranit).**</span><span class="sxs-lookup"><span data-stu-id="b653b-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="b653b-170">Každá stránka nastaví název, který můžete vidět na kartě prohlížeče. Když záložku přidáte do záložek, použije se pro záložku název.</span><span class="sxs-lookup"><span data-stu-id="b653b-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="b653b-171">Je možné, že do pole nebudete moci `Price` zadat desetinná čárka.</span><span class="sxs-lookup"><span data-stu-id="b653b-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b653b-172">Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a formáty dat mimo americkou angličtinu, musíte podniknout kroky ke globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b653b-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="b653b-173">Pokyny k přidání desetinné čárky najdete v tomto [problému GitHub 4076.](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)</span><span class="sxs-lookup"><span data-stu-id="b653b-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="b653b-174">Vlastnost `Layout` je nastavena v souboru *Pages/_ViewStart.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="b653b-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="b653b-175">Předchozí značky nastaví soubor rozvržení na *Pages/Shared/_Layout.cshtml* pro všechny soubory Razor ve složce *Pages.*</span><span class="sxs-lookup"><span data-stu-id="b653b-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="b653b-176">Další informace najdete [v tématu Rozložení.](xref:razor-pages/index#layout)</span><span class="sxs-lookup"><span data-stu-id="b653b-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="b653b-177">Vytvořit model stránky</span><span class="sxs-lookup"><span data-stu-id="b653b-177">The Create page model</span></span>

<span data-ttu-id="b653b-178">Prohlédněte si model stránky *Pages/Movies/Create.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="b653b-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="b653b-179">Metoda `OnGet` inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="b653b-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="b653b-180">Create stránka nemá žádný stav inicializovat, `Page` tak je vrácena.</span><span class="sxs-lookup"><span data-stu-id="b653b-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="b653b-181">Později v kurzu je `OnGet` zobrazen příklad inicializace stavu.</span><span class="sxs-lookup"><span data-stu-id="b653b-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="b653b-182">Metoda `Page` vytvoří `PageResult` objekt, který vykreslí *create.cshtml* stránku.</span><span class="sxs-lookup"><span data-stu-id="b653b-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="b653b-183">Vlastnost `Movie` používá `[BindProperty]` atribut opt-in pro [vazby modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="b653b-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="b653b-184">Když formulář Vytvořit zaúčtuje hodnoty formuláře, ASP.NET core runtime `Movie` sváže zaúčtované hodnoty s modelem.</span><span class="sxs-lookup"><span data-stu-id="b653b-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="b653b-185">Metoda `OnPostAsync` je spuštěna, když stránka odešle data formuláře:</span><span class="sxs-lookup"><span data-stu-id="b653b-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="b653b-186">Pokud dojde k chybám modelu, formulář se znovu zobrazí spolu se všemi zaúčtovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="b653b-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="b653b-187">Většina chyb modelu může být zachycena na straně klienta před zaúčtováním formuláře.</span><span class="sxs-lookup"><span data-stu-id="b653b-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="b653b-188">Příkladem chyby modelu je zaúčtování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="b653b-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="b653b-189">Ověření na straně klienta a ověření modelu jsou popsány dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="b653b-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="b653b-190">Pokud nejsou žádné chyby modelu, data se uloží a prohlížeč je přesměrován na stránku Index.</span><span class="sxs-lookup"><span data-stu-id="b653b-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="b653b-191">Stránka Vytvořit holicí strojek</span><span class="sxs-lookup"><span data-stu-id="b653b-191">The Create Razor Page</span></span>

<span data-ttu-id="b653b-192">Prohlédněte si stránkovací soubor *Pages/Movies/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="b653b-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="b653b-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b653b-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b653b-194">Visual Studio zobrazí následující značky v rozlišovací tučné písmo používané pro tag pomocné síly:</span><span class="sxs-lookup"><span data-stu-id="b653b-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Zobrazení VS17 na stránce Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b653b-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b653b-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b653b-197">V předchozích značkách jsou zobrazeny následující pomocné spoje značek:</span><span class="sxs-lookup"><span data-stu-id="b653b-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b653b-198">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="b653b-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b653b-199">Visual Studio zobrazí následující značky v rozlišovací tučné písmo používané pro tag pomocné síly:</span><span class="sxs-lookup"><span data-stu-id="b653b-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="b653b-200">Tento `<form method="post">` prvek je [pomocník značek formulářů](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b653b-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="b653b-201">Pomocník s značkami formuláře automaticky obsahuje [token antiforgery](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="b653b-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="b653b-202">Modul lešení vytvoří značky Razor pro každé pole v modelu (kromě ID) podobné následujícímu:</span><span class="sxs-lookup"><span data-stu-id="b653b-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="b653b-203">Pomocníci [značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ověření`<div asp-validation-summary` `<span asp-validation-for`(a) zobrazují chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="b653b-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="b653b-204">Ověření je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="b653b-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="b653b-205">Pomocník [značky popisků](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje `for` popisek `Title` a atribut vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b653b-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="b653b-206">Pomocník [vstupníznačky](xref:mvc/views/working-with-forms) `<input asp-for="Movie.Title" class="form-control">`( ) používá [atributy DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="b653b-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="b653b-207">Další informace o pomocné `<form method="post">`spoje značek, například , naleznete [v tématu Pomocníci značek v ASP.NET jádru](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="b653b-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b653b-208">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b653b-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b653b-209">[Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: Databáze](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="b653b-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b653b-210">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b653b-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b653b-211">Tento kurz zkoumá Razor stránky vytvořené lešení v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="b653b-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="b653b-212">[Prohlédněte si nebo stáhněte](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) ukázku.</span><span class="sxs-lookup"><span data-stu-id="b653b-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="b653b-213">Stránky Vytvořit, Odstranit, Podrobnosti a Upravit</span><span class="sxs-lookup"><span data-stu-id="b653b-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="b653b-214">Prohlédněte si stránkový model *Pages/Movies/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="b653b-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="b653b-215">Razor Stránky jsou `PageModel`odvozeny od .</span><span class="sxs-lookup"><span data-stu-id="b653b-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="b653b-216">Podle konvence `PageModel`-derived třídy `<PageName>Model`se nazývá .</span><span class="sxs-lookup"><span data-stu-id="b653b-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="b653b-217">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) `RazorPagesMovieContext` k přidání na stránku.</span><span class="sxs-lookup"><span data-stu-id="b653b-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="b653b-218">Všechny lešení stránky následovat tento vzor.</span><span class="sxs-lookup"><span data-stu-id="b653b-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="b653b-219">Další informace o asynchronním programování pomocí entity Framework naleznete v [tématu Asynchronní kód.](xref:data/ef-rp/intro#asynchronous-code)</span><span class="sxs-lookup"><span data-stu-id="b653b-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="b653b-220">Když je požadavek na stránku, `OnGetAsync` metoda vrátí seznam filmů na razor page.</span><span class="sxs-lookup"><span data-stu-id="b653b-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="b653b-221">`OnGetAsync`nebo `OnGet` je volána na Razor Page inicializovat stav stránky.</span><span class="sxs-lookup"><span data-stu-id="b653b-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="b653b-222">V tomto `OnGetAsync` případě získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="b653b-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="b653b-223">Při `OnGet` `void` vrácení `OnGetAsync` `Task`nebo vrátí , je použita žádná metoda vrácení.</span><span class="sxs-lookup"><span data-stu-id="b653b-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="b653b-224">Pokud je `IActionResult` návratový `Task<IActionResult>`typ nebo , musí být zadán příkaz return.</span><span class="sxs-lookup"><span data-stu-id="b653b-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="b653b-225">Například metoda *Pages/Movies/Create.cshtml.cs:* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="b653b-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="b653b-226">Prohlédněte si *stránku Pages/Movies/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="b653b-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="b653b-227">Holicí strojek může přejít z HTML do Jazyka C# nebo do značky specifické pro razor.</span><span class="sxs-lookup"><span data-stu-id="b653b-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="b653b-228">Pokud `@` symbol následuje [razor vyhrazené klíčové slovo](xref:mvc/views/razor#razor-reserved-keywords), přechody do razor-specifické značky, jinak se přejde do C#.</span><span class="sxs-lookup"><span data-stu-id="b653b-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="b653b-229">Razor `@page` směrnice vytvoří soubor do akce MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="b653b-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="b653b-230">`@page`musí být první direktivou Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="b653b-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="b653b-231">`@page`je příkladem přechodu na značky specifické pro Razor.</span><span class="sxs-lookup"><span data-stu-id="b653b-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="b653b-232">Další informace naleznete v [tématu Syntaxe břitvy.](xref:mvc/views/razor#razor-syntax)</span><span class="sxs-lookup"><span data-stu-id="b653b-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="b653b-233">Zkontrolujte výraz lambda použitý v následujícím pomocném prvku HTML:</span><span class="sxs-lookup"><span data-stu-id="b653b-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="b653b-234">Pomocník `DisplayNameFor` HTML zkontroluje `Title` vlastnost, na kterou odkazuje výraz lambda, a určí tak zobrazovaný název.</span><span class="sxs-lookup"><span data-stu-id="b653b-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="b653b-235">Lambda výraz je kontrolována spíše než vyhodnocena.</span><span class="sxs-lookup"><span data-stu-id="b653b-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="b653b-236">To znamená, že neexistuje `model` `model.Movie`žádné `model.Movie[0]` narušení `null` přístupu, když , , nebo jsou nebo prázdné.</span><span class="sxs-lookup"><span data-stu-id="b653b-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="b653b-237">Při vyhodnocení výrazu lambda (například s `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnoceny hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="b653b-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="b653b-238">Směrnice @model</span><span class="sxs-lookup"><span data-stu-id="b653b-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="b653b-239">Směrnice `@model` určuje typ modelu předaného razor Page.</span><span class="sxs-lookup"><span data-stu-id="b653b-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="b653b-240">V předchozím příkladu `@model` řádek zpřístupňuje `PageModel`-derived třídy Razor Page.</span><span class="sxs-lookup"><span data-stu-id="b653b-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="b653b-241">Model se používá `@Html.DisplayNameFor` v `@Html.DisplayFor` a [HTML pomocníky](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.</span><span class="sxs-lookup"><span data-stu-id="b653b-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="b653b-242">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="b653b-242">The layout page</span></span>

<span data-ttu-id="b653b-243">Vyberte odkazy na nabídky **(RazorPagesMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="b653b-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="b653b-244">Každá stránka zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="b653b-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="b653b-245">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b653b-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b653b-246">Otevřete soubor *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b653b-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b653b-247">[Šablony rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejnerů HTML webu na jednom místě a pak ho použít na více stránkách webu.</span><span class="sxs-lookup"><span data-stu-id="b653b-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="b653b-248">Najděte `@RenderBody()` linku.</span><span class="sxs-lookup"><span data-stu-id="b653b-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b653b-249">`RenderBody`je zástupný symbol, ve kterém se zobrazují všechna vytvářená zobrazení specifická pro *stránku, která jsou zabalena* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="b653b-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b653b-250">Pokud například vyberete odkaz **Soukromí,** zobrazení **Stránky/Privacy.cshtml** se `RenderBody` vykreslí uvnitř metody.</span><span class="sxs-lookup"><span data-stu-id="b653b-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="b653b-251">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="b653b-251">ViewData and layout</span></span>

<span data-ttu-id="b653b-252">Zvažte následující kód ze souboru *Pages/Movies/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="b653b-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="b653b-253">Předchozí zvýrazněný kód je příkladem razor přechodu do Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="b653b-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="b653b-254">Znaky `{` `}` a uzavřete blok kódu Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="b653b-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="b653b-255">Základní `PageModel` třída má `ViewData` vlastnost dictionary, kterou lze použít k přidání dat, která chcete předat zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b653b-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="b653b-256">Objekty přidáte `ViewData` do slovníku pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="b653b-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="b653b-257">V předchozí ukázce je do slovníku `ViewData` přidána vlastnost "Title".</span><span class="sxs-lookup"><span data-stu-id="b653b-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="b653b-258">Vlastnost "Název" se používá v souboru *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b653b-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b653b-259">Následující značky zobrazují několik prvních řádků souboru *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b653b-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="b653b-260">Řádek `@*Markup removed for brevity.*@` je razor komentář, který se nezobrazí v souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="b653b-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="b653b-261">Na rozdíl`<!-- -->`od html komentáře ( ), Razor komentáře nejsou odesílány klientovi.</span><span class="sxs-lookup"><span data-stu-id="b653b-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="b653b-262">Aktualizace rozložení</span><span class="sxs-lookup"><span data-stu-id="b653b-262">Update the layout</span></span>

<span data-ttu-id="b653b-263">Změňte `<title>` prvek v souboru *Pages/Shared/_Layout.cshtml* tak, aby zobrazoval **film** místo **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="b653b-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="b653b-264">V souboru *Stránky/Sdílené/_Layout.cshtml* najdete následující prvek ukotvení.</span><span class="sxs-lookup"><span data-stu-id="b653b-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="b653b-265">Nahraďte předchozí prvek následujícíznačkou.</span><span class="sxs-lookup"><span data-stu-id="b653b-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="b653b-266">Předchozí prvek ukotvení je [Tag Helper](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="b653b-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="b653b-267">V tomto případě je to [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b653b-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="b653b-268">Tag `asp-page="/Movies/Index"` Helper atribut a hodnota vytvoří `/Movies/Index` odkaz na holicí strojek stránku.</span><span class="sxs-lookup"><span data-stu-id="b653b-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="b653b-269">Hodnota `asp-area` atributu je prázdná, takže oblast není použita v odkazu.</span><span class="sxs-lookup"><span data-stu-id="b653b-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="b653b-270">Další informace naleznete v [části Oblasti.](xref:mvc/controllers/areas)</span><span class="sxs-lookup"><span data-stu-id="b653b-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="b653b-271">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie.**</span><span class="sxs-lookup"><span data-stu-id="b653b-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="b653b-272">Pokud máte nějaké problémy, podívejte se na soubor [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) v GitHubu.</span><span class="sxs-lookup"><span data-stu-id="b653b-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="b653b-273">Otestujte ostatní odkazy **(Domů**, **RpMovie**, **Vytvořit**, **Upravit**a **Odstranit).**</span><span class="sxs-lookup"><span data-stu-id="b653b-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="b653b-274">Každá stránka nastaví název, který můžete vidět na kartě prohlížeče. Když záložku přidáte do záložek, použije se pro záložku název.</span><span class="sxs-lookup"><span data-stu-id="b653b-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="b653b-275">Je možné, že do pole nebudete moci `Price` zadat desetinná čárka.</span><span class="sxs-lookup"><span data-stu-id="b653b-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b653b-276">Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a formáty dat mimo americkou angličtinu, musíte podniknout kroky ke globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b653b-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="b653b-277">Tento [problém GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) pro pokyny k přidání desetinné čárky.</span><span class="sxs-lookup"><span data-stu-id="b653b-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="b653b-278">Vlastnost `Layout` je nastavena v souboru *Pages/_ViewStart.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="b653b-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="b653b-279">Předchozí značky nastaví soubor rozvržení na *Pages/Shared/_Layout.cshtml* pro všechny soubory Razor ve složce *Pages.*</span><span class="sxs-lookup"><span data-stu-id="b653b-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="b653b-280">Další informace najdete [v tématu Rozložení.](xref:razor-pages/index#layout)</span><span class="sxs-lookup"><span data-stu-id="b653b-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="b653b-281">Vytvořit model stránky</span><span class="sxs-lookup"><span data-stu-id="b653b-281">The Create page model</span></span>

<span data-ttu-id="b653b-282">Prohlédněte si model stránky *Pages/Movies/Create.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="b653b-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="b653b-283">Metoda `OnGet` inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="b653b-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="b653b-284">Create stránka nemá žádný stav inicializovat, `Page` tak je vrácena.</span><span class="sxs-lookup"><span data-stu-id="b653b-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="b653b-285">Později v kurzu `OnGet` uvidíte, že metoda inicializovat stav.</span><span class="sxs-lookup"><span data-stu-id="b653b-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="b653b-286">Metoda `Page` vytvoří `PageResult` objekt, který vykreslí *create.cshtml* stránku.</span><span class="sxs-lookup"><span data-stu-id="b653b-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="b653b-287">Vlastnost `Movie` používá `[BindProperty]` atribut opt-in pro [vazby modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="b653b-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="b653b-288">Když formulář Vytvořit zaúčtuje hodnoty formuláře, ASP.NET core runtime `Movie` sváže zaúčtované hodnoty s modelem.</span><span class="sxs-lookup"><span data-stu-id="b653b-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="b653b-289">Metoda `OnPostAsync` je spuštěna, když stránka odešle data formuláře:</span><span class="sxs-lookup"><span data-stu-id="b653b-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="b653b-290">Pokud dojde k chybám modelu, formulář se znovu zobrazí spolu se všemi zaúčtovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="b653b-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="b653b-291">Většina chyb modelu může být zachycena na straně klienta před zaúčtováním formuláře.</span><span class="sxs-lookup"><span data-stu-id="b653b-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="b653b-292">Příkladem chyby modelu je zaúčtování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="b653b-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="b653b-293">Ověření na straně klienta a ověření modelu jsou popsány dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="b653b-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="b653b-294">Pokud nejsou žádné chyby modelu, data se uloží a prohlížeč je přesměrován na stránku Index.</span><span class="sxs-lookup"><span data-stu-id="b653b-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="b653b-295">Stránka Vytvořit holicí strojek</span><span class="sxs-lookup"><span data-stu-id="b653b-295">The Create Razor Page</span></span>

<span data-ttu-id="b653b-296">Prohlédněte si stránkovací soubor *Pages/Movies/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="b653b-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="b653b-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b653b-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b653b-298">Visual Studio `<form method="post">` zobrazí značku výrazným tučným písmem používaným pro pomocné síly značek:</span><span class="sxs-lookup"><span data-stu-id="b653b-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Zobrazení VS17 na stránce Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b653b-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b653b-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b653b-301">Další informace o pomocné `<form method="post">`spoje značek, například , naleznete [v tématu Pomocníci značek v ASP.NET jádru](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="b653b-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b653b-302">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="b653b-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b653b-303">Visual Studio pro `<form method="post">` Mac zobrazí značku výrazným tučným písmem používaným pro pomocné rutiny značek.</span><span class="sxs-lookup"><span data-stu-id="b653b-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="b653b-304">Tento `<form method="post">` prvek je [pomocník značek formulářů](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b653b-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="b653b-305">Pomocník s značkami formuláře automaticky obsahuje [token antiforgery](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="b653b-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="b653b-306">Modul lešení vytvoří značky Razor pro každé pole v modelu (kromě ID) podobné následujícímu:</span><span class="sxs-lookup"><span data-stu-id="b653b-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="b653b-307">Pomocníci [značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ověření`<div asp-validation-summary` `<span asp-validation-for`(a) zobrazují chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="b653b-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="b653b-308">Ověření je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="b653b-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="b653b-309">Pomocník [značky popisků](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje `for` popisek `Title` a atribut vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b653b-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="b653b-310">Pomocník [vstupníznačky](xref:mvc/views/working-with-forms) `<input asp-for="Movie.Title" class="form-control">`( ) používá [atributy DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="b653b-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b653b-311">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b653b-311">Additional resources</span></span>

* [<span data-ttu-id="b653b-312">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="b653b-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="b653b-313">[Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: Databáze](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="b653b-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
