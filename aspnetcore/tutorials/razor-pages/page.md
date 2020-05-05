---
title: Vygenerované Razor stránky v ASP.NET Core
author: rick-anderson
description: Vysvětluje Razor stránky generované pomocí generování uživatelského rozhraní.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 22afbc729cc73427b3d04bee379534cda38b39bd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774844"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="bee57-103">Vygenerované Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bee57-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bee57-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bee57-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bee57-105">Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="bee57-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="bee57-106">Stránky vytvořit, odstranit, podrobnosti a upravit</span><span class="sxs-lookup"><span data-stu-id="bee57-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="bee57-107">Projděte si model stránky *stránky/filmy/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="bee57-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="bee57-108">Razor Pages jsou odvozeny `PageModel`z.</span><span class="sxs-lookup"><span data-stu-id="bee57-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="bee57-109">Podle konvence `PageModel`je volána `<PageName>Model`třída odvozená od třídy.</span><span class="sxs-lookup"><span data-stu-id="bee57-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="bee57-110">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání `RazorPagesMovieContext` na stránku.</span><span class="sxs-lookup"><span data-stu-id="bee57-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="bee57-111">Všechny vygenerované stránky následují tento model.</span><span class="sxs-lookup"><span data-stu-id="bee57-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="bee57-112">Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="bee57-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="bee57-113">Když je vytvořena žádost pro stránku, `OnGetAsync` metoda vrátí seznam filmů na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="bee57-114">`OnGetAsync`nebo `OnGet` se volá k inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="bee57-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="bee57-115">V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="bee57-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="bee57-116">Při `OnGet` vrácení `void` nebo `OnGetAsync` `Task`vrácení se nepoužije žádný návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="bee57-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="bee57-117">Pokud je `IActionResult` návratový typ nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="bee57-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="bee57-118">Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="bee57-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="bee57-119">Projděte stránku *stránky/filmy/index. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="bee57-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="bee57-120">Razor může přejít z HTML do C# nebo do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="bee57-121">Je- `@` li symbol následován [rezervovaným klíčovým slovem Razor](xref:mvc/views/razor#razor-reserved-keywords), přejde do kódu specifického pro Razor, jinak přejde do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="bee57-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="bee57-122">@page Direktiva</span><span class="sxs-lookup"><span data-stu-id="bee57-122">The @page directive</span></span>

<span data-ttu-id="bee57-123">Direktiva `@page` Razor vytvoří soubor akce MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="bee57-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="bee57-124">`@page`musí se jednat o první direktivu Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="bee57-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bee57-125">`@page`je příkladem přechodu do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="bee57-126">Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="bee57-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="bee57-127">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="bee57-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="bee57-128">Pomocník `DisplayNameFor` HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu.</span><span class="sxs-lookup"><span data-stu-id="bee57-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="bee57-129">Výraz lambda je zkontrolován namísto vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="bee57-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="bee57-130">To znamená, že nedojde k narušení přístupu `model`, `model.Movie`Pokud je `model.Movie[0]` , `null` nebo je nebo prázdné.</span><span class="sxs-lookup"><span data-stu-id="bee57-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="bee57-131">Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="bee57-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="bee57-132">@model Direktiva</span><span class="sxs-lookup"><span data-stu-id="bee57-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="bee57-133">`@model` Direktiva určuje typ modelu předaného na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="bee57-134">V předchozím příkladu `@model` čára zpřístupňuje `PageModel`třídu odvozenou pro stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="bee57-135">Model se používá v `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a na stránce.</span><span class="sxs-lookup"><span data-stu-id="bee57-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="bee57-136">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="bee57-136">The layout page</span></span>

<span data-ttu-id="bee57-137">Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="bee57-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="bee57-138">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="bee57-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="bee57-139">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bee57-140">Otevřete soubor *stránky/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bee57-141">Šablony [rozložení](xref:mvc/views/layout) umožňují, aby rozložení kontejneru HTML bylo:</span><span class="sxs-lookup"><span data-stu-id="bee57-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="bee57-142">Zadáno na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="bee57-142">Specified in one place.</span></span>
* <span data-ttu-id="bee57-143">Použito na více stránkách v lokalitě.</span><span class="sxs-lookup"><span data-stu-id="bee57-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="bee57-144">Najděte `@RenderBody()` řádek.</span><span class="sxs-lookup"><span data-stu-id="bee57-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="bee57-145">`RenderBody`je zástupný symbol, kde jsou všechna zobrazení konkrétní stránky zobrazena, *zabalena* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="bee57-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="bee57-146">Například vyberte odkaz **osobních údajů** a zobrazení *stránky/soukromí. cshtml* je vykresleno uvnitř `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="bee57-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="bee57-147">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="bee57-147">ViewData and layout</span></span>

