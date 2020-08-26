---
title: Část 3, vygenerované Razor stránky v ASP.NET Core
author: rick-anderson
description: Třetí část série kurzů na Razor stránkách.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 03febbd71df19cd3524d26e229a8bd8798a874b5
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865113"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="14808-103">Část 3, vygenerované Razor stránky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14808-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14808-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="14808-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="14808-105">V tomto kurzu se podíváme na Razor stránky vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="14808-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="14808-106">Stránky vytvořit, odstranit, podrobnosti a upravit</span><span class="sxs-lookup"><span data-stu-id="14808-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="14808-107">Projděte si model stránky *stránky/filmy/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="14808-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="14808-108">Razor Stránky jsou odvozeny z `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="14808-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="14808-109">Podle konvence `PageModel` je volána třída odvozená od třídy `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="14808-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="14808-110">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání na `RazorPagesMovieContext` stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="14808-111">Všechny vygenerované stránky následují tento model.</span><span class="sxs-lookup"><span data-stu-id="14808-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="14808-112">Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="14808-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="14808-113">Když je vytvořena žádost pro stránku, `OnGetAsync` Metoda vrátí seznam filmů na Razor stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="14808-114">`OnGetAsync` nebo `OnGet` se volá k inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="14808-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="14808-115">V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="14808-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="14808-116">Při vrácení nebo vrácení se `OnGet` `void` `OnGetAsync` `Task` nepoužije žádný návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="14808-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="14808-117">Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>` , je nutné poskytnout návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="14808-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="14808-118">Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="14808-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="14808-119">Projděte stránku *stránky/filmy/index. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="14808-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="14808-120">Razor může přejít z formátu HTML do jazyka C# nebo do Razor specifického kódu.</span><span class="sxs-lookup"><span data-stu-id="14808-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="14808-121">`@`Je-li symbol následován [ Razor vyhrazeným klíčovým slovem](xref:mvc/views/razor#razor-reserved-keywords), přechází na Razor konkrétní značku, jinak přejde do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="14808-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="14808-122">@pageDirektiva</span><span class="sxs-lookup"><span data-stu-id="14808-122">The @page directive</span></span>

<span data-ttu-id="14808-123">Tato `@page` Razor direktiva vytvoří soubor akce MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="14808-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="14808-124">`@page` musí se jednat o první Razor direktivu na stránce.</span><span class="sxs-lookup"><span data-stu-id="14808-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="14808-125">`@page` je příkladem přechodu na Razor konkrétní značku.</span><span class="sxs-lookup"><span data-stu-id="14808-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="14808-126">Další informace najdete v tématu [ Razor syntaxe](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="14808-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="14808-127">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="14808-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="14808-128">`DisplayNameFor`Pomocník HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu.</span><span class="sxs-lookup"><span data-stu-id="14808-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="14808-129">Výraz lambda je zkontrolován namísto vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="14808-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="14808-130">To znamená, že nedojde k narušení přístupu `model` , pokud `model.Movie` je, nebo `model.Movie[0]` je `null` nebo prázdné.</span><span class="sxs-lookup"><span data-stu-id="14808-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="14808-131">Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)` ) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="14808-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="14808-132">@modelDirektiva</span><span class="sxs-lookup"><span data-stu-id="14808-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="14808-133">`@model`Direktiva určuje typ modelu předaného na Razor stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="14808-134">V předchozím příkladu `@model` čára zpřístupňuje `PageModel` třídu odvozenou pro Razor stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="14808-135">Model se používá v `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a na stránce.</span><span class="sxs-lookup"><span data-stu-id="14808-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="14808-136">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="14808-136">The layout page</span></span>

