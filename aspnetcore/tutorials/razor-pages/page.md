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
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="f3a1b-103">Vygenerované Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3a1b-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3a1b-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f3a1b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f3a1b-105">Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="f3a1b-106">Stránky vytvořit, odstranit, podrobnosti a upravit</span><span class="sxs-lookup"><span data-stu-id="f3a1b-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="f3a1b-107">Projděte si model stránky *stránky/filmy/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="f3a1b-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="f3a1b-108">Razor Pages jsou odvozeny z `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="f3a1b-109">Podle konvence je třída odvozená od `PageModel`volána `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="f3a1b-110">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro přidání `RazorPagesMovieContext` na stránku.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="f3a1b-111">Všechny vygenerované stránky následují tento model.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="f3a1b-112">Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="f3a1b-113">Když je pro stránku vytvořen požadavek, metoda `OnGetAsync` vrátí seznam filmů na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="f3a1b-114">je volána `OnGetAsync` nebo `OnGet` k inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="f3a1b-115">V takovém případě `OnGetAsync` získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="f3a1b-116">Když `OnGet` vrátí `void` nebo `OnGetAsync` vrátí`Task`, není použit žádný návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="f3a1b-117">Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="f3a1b-118">Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="f3a1b-119">Projděte stránku *stránky/filmy/index. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="f3a1b-120">Razor může přejít z formátu HTML C# do nebo do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="f3a1b-121">Je-li symbol `@` následovaný [rezervovaným klíčovým slovem Razor](xref:mvc/views/razor#razor-reserved-keywords), přejde do kódu specifického pro Razor, jinak přejde C#do.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="f3a1b-122">Direktiva @page</span><span class="sxs-lookup"><span data-stu-id="f3a1b-122">The @page directive</span></span>