<span data-ttu-id="bee57-148">Vezměte v úvahu následující značky ze souboru *Pages/Movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bee57-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="bee57-149">Předchozí zvýrazněný kód je příkladem přechodu Razor do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="bee57-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="bee57-150">Znaky `{` a `}` jsou uzavřeny v bloku kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="bee57-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="bee57-151">`PageModel` Základní třída obsahuje vlastnost `ViewData` Dictionary, kterou lze použít k předání dat do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bee57-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="bee57-152">Objekty jsou přidány do `ViewData` slovníku pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="bee57-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="bee57-153">V předchozí ukázce je `"Title"` vlastnost přidána do `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="bee57-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="bee57-154">Tato `"Title"` vlastnost se používá v souboru *Pages/shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bee57-155">Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="bee57-156">Řádek `@*Markup removed for brevity.*@` je komentář Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="bee57-157">Na rozdíl od komentářů jazyka`<!-- -->`HTML () nejsou komentáře pro Razor odesílány klientovi.</span><span class="sxs-lookup"><span data-stu-id="bee57-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="bee57-158">Aktualizovat rozložení</span><span class="sxs-lookup"><span data-stu-id="bee57-158">Update the layout</span></span>

<span data-ttu-id="bee57-159">Změňte `<title>` prvek v souboru *Pages/shared/_Layout. cshtml* tak, aby se místo **RazorPagesMovie**zobrazoval **film** .</span><span class="sxs-lookup"><span data-stu-id="bee57-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="bee57-160">Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="bee57-161">Nahraďte předchozí prvek následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bee57-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="bee57-162">Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bee57-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bee57-163">V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bee57-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="bee57-164">Atribut `asp-page="/Movies/Index"` pomocník značek a hodnota vytvoří odkaz na stránku `/Movies/Index` Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="bee57-165">Hodnota `asp-area` atributu je prázdná, takže se oblast nepoužívá v odkazu.</span><span class="sxs-lookup"><span data-stu-id="bee57-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="bee57-166">Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="bee57-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="bee57-167">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="bee57-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="bee57-168">Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="bee57-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="bee57-169">Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**).</span><span class="sxs-lookup"><span data-stu-id="bee57-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="bee57-170">Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.</span><span class="sxs-lookup"><span data-stu-id="bee57-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="bee57-171">V `Price` poli možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="bee57-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bee57-172">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="bee57-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="bee57-173">Pokyny k přidání desetinné čárky najdete v tomto [problému v githubu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="bee57-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="bee57-174">`Layout` Vlastnost je nastavena v souboru *pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bee57-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bee57-175">Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="bee57-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="bee57-176">Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="bee57-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="bee57-177">Model vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="bee57-177">The Create page model</span></span>