<span data-ttu-id="14808-137">Vyberte odkazy nabídky (\*\* Razor PagesMovie\*\*, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="14808-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="14808-138">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="14808-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="14808-139">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="14808-140">Otevřete soubor *stránky/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="14808-141">Šablony [rozložení](xref:mvc/views/layout) umožňují, aby rozložení kontejneru HTML bylo:</span><span class="sxs-lookup"><span data-stu-id="14808-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="14808-142">Zadáno na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="14808-142">Specified in one place.</span></span>
* <span data-ttu-id="14808-143">Použito na více stránkách v lokalitě.</span><span class="sxs-lookup"><span data-stu-id="14808-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="14808-144">Najděte `@RenderBody()` řádek.</span><span class="sxs-lookup"><span data-stu-id="14808-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="14808-145">`RenderBody` je zástupný symbol, kde jsou všechna zobrazení konkrétní stránky zobrazena, *zabalena* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="14808-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="14808-146">Například vyberte odkaz **osobních údajů** a zobrazení *stránky/soukromí. cshtml* je vykresleno uvnitř `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="14808-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="14808-147">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="14808-147">ViewData and layout</span></span>

<span data-ttu-id="14808-148">Vezměte v úvahu následující značky ze souboru *Pages/Movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="14808-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="14808-149">Předchozí zvýrazněný kód je příkladem Razor přechodu do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="14808-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="14808-150">`{`Znaky a jsou `}` uzavřeny v bloku kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="14808-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="14808-151">`PageModel`Základní třída obsahuje `ViewData` vlastnost Dictionary, kterou lze použít k předání dat do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="14808-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="14808-152">Objekty jsou přidány do `ViewData` slovníku pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="14808-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="14808-153">V předchozí ukázce `"Title"` je vlastnost přidána do `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="14808-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="14808-154">Tato `"Title"` vlastnost se používá v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="14808-155">Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="14808-156">Řádek `@*Markup removed for brevity.*@` je Razor komentář.</span><span class="sxs-lookup"><span data-stu-id="14808-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="14808-157">Na rozdíl od komentářů jazyka HTML ( `<!-- -->` ) nejsou Razor Komentáře odesílány klientovi.</span><span class="sxs-lookup"><span data-stu-id="14808-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="14808-158">Aktualizovat rozložení</span><span class="sxs-lookup"><span data-stu-id="14808-158">Update the layout</span></span>

<span data-ttu-id="14808-159">Změňte `<title>` prvek v souboru *Pages/Shared/_Layout. cshtml* tak, aby se místo \*\* Razor PagesMovie\*\*zobrazoval **film** .</span><span class="sxs-lookup"><span data-stu-id="14808-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="14808-160">Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="14808-161">Nahraďte předchozí prvek následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14808-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="14808-162">Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="14808-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="14808-163">V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="14808-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="14808-164">`asp-page="/Movies/Index"`Atribut pomocník značek a hodnota vytvoří odkaz na `/Movies/Index` Razor stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="14808-165">`asp-area`Hodnota atributu je prázdná, takže se oblast nepoužívá v odkazu.</span><span class="sxs-lookup"><span data-stu-id="14808-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="14808-166">Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="14808-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="14808-167">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="14808-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="14808-168">Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="14808-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="14808-169">Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**).</span><span class="sxs-lookup"><span data-stu-id="14808-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="14808-170">Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.</span><span class="sxs-lookup"><span data-stu-id="14808-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="14808-171">V poli možná nebudete moct zadat desítkové čárky `Price` .</span><span class="sxs-lookup"><span data-stu-id="14808-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="14808-172">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="14808-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="14808-173">Pokyny k přidání desetinné čárky najdete v tomto [problému v githubu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="14808-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="14808-174">`Layout`Vlastnost je nastavena v souboru *Pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="14808-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="14808-175">Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny Razor soubory ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="14808-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="14808-176">Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="14808-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="14808-177">Model vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="14808-177">The Create page model</span></span>

<span data-ttu-id="14808-178">Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="14808-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="14808-179">`OnGet`Metoda inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="14808-180">Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí.</span><span class="sxs-lookup"><span data-stu-id="14808-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="14808-181">Později v tomto kurzu `OnGet` se zobrazuje příklad stavu inicializace.</span><span class="sxs-lookup"><span data-stu-id="14808-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="14808-182">`Page`Metoda vytvoří `PageResult` objekt, který vykresluje stránku *vytvořit. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="14808-183">`Movie`Vlastnost používá `[BindProperty]` atribut pro přihlášení k [modelu vazby](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="14808-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="14808-184">Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="14808-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="14808-185">`OnPostAsync`Metoda se spustí, když stránka publikuje data formuláře:</span><span class="sxs-lookup"><span data-stu-id="14808-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="14808-186">Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="14808-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="14808-187">Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="14808-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="14808-188">Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="14808-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="14808-189">Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="14808-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="14808-190">Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="14808-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="14808-191">Stránka pro vytvoření Razor</span><span class="sxs-lookup"><span data-stu-id="14808-191">The Create Razor Page</span></span>

<span data-ttu-id="14808-192">Prověřte stránku *stránky/filmy/vytvořit soubor. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="14808-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="14808-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14808-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14808-194">Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="14808-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="14808-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14808-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="14808-197">V předchozím kódu se zobrazují následující pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="14808-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14808-198">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="14808-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="14808-199">Visual Studio zobrazí následující značky v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="14808-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="14808-200">`<form method="post">`Element je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="14808-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="14808-201">Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="14808-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="14808-202">Modul generování uživatelského rozhraní vytvoří Razor značky pro každé pole v modelu (s výjimkou identifikátoru), který je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="14808-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="14808-203">[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` a `<span asp-validation-for` ) zobrazí chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="14808-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="14808-204">Ověřování je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="14808-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="14808-205">[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) vygeneruje popisek a `for` atribut pro `Title` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="14808-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="14808-206">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="14808-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="14808-207">Další informace o Pomocníkech značek `<form method="post">` , jako je, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="14808-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14808-208">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="14808-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="14808-209">[Předchozí: Přidání modelu](xref:tutorials/razor-pages/model) 
>  [Další: databáze](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="14808-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14808-210">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="14808-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="14808-211">V tomto kurzu se podíváme na Razor stránky vytvořené pomocí generování uživatelského rozhraní v [předchozím kurzu](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="14808-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="14808-212">[Zobrazit nebo stáhnout](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) ukázku.</span><span class="sxs-lookup"><span data-stu-id="14808-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="14808-213">Stránky vytvořit, odstranit, podrobnosti a upravit</span><span class="sxs-lookup"><span data-stu-id="14808-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="14808-214">Projděte si model stránky *stránky/filmy/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="14808-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="14808-215">Razor Stránky jsou odvozeny z `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="14808-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="14808-216">Podle konvence `PageModel` je volána třída odvozená od třídy `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="14808-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="14808-217">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k přidání na `RazorPagesMovieContext` stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="14808-218">Všechny vygenerované stránky následují tento model.</span><span class="sxs-lookup"><span data-stu-id="14808-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="14808-219">Další informace o asynchronním programování pomocí Entity Framework naleznete v tématu [asynchronní kód](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="14808-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="14808-220">Když je vytvořena žádost pro stránku, `OnGetAsync` Metoda vrátí seznam filmů na Razor stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="14808-221">`OnGetAsync` nebo `OnGet` je volána na Razor stránce pro inicializaci stavu stránky.</span><span class="sxs-lookup"><span data-stu-id="14808-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="14808-222">V tomto případě `OnGetAsync` získá seznam filmů a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="14808-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="14808-223">Při vrácení nebo vrácení se `OnGet` `void` `OnGetAsync` `Task` nepoužije žádná návratová metoda.</span><span class="sxs-lookup"><span data-stu-id="14808-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="14808-224">Pokud je návratový typ `IActionResult` nebo `Task<IActionResult>` , je nutné poskytnout návratový příkaz.</span><span class="sxs-lookup"><span data-stu-id="14808-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="14808-225">Například metoda *Pages/video/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="14808-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="14808-226">Projděte stránku *stránky/filmy/index. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="14808-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="14808-227">Razor může přejít z formátu HTML do jazyka C# nebo do Razor specifického kódu.</span><span class="sxs-lookup"><span data-stu-id="14808-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="14808-228">`@`Je-li symbol následován [ Razor vyhrazeným klíčovým slovem](xref:mvc/views/razor#razor-reserved-keywords), přechází na Razor konkrétní značku, jinak přejde do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="14808-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="14808-229">`@page` Razor Direktiva vytvoří soubor na akci MVC, což znamená, že může zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="14808-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="14808-230">`@page` musí se jednat o první Razor direktivu na stránce.</span><span class="sxs-lookup"><span data-stu-id="14808-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="14808-231">`@page` je příkladem přechodu na Razor konkrétní značku.</span><span class="sxs-lookup"><span data-stu-id="14808-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="14808-232">Další informace najdete v tématu [ Razor syntaxe](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="14808-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="14808-233">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="14808-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="14808-234">`DisplayNameFor`Pomocník HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu.</span><span class="sxs-lookup"><span data-stu-id="14808-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="14808-235">Výraz lambda je zkontrolován namísto vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="14808-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="14808-236">To znamená, že nedojde k narušení přístupu `model` , pokud `model.Movie` `model.Movie[0]` jsou `null` nebo prázdná.</span><span class="sxs-lookup"><span data-stu-id="14808-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="14808-237">Při vyhodnocování výrazu lambda (například s hodnotou `@Html.DisplayFor(modelItem => item.Title)` ) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="14808-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="14808-238">@modelDirektiva</span><span class="sxs-lookup"><span data-stu-id="14808-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="14808-239">`@model`Direktiva určuje typ modelu předaného na Razor stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="14808-240">V předchozím příkladu `@model` čára zpřístupňuje `PageModel` třídu odvozenou pro Razor stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="14808-241">Model se používá v `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocníkech HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) a na stránce.</span><span class="sxs-lookup"><span data-stu-id="14808-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="14808-242">Stránka rozložení</span><span class="sxs-lookup"><span data-stu-id="14808-242">The layout page</span></span>

<span data-ttu-id="14808-243">Vyberte odkazy nabídky (\*\* Razor PagesMovie\*\*, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="14808-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="14808-244">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="14808-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="14808-245">Rozložení nabídky je implementováno v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="14808-246">Otevřete soubor *stránky/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="14808-247">Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu.</span><span class="sxs-lookup"><span data-stu-id="14808-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="14808-248">Najděte `@RenderBody()` řádek.</span><span class="sxs-lookup"><span data-stu-id="14808-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="14808-249">`RenderBody` je zástupný symbol, kde jsou všechna zobrazení pro konkrétní stránku, která vytvoříte, *zabalena* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="14808-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="14808-250">Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **stránky/soukromí. cshtml** se vykreslí uvnitř `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="14808-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="14808-251">ViewData a rozložení</span><span class="sxs-lookup"><span data-stu-id="14808-251">ViewData and layout</span></span>

<span data-ttu-id="14808-252">Vezměte v úvahu následující kód ze souboru *Pages/Movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="14808-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="14808-253">Předchozí zvýrazněný kód je příkladem Razor přechodu do jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="14808-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="14808-254">`{`Znaky a jsou `}` uzavřeny v bloku kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="14808-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="14808-255">`PageModel`Základní třída má `ViewData` vlastnost Dictionary, kterou lze použít k přidání dat, která chcete předat zobrazení.</span><span class="sxs-lookup"><span data-stu-id="14808-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="14808-256">Do slovníku přidáte objekty `ViewData` pomocí vzoru klíč/hodnota.</span><span class="sxs-lookup"><span data-stu-id="14808-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="14808-257">V předchozím příkladu je do slovníku přidána vlastnost title `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="14808-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="14808-258">Vlastnost title se používá v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="14808-259">Následující kód ukazuje několik prvních řádků souboru *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="14808-260">Řádek `@*Markup removed for brevity.*@` je komentář, Razor který se nezobrazí v souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="14808-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="14808-261">Na rozdíl od komentářů jazyka HTML ( `<!-- -->` ) nejsou Razor Komentáře odesílány klientovi.</span><span class="sxs-lookup"><span data-stu-id="14808-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="14808-262">Aktualizovat rozložení</span><span class="sxs-lookup"><span data-stu-id="14808-262">Update the layout</span></span>

<span data-ttu-id="14808-263">Změňte `<title>` prvek v souboru *Pages/Shared/_Layout. cshtml* tak, aby se místo \*\* Razor PagesMovie\*\*zobrazoval **film** .</span><span class="sxs-lookup"><span data-stu-id="14808-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="14808-264">Vyhledejte následující kotvicí prvek v souboru *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="14808-265">Nahraďte předchozí prvek následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="14808-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="14808-266">Předchozí element ukotvení je [pomocná pomůcka značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="14808-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="14808-267">V tomto případě je to [Pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="14808-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="14808-268">`asp-page="/Movies/Index"`Atribut pomocník značek a hodnota vytvoří odkaz na `/Movies/Index` Razor stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="14808-269">`asp-area`Hodnota atributu je prázdná, takže se oblast nepoužívá v odkazu.</span><span class="sxs-lookup"><span data-stu-id="14808-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="14808-270">Další informace najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="14808-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="14808-271">Uložte změny a otestujte aplikaci kliknutím na odkaz **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="14808-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="14808-272">Pokud máte nějaké problémy, podívejte se na soubor [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="14808-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="14808-273">Otestujte ostatní odkazy (**Domů**, **RpMovie**, **vytvořit**, **Upravit**a **Odstranit**).</span><span class="sxs-lookup"><span data-stu-id="14808-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="14808-274">Každá stránka nastaví nadpis, který vidíte na kartě prohlížeče. Při vytváření záložky stránky se nadpis používá pro záložku.</span><span class="sxs-lookup"><span data-stu-id="14808-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="14808-275">V poli možná nebudete moct zadat desítkové čárky `Price` .</span><span class="sxs-lookup"><span data-stu-id="14808-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="14808-276">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a formáty kalendářních dat, které nejsou v češtině, je nutné provést kroky k globalizaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="14808-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="14808-277">Tento [problém githubu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) najdete v pokynech k přidání desetinné čárky.</span><span class="sxs-lookup"><span data-stu-id="14808-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="14808-278">`Layout`Vlastnost je nastavena v souboru *Pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="14808-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="14808-279">Předchozí kód nastaví soubor rozložení na *stránky/Shared/_Layout. cshtml* pro všechny Razor soubory ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="14808-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="14808-280">Další informace najdete v tématu [rozložení](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="14808-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="14808-281">Model vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="14808-281">The Create page model</span></span>

<span data-ttu-id="14808-282">Projděte si model stránky *stránky/filmy/vytvořit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="14808-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="14808-283">`OnGet`Metoda inicializuje libovolný stav potřebný pro stránku.</span><span class="sxs-lookup"><span data-stu-id="14808-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="14808-284">Stránka pro vytvoření nemá žádný stav k inicializaci, takže `Page` se vrátí.</span><span class="sxs-lookup"><span data-stu-id="14808-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="14808-285">Později v tomto kurzu vidíte `OnGet` Stav inicializace metody.</span><span class="sxs-lookup"><span data-stu-id="14808-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="14808-286">`Page`Metoda vytvoří `PageResult` objekt, který vykresluje stránku *vytvořit. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="14808-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="14808-287">`Movie`Vlastnost používá `[BindProperty]` atribut pro přihlášení k [modelu vazby](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="14808-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="14808-288">Když formulář vytvořit odešle hodnoty formuláře, modul runtime ASP.NET Core váže odeslané hodnoty do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="14808-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="14808-289">`OnPostAsync`Metoda se spustí, když stránka publikuje data formuláře:</span><span class="sxs-lookup"><span data-stu-id="14808-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="14808-290">Pokud dojde k nějakým chybám modelu, formulář se znovu zobrazí spolu se všemi publikovanými daty formuláře.</span><span class="sxs-lookup"><span data-stu-id="14808-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="14808-291">Většinu chyb modelu lze zachytit na straně klienta před odesláním formuláře.</span><span class="sxs-lookup"><span data-stu-id="14808-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="14808-292">Příkladem chyby modelu je publikování hodnoty pro pole data, které nelze převést na datum.</span><span class="sxs-lookup"><span data-stu-id="14808-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="14808-293">Ověřování na straně klienta a ověřování modelu jsou popsány dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="14808-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="14808-294">Pokud nedochází k žádným chybám modelu, data se uloží a prohlížeč se přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="14808-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="14808-295">Stránka pro vytvoření Razor</span><span class="sxs-lookup"><span data-stu-id="14808-295">The Create Razor Page</span></span>

<span data-ttu-id="14808-296">Prověřte stránku *stránky/filmy/vytvořit soubor. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="14808-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="14808-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14808-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14808-298">Visual Studio zobrazí `<form method="post">` značku v zřetelném tučném písmu, které se používá pro pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="14808-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![VS17 zobrazení stránky vytvořit. cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="14808-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14808-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="14808-301">Další informace o Pomocníkech značek `<form method="post">` , jako je, najdete [v tématu označení pomocníků značek v ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="14808-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14808-302">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="14808-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="14808-303">Visual Studio pro Mac zobrazí `<form method="post">` značku v zřetelném tučném písmu, které se používá pro pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="14808-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="14808-304">`<form method="post">`Element je Pomocník pro [značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="14808-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="14808-305">Pomocná rutina značky Form zahrnuje automaticky [tokeny proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="14808-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="14808-306">Modul generování uživatelského rozhraní vytvoří Razor značky pro každé pole v modelu (s výjimkou identifikátoru), který je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="14808-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="14808-307">[Pomocníka značek ověřování](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` a `<span asp-validation-for` ) zobrazí chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="14808-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="14808-308">Ověřování je podrobněji popsáno dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="14808-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="14808-309">[Pomocník značek popisku](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) vygeneruje popisek a `for` atribut pro `Title` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="14808-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="14808-310">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="14808-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14808-311">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="14808-311">Additional resources</span></span>

* [<span data-ttu-id="14808-312">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="14808-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="14808-313">[Předchozí: Přidání modelu](xref:tutorials/razor-pages/model) 
>  [Další: databáze](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="14808-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