<span data-ttu-id="f3a1b-123">Direktiva `@page` Razor vytvoří soubor akce MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="f3a1b-124">`@page` musí být první direktivou Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="f3a1b-125">`@page` je příklad přechodu do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="f3a1b-126">Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="f3a1b-127">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="f3a1b-128">Pomocník pro `DisplayNameFor` HTML kontroluje vlastnost `Title`, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="f3a1b-129">Výraz lambda je zkontrolován namísto vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="f3a1b-130">To znamená, že při `model`, `model.Movie`nebo `model.Movie[0]` není toto porušení přístupu `null` nebo je prázdné.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="f3a1b-131">Při vyhodnocování výrazu lambda (například s `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="f3a1b-132">Direktiva @model</span><span class="sxs-lookup"><span data-stu-id="f3a1b-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="f3a1b-133">Direktiva `@model` určuje typ modelu předaný na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="f3a1b-134">V předchozím příkladu `@model` řádek zpřístupňuje třídu odvozenou `PageModel`, která je k dispozici na stránce Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="f3a1b-135">Model se používá v `@Html.DisplayNameFor` a `@Html.DisplayFor` v [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="f3a1b-136">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="f3a1b-136">The layout page</span></span>

<span data-ttu-id="f3a1b-137">Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="f3a1b-138">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="f3a1b-139">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f3a1b-140">Otevřete soubor *stránky/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="f3a1b-141">Šablony [rozložení](xref:mvc/views/layout) umožňují, aby rozložení kontejneru HTML bylo:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="f3a1b-142">Zadáno na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-142">Specified in one place.</span></span>
* <span data-ttu-id="f3a1b-143">Použito na více stránkách v lokalitě.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="f3a1b-144">Najděte `@RenderBody()` řádek.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="f3a1b-145">`RenderBody` je zástupný symbol, ve kterém se všechna zobrazení pro konkrétní stránky zobrazí, *zabalená* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="f3a1b-146">Například vyberte odkaz **osobních údajů** a zobrazení *stránky/soukromí. cshtml* se vykresluje v rámci metody `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="f3a1b-147">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="f3a1b-147">ViewData and layout</span></span>

<span data-ttu-id="f3a1b-148">Vezměte v úvahu následující značky ze souboru *Pages/Movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f3a1b-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="f3a1b-149">Předchozí zvýrazněný kód je příkladem přechodu Razor do C#.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="f3a1b-150">Znaky `{` a `}` obklopují blok C# kódu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="f3a1b-151">Základní třída `PageModel` obsahuje vlastnost `ViewData` slovníku, která se dá použít k předání dat do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="f3a1b-152">Objekty jsou přidány do `ViewData` slovníku pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="f3a1b-153">V předchozím příkladu je do slovníku `ViewData` přidána vlastnost `"Title"`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="f3a1b-154">Vlastnost `"Title"` se používá v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f3a1b-155">Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="f3a1b-156">Řádková `@*Markup removed for brevity.*@` je komentář Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="f3a1b-157">Na rozdíl od komentářů ve formátu HTML (`<!-- -->`) se do klienta neodesílají komentáře Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="f3a1b-158">Aktualizovat rozložení</span><span class="sxs-lookup"><span data-stu-id="f3a1b-158">Update the layout</span></span>

<span data-ttu-id="f3a1b-159">Změňte `<title>` prvek v souboru *Pages/Shared/_Layout. cshtml* pro zobrazení **filmu** místo **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="f3a1b-160">Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="f3a1b-161">Nahraďte předchozí prvek následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="f3a1b-162">Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="f3a1b-163">V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="f3a1b-164">Pomocný atribut `asp-page="/Movies/Index"` značky a hodnota vytvoří odkaz na stránku `/Movies/Index` Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="f3a1b-165">Hodnota atributu `asp-area` je prázdná, takže se oblast nepoužívá v odkazu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="f3a1b-166">Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="f3a1b-167">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="f3a1b-168">Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-168">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="f3a1b-169">Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="f3a1b-170">Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="f3a1b-171">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f3a1b-172">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="f3a1b-173">Pokyny k přidání desetinné čárky najdete v tomto [problému v githubu 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-173">See this [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="f3a1b-174">Vlastnost `Layout` je nastavena v souboru *Pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f3a1b-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="f3a1b-175">Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="f3a1b-176">Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="f3a1b-177">Model vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="f3a1b-177">The Create page model</span></span>

<span data-ttu-id="f3a1b-178">Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="f3a1b-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="f3a1b-179">Metoda `OnGet` inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="f3a1b-180">Stránka pro vytvoření nemá žádný stav k inicializaci, takže se vrátí `Page`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="f3a1b-181">Později v tomto kurzu se zobrazí příklad `OnGet` stav inicializace.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="f3a1b-182">Metoda `Page` vytvoří objekt `PageResult`, který vykresluje stránku *vytvořit. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="f3a1b-183">Vlastnost `Movie` používá atribut `[BindProperty]` pro přihlášení k [modelu vazby](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="f3a1b-184">Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do modelu `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="f3a1b-185">Metoda `OnPostAsync` se spustí, když stránka publikuje data formuláře:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="f3a1b-186">Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="f3a1b-187">Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="f3a1b-188">Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="f3a1b-189">Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="f3a1b-190">Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="f3a1b-191">Stránka vytvoření Razor</span><span class="sxs-lookup"><span data-stu-id="f3a1b-191">The Create Razor Page</span></span>

<span data-ttu-id="f3a1b-192">Projděte si soubor *Pages/video/Create. cshtml* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f3a1b-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3a1b-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3a1b-194">Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f3a1b-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3a1b-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f3a1b-197">V předchozím kódu se zobrazují následující pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f3a1b-198">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f3a1b-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f3a1b-199">Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="f3a1b-200">Element `<form method="post">` je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="f3a1b-201">Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="f3a1b-202">Modul generování uživatelského rozhraní vytvoří pro každé pole v modelu označení Razor (s výjimkou ID), které je podobné následujícímu:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="f3a1b-203">[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazuje chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="f3a1b-204">Ověřování je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="f3a1b-205">[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje popisek popisku a `for` atributu pro vlastnost `Title`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="f3a1b-206">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="f3a1b-207">Další informace o značkách pomocníka značek, jako je například `<form method="post">`, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3a1b-208">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f3a1b-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f3a1b-209">[Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: databáze](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="f3a1b-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3a1b-210">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f3a1b-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f3a1b-211">Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="f3a1b-212">[Zobrazení nebo stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) vzorku.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-212">[View or download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="f3a1b-213">Stránky vytvořit, odstranit, podrobnosti a upravit</span><span class="sxs-lookup"><span data-stu-id="f3a1b-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="f3a1b-214">Projděte si model stránky *stránky/filmy/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="f3a1b-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="f3a1b-215">Razor Pages jsou odvozeny z `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="f3a1b-216">Podle konvence je třída odvozená od `PageModel`volána `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="f3a1b-217">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro přidání `RazorPagesMovieContext` na stránku.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="f3a1b-218">Všechny vygenerované stránky následují tento model.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="f3a1b-219">Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="f3a1b-220">Když je pro stránku vytvořen požadavek, metoda `OnGetAsync` vrátí seznam filmů na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="f3a1b-221">`OnGetAsync` nebo `OnGet` je volána na stránce Razor pro inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="f3a1b-222">V takovém případě `OnGetAsync` získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="f3a1b-223">Když `OnGet` vrátí `void` nebo `OnGetAsync` vrátí`Task`, není použita žádná návratová metoda.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="f3a1b-224">Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="f3a1b-225">Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="f3a1b-226">Projděte stránku *stránky/filmy/index. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="f3a1b-227">Razor může přejít z formátu HTML C# do nebo do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="f3a1b-228">Je-li symbol `@` následovaný [rezervovaným klíčovým slovem Razor](xref:mvc/views/razor#razor-reserved-keywords), přejde do kódu specifického pro Razor, jinak přejde C#do.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="f3a1b-229">Direktiva `@page` Razor vytvoří soubor na akci MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="f3a1b-230">`@page` musí být první direktivou Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="f3a1b-231">`@page` je příklad přechodu do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="f3a1b-232">Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="f3a1b-233">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="f3a1b-234">Pomocník pro `DisplayNameFor` HTML kontroluje vlastnost `Title`, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="f3a1b-235">Výraz lambda je zkontrolován namísto vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="f3a1b-236">To znamená, že při `model`, `model.Movie`nebo `model.Movie[0]` nejsou `null` nebo jsou prázdné žádné porušení přístupu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="f3a1b-237">Při vyhodnocování výrazu lambda (například s `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="f3a1b-238">Direktiva @model</span><span class="sxs-lookup"><span data-stu-id="f3a1b-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="f3a1b-239">Direktiva `@model` určuje typ modelu předaný na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="f3a1b-240">V předchozím příkladu `@model` řádek zpřístupňuje třídu odvozenou `PageModel`, která je k dispozici na stránce Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="f3a1b-241">Model se používá v `@Html.DisplayNameFor` a `@Html.DisplayFor` v [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stránce.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="f3a1b-242">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="f3a1b-242">The layout page</span></span>

<span data-ttu-id="f3a1b-243">Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="f3a1b-244">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="f3a1b-245">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f3a1b-246">Otevřete soubor *stránky/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="f3a1b-247">Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="f3a1b-248">Najděte `@RenderBody()` řádek.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="f3a1b-249">`RenderBody` je zástupný symbol, kde jsou všechna zobrazení, která vytvoříte, *zabalena* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="f3a1b-250">Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **stránky/soukromí. cshtml** se vykreslí v rámci metody `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="f3a1b-251">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="f3a1b-251">ViewData and layout</span></span>

<span data-ttu-id="f3a1b-252">Vezměte v úvahu následující kód ze souboru *Pages/Movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f3a1b-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="f3a1b-253">Předchozí zvýrazněný kód je příkladem přechodu Razor do C#.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="f3a1b-254">Znaky `{` a `}` obklopují blok C# kódu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="f3a1b-255">Základní třída `PageModel` obsahuje vlastnost `ViewData` slovníku, kterou lze použít k přidání dat, která chcete předat zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="f3a1b-256">Do slovníku `ViewData` přidáte objekty pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="f3a1b-257">V předchozím příkladu je do slovníku `ViewData` přidána vlastnost title.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="f3a1b-258">Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f3a1b-259">Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="f3a1b-260">Řádková `@*Markup removed for brevity.*@` je komentář Razor, který se v souboru rozložení nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="f3a1b-261">Na rozdíl od komentářů ve formátu HTML (`<!-- -->`) se do klienta neodesílají komentáře Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="f3a1b-262">Aktualizovat rozložení</span><span class="sxs-lookup"><span data-stu-id="f3a1b-262">Update the layout</span></span>

<span data-ttu-id="f3a1b-263">Změňte `<title>` prvek v souboru *Pages/Shared/_Layout. cshtml* pro zobrazení **filmu** místo **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="f3a1b-264">Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="f3a1b-265">Nahraďte předchozí prvek následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="f3a1b-266">Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="f3a1b-267">V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="f3a1b-268">Pomocný atribut `asp-page="/Movies/Index"` značky a hodnota vytvoří odkaz na stránku `/Movies/Index` Razor.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="f3a1b-269">Hodnota atributu `asp-area` je prázdná, takže se oblast nepoužívá v odkazu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="f3a1b-270">Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="f3a1b-271">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="f3a1b-272">Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-272">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="f3a1b-273">Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="f3a1b-274">Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="f3a1b-275">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f3a1b-276">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="f3a1b-277">Tento [problém githubu 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) najdete v pokynech k přidání desetinné čárky.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-277">This [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="f3a1b-278">Vlastnost `Layout` je nastavena v souboru *Pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f3a1b-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="f3a1b-279">Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="f3a1b-280">Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="f3a1b-281">Model vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="f3a1b-281">The Create page model</span></span>

<span data-ttu-id="f3a1b-282">Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="f3a1b-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="f3a1b-283">Metoda `OnGet` inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="f3a1b-284">Stránka pro vytvoření nemá žádný stav k inicializaci, takže se vrátí `Page`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="f3a1b-285">Později v tomto kurzu vidíte `OnGet` stav inicializace metody.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="f3a1b-286">Metoda `Page` vytvoří objekt `PageResult`, který vykresluje stránku *vytvořit. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f3a1b-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="f3a1b-287">Vlastnost `Movie` používá atribut `[BindProperty]` pro přihlášení k [modelu vazby](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="f3a1b-288">Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do modelu `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="f3a1b-289">Metoda `OnPostAsync` se spustí, když stránka publikuje data formuláře:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="f3a1b-290">Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="f3a1b-291">Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="f3a1b-292">Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="f3a1b-293">Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="f3a1b-294">Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="f3a1b-295">Stránka vytvoření Razor</span><span class="sxs-lookup"><span data-stu-id="f3a1b-295">The Create Razor Page</span></span>

<span data-ttu-id="f3a1b-296">Projděte si soubor *Pages/video/Create. cshtml* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f3a1b-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3a1b-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3a1b-298">Visual Studio zobrazí značku `<form method="post">` v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f3a1b-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3a1b-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f3a1b-301">Další informace o značkách pomocníka značek, jako je například `<form method="post">`, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f3a1b-302">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f3a1b-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f3a1b-303">Visual Studio pro Mac zobrazuje značku `<form method="post">` v zřetelném tučném písmu, které se používá pro pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="f3a1b-304">Element `<form method="post">` je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="f3a1b-305">Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="f3a1b-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="f3a1b-306">Modul generování uživatelského rozhraní vytvoří pro každé pole v modelu označení Razor (s výjimkou ID), které je podobné následujícímu:</span><span class="sxs-lookup"><span data-stu-id="f3a1b-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="f3a1b-307">[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazuje chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="f3a1b-308">Ověřování je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="f3a1b-309">[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje popisek popisku a `for` atributu pro vlastnost `Title`.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="f3a1b-310">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f3a1b-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3a1b-311">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f3a1b-311">Additional resources</span></span>

* [<span data-ttu-id="f3a1b-312">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="f3a1b-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="f3a1b-313">[Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> [Další: databáze](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="f3a1b-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