<span data-ttu-id="bee57-178">Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="bee57-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="bee57-179">`OnGet` Metoda inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="bee57-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="bee57-180">Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí.</span><span class="sxs-lookup"><span data-stu-id="bee57-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="bee57-181">Později v tomto kurzu se zobrazuje příklad stavu `OnGet` inicializace.</span><span class="sxs-lookup"><span data-stu-id="bee57-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="bee57-182">`Page` Metoda vytvoří `PageResult` objekt, který vykresluje stránku *vytvořit. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="bee57-183">`Movie` Vlastnost používá `[BindProperty]` atribut pro přihlášení k [modelu vazby](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="bee57-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="bee57-184">Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="bee57-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="bee57-185">`OnPostAsync` Metoda se spustí, když stránka publikuje data formuláře:</span><span class="sxs-lookup"><span data-stu-id="bee57-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="bee57-186">Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="bee57-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="bee57-187">Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="bee57-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="bee57-188">Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="bee57-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="bee57-189">Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="bee57-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="bee57-190">Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="bee57-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="bee57-191">Stránka vytvoření Razor</span><span class="sxs-lookup"><span data-stu-id="bee57-191">The Create Razor Page</span></span>

<span data-ttu-id="bee57-192">Projděte si soubor *Pages/video/Create. cshtml* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="bee57-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="bee57-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bee57-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bee57-194">Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="bee57-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bee57-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bee57-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bee57-197">V předchozím kódu se zobrazují následující pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="bee57-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bee57-198">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bee57-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bee57-199">Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="bee57-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="bee57-200">`<form method="post">` Element je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bee57-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="bee57-201">Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="bee57-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="bee57-202">Modul generování uživatelského rozhraní vytvoří pro každé pole v modelu označení Razor (s výjimkou ID), které je podobné následujícímu:</span><span class="sxs-lookup"><span data-stu-id="bee57-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="bee57-203">[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazí chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="bee57-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="bee57-204">Ověřování je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="bee57-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="bee57-205">[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) vygeneruje popisek a `for` atribut pro `Title` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bee57-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="bee57-206">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="bee57-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="bee57-207">Další informace o Pomocníkech značek `<form method="post">`, jako je, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bee57-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bee57-208">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bee57-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bee57-209">[Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> ([Další): databáze](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="bee57-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bee57-210">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bee57-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bee57-211">Tento kurz prověřuje Razor Pages vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="bee57-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="bee57-212">[Zobrazit nebo stáhnout](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) ukázku.</span><span class="sxs-lookup"><span data-stu-id="bee57-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="bee57-213">Stránky vytvořit, odstranit, podrobnosti a upravit</span><span class="sxs-lookup"><span data-stu-id="bee57-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="bee57-214">Projděte si model stránky *stránky/filmy/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="bee57-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="bee57-215">Razor Pages jsou odvozeny `PageModel`z.</span><span class="sxs-lookup"><span data-stu-id="bee57-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="bee57-216">Podle konvence `PageModel`je volána `<PageName>Model`třída odvozená od třídy.</span><span class="sxs-lookup"><span data-stu-id="bee57-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="bee57-217">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání `RazorPagesMovieContext` na stránku.</span><span class="sxs-lookup"><span data-stu-id="bee57-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="bee57-218">Všechny vygenerované stránky následují tento model.</span><span class="sxs-lookup"><span data-stu-id="bee57-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="bee57-219">Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="bee57-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="bee57-220">Když je vytvořena žádost pro stránku, `OnGetAsync` metoda vrátí seznam filmů na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="bee57-221">`OnGetAsync`nebo `OnGet` je volána na stránce Razor pro inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="bee57-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="bee57-222">V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="bee57-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="bee57-223">Při `OnGet` vrácení `void` nebo `OnGetAsync` `Task`vrácení se nepoužije žádná návratová metoda.</span><span class="sxs-lookup"><span data-stu-id="bee57-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="bee57-224">Pokud je `IActionResult` návratový typ nebo `Task<IActionResult>`, je nutné poskytnout návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="bee57-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="bee57-225">Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="bee57-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="bee57-226">Projděte stránku *stránky/filmy/index. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="bee57-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="bee57-227">Razor může přejít z HTML do C# nebo do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="bee57-228">Je- `@` li symbol následován [rezervovaným klíčovým slovem Razor](xref:mvc/views/razor#razor-reserved-keywords), přejde do kódu specifického pro Razor, jinak přejde do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="bee57-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="bee57-229">Direktiva `@page` Razor vytvoří soubor na akci MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="bee57-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="bee57-230">`@page`musí se jednat o první direktivu Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="bee57-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bee57-231">`@page`je příkladem přechodu do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="bee57-232">Další informace najdete v tématu [syntaxe Razor](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="bee57-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="bee57-233">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="bee57-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="bee57-234">Pomocník `DisplayNameFor` HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu.</span><span class="sxs-lookup"><span data-stu-id="bee57-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="bee57-235">Výraz lambda je zkontrolován namísto vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="bee57-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="bee57-236">To znamená, že nedojde k narušení přístupu `model`, `model.Movie`Pokud `model.Movie[0]` jsou `null` nebo prázdná.</span><span class="sxs-lookup"><span data-stu-id="bee57-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="bee57-237">Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="bee57-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="bee57-238">@model Direktiva</span><span class="sxs-lookup"><span data-stu-id="bee57-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="bee57-239">`@model` Direktiva určuje typ modelu předaného na stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="bee57-240">V předchozím příkladu `@model` čára zpřístupňuje `PageModel`třídu odvozenou pro stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="bee57-241">Model se používá v `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a na stránce.</span><span class="sxs-lookup"><span data-stu-id="bee57-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="bee57-242">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="bee57-242">The layout page</span></span>

<span data-ttu-id="bee57-243">Vyberte odkazy nabídky (**RazorPagesMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="bee57-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="bee57-244">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="bee57-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="bee57-245">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bee57-246">Otevřete soubor *stránky/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bee57-247">Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu.</span><span class="sxs-lookup"><span data-stu-id="bee57-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="bee57-248">Najděte `@RenderBody()` řádek.</span><span class="sxs-lookup"><span data-stu-id="bee57-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="bee57-249">`RenderBody`je zástupný symbol, kde jsou všechna zobrazení pro konkrétní stránku, která vytvoříte, *zabalena* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="bee57-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="bee57-250">Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **stránky/soukromí. cshtml** se vykreslí uvnitř `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="bee57-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="bee57-251">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="bee57-251">ViewData and layout</span></span>

<span data-ttu-id="bee57-252">Vezměte v úvahu následující kód ze souboru *Pages/Movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bee57-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="bee57-253">Předchozí zvýrazněný kód je příkladem přechodu Razor do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="bee57-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="bee57-254">Znaky `{` a `}` jsou uzavřeny v bloku kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="bee57-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="bee57-255">`PageModel` Základní třída má vlastnost `ViewData` Dictionary, kterou lze použít k přidání dat, která chcete předat zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bee57-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="bee57-256">Do `ViewData` slovníku přidáte objekty pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="bee57-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="bee57-257">V předchozím příkladu je do `ViewData` slovníku přidána vlastnost title.</span><span class="sxs-lookup"><span data-stu-id="bee57-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="bee57-258">Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bee57-259">Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="bee57-260">Řádek `@*Markup removed for brevity.*@` je komentář Razor, který se v souboru rozložení nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="bee57-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="bee57-261">Na rozdíl od komentářů jazyka`<!-- -->`HTML () nejsou komentáře pro Razor odesílány klientovi.</span><span class="sxs-lookup"><span data-stu-id="bee57-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="bee57-262">Aktualizovat rozložení</span><span class="sxs-lookup"><span data-stu-id="bee57-262">Update the layout</span></span>

<span data-ttu-id="bee57-263">Změňte `<title>` prvek v souboru *Pages/shared/_Layout. cshtml* tak, aby se místo **RazorPagesMovie**zobrazoval **film** .</span><span class="sxs-lookup"><span data-stu-id="bee57-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="bee57-264">Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="bee57-265">Nahraďte předchozí prvek následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="bee57-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="bee57-266">Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bee57-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bee57-267">V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bee57-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="bee57-268">Atribut `asp-page="/Movies/Index"` pomocník značek a hodnota vytvoří odkaz na stránku `/Movies/Index` Razor.</span><span class="sxs-lookup"><span data-stu-id="bee57-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="bee57-269">Hodnota `asp-area` atributu je prázdná, takže se oblast nepoužívá v odkazu.</span><span class="sxs-lookup"><span data-stu-id="bee57-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="bee57-270">Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="bee57-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="bee57-271">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="bee57-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="bee57-272">Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="bee57-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="bee57-273">Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**).</span><span class="sxs-lookup"><span data-stu-id="bee57-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="bee57-274">Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.</span><span class="sxs-lookup"><span data-stu-id="bee57-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="bee57-275">V `Price` poli možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="bee57-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bee57-276">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="bee57-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="bee57-277">Tento [problém githubu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) najdete v pokynech k přidání desetinné čárky.</span><span class="sxs-lookup"><span data-stu-id="bee57-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="bee57-278">`Layout` Vlastnost je nastavena v souboru *pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bee57-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bee57-279">Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny soubory Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="bee57-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="bee57-280">Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="bee57-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="bee57-281">Model vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="bee57-281">The Create page model</span></span>

<span data-ttu-id="bee57-282">Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="bee57-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="bee57-283">`OnGet` Metoda inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="bee57-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="bee57-284">Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí.</span><span class="sxs-lookup"><span data-stu-id="bee57-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="bee57-285">Později v tomto kurzu vidíte `OnGet` stav inicializace metody.</span><span class="sxs-lookup"><span data-stu-id="bee57-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="bee57-286">`Page` Metoda vytvoří `PageResult` objekt, který vykresluje stránku *vytvořit. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee57-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="bee57-287">`Movie` Vlastnost používá `[BindProperty]` atribut pro přihlášení k [modelu vazby](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="bee57-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="bee57-288">Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="bee57-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="bee57-289">`OnPostAsync` Metoda se spustí, když stránka publikuje data formuláře:</span><span class="sxs-lookup"><span data-stu-id="bee57-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="bee57-290">Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="bee57-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="bee57-291">Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="bee57-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="bee57-292">Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="bee57-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="bee57-293">Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="bee57-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="bee57-294">Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="bee57-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="bee57-295">Stránka vytvoření Razor</span><span class="sxs-lookup"><span data-stu-id="bee57-295">The Create Razor Page</span></span>

<span data-ttu-id="bee57-296">Projděte si soubor *Pages/video/Create. cshtml* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="bee57-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="bee57-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bee57-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bee57-298">Visual Studio zobrazí značku `<form method="post">` v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="bee57-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bee57-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bee57-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bee57-301">Další informace o Pomocníkech značek `<form method="post">`, jako je, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bee57-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bee57-302">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bee57-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bee57-303">Visual Studio pro Mac zobrazí značku `<form method="post">` v zřetelném tučném písmu, které se používá pro pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="bee57-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="bee57-304">`<form method="post">` Element je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bee57-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="bee57-305">Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="bee57-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="bee57-306">Modul generování uživatelského rozhraní vytvoří Razor značky pro každé pole v modelu (s VÝJIMKou identifikátoru), který je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="bee57-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="bee57-307">[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazí chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="bee57-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="bee57-308">Ověřování je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="bee57-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="bee57-309">[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) vygeneruje popisek a `for` atribut pro `Title` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bee57-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="bee57-310">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="bee57-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bee57-311">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bee57-311">Additional resources</span></span>

* [<span data-ttu-id="bee57-312">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="bee57-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="bee57-313">[Předchozí: Přidání modelu](xref:tutorials/razor-pages/model)
> ([Další): databáze](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="bee57-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
