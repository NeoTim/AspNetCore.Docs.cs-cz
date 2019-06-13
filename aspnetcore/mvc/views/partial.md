---
title: Částečná zobrazení v ASP.NET Core
author: ardalis
description: Objevte, jak použít částečné zobrazení k rozdělení souborů velké značek a omezili dvojí společné značky na webových stránkách v aplikacích ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
uid: mvc/views/partial
ms.openlocfilehash: 901fd52f89969141713e443890781a77308bd901
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034913"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="0dee3-103">Částečná zobrazení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0dee3-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="0dee3-104">Podle [Steve Smith](https://ardalis.com/), [Luke Latham](https://github.com/guardrex), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), a [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="0dee3-104">By [Steve Smith](https://ardalis.com/), [Luke Latham](https://github.com/guardrex), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="0dee3-105">Částečné zobrazení je [Razor](xref:mvc/views/razor) souboru označení ( *.cshtml*), který vykreslí výstupu protokolu HTML *v rámci* jiný soubor značek je vykreslen výstup.</span><span class="sxs-lookup"><span data-stu-id="0dee3-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0dee3-106">Termín *částečné zobrazení* se používá při vývoji buď aplikace MVC, ve kterém jsou volány soubory označení *zobrazení*, nebo aplikace stránky Razor, ve kterém jsou volány soubory označení *stránky*.</span><span class="sxs-lookup"><span data-stu-id="0dee3-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="0dee3-107">Toto téma odkazuje obecně na zobrazení MVC a stránek Razor pages jako *soubory označení*.</span><span class="sxs-lookup"><span data-stu-id="0dee3-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="0dee3-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0dee3-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="0dee3-109">Kdy použít částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="0dee3-109">When to use partial views</span></span>

<span data-ttu-id="0dee3-110">Částečná zobrazení jsou účinný způsob, jak:</span><span class="sxs-lookup"><span data-stu-id="0dee3-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="0dee3-111">Rozdělte soubory velkých značek na menší součásti.</span><span class="sxs-lookup"><span data-stu-id="0dee3-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="0dee3-112">Ve velkých složitých značek souborů skládá z několika logických částí, je výhodné pracovat se službou každého jednotlivého samostatný do částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="0dee3-113">Kód v souboru označení je spravovat, protože kód obsahuje pouze celkovou strukturu stránky a odkazy na částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="0dee3-114">Omezili dvojí společný kód obsah značky souborů.</span><span class="sxs-lookup"><span data-stu-id="0dee3-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="0dee3-115">Při použití stejné prvky kódu v souborech značek částečné zobrazení odebere duplicitní značky obsah do jednoho souboru částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="0dee3-116">Při změně kódu v částečné zobrazení aktualizuje vykresleného výstupu souborů značek, které používají částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="0dee3-117">Částečná zobrazení nesmí sloužit k udržování společné prvky rozložení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="0dee3-118">Společné prvky rozložení musí být zadán v [_Layout.cshtml](xref:mvc/views/layout) soubory.</span><span class="sxs-lookup"><span data-stu-id="0dee3-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="0dee3-119">Není použít částečné zobrazení, ve kterém jsou vyžadována k vykreslení kódu vykreslování komplexní logiku nebo spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="0dee3-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="0dee3-120">Namísto částečné zobrazení, použijte [zobrazení komponenty](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="0dee3-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="0dee3-121">Deklarujte částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="0dee3-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0dee3-122">Částečné zobrazení je *.cshtml* souboru označení udržována v rámci *zobrazení* složky (MVC) nebo *stránky* složky (stránky Razor).</span><span class="sxs-lookup"><span data-stu-id="0dee3-122">A partial view is a *.cshtml* markup file maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="0dee3-123">V ASP.NET Core MVC, řadič <xref:Microsoft.AspNetCore.Mvc.ViewResult> je schopen vracet částečné zobrazení nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="0dee3-124">V Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> může vrátit reprezentovaná jako částečné zobrazení <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> objektu.</span><span class="sxs-lookup"><span data-stu-id="0dee3-124">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="0dee3-125">Odkazování na a vykreslení částečných zobrazení je popsána v [odkazovat na částečné zobrazení](#reference-a-partial-view) oddílu.</span><span class="sxs-lookup"><span data-stu-id="0dee3-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="0dee3-126">Na rozdíl od zobrazení MVC nebo vykreslení stránky částečné zobrazení nespustí *soubor _ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0dee3-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="0dee3-127">Další informace o *soubor _ViewStart.cshtml*, naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="0dee3-127">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="0dee3-128">Názvy souborů částečné zobrazení často začínají podtržítkem (`_`).</span><span class="sxs-lookup"><span data-stu-id="0dee3-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="0dee3-129">Tyto zásady vytváření názvů není povinné, ale pomáhá vizuálně odlišili od zobrazení a stránky částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="0dee3-130">Částečné zobrazení je *.cshtml* souboru označení udržována v rámci *zobrazení* složky.</span><span class="sxs-lookup"><span data-stu-id="0dee3-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="0dee3-131">Kontroleru <xref:Microsoft.AspNetCore.Mvc.ViewResult> je schopen vracet částečné zobrazení nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="0dee3-132">Odkazování na a vykreslení částečných zobrazení je popsána v [odkazovat na částečné zobrazení](#reference-a-partial-view) oddílu.</span><span class="sxs-lookup"><span data-stu-id="0dee3-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="0dee3-133">Na rozdíl od MVC vykreslení zobrazení, částečná zobrazení nespustí *soubor _ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0dee3-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="0dee3-134">Další informace o *soubor _ViewStart.cshtml*, naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="0dee3-134">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="0dee3-135">Názvy souborů částečné zobrazení často začínají podtržítkem (`_`).</span><span class="sxs-lookup"><span data-stu-id="0dee3-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="0dee3-136">Tyto zásady vytváření názvů není povinné, ale pomáhá vizuálně odlišili od zobrazení částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="0dee3-137">Odkaz na částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="0dee3-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-razor-pages-pagemodel"></a><span data-ttu-id="0dee3-138">Použít částečné zobrazení v PageModel stránek Razor</span><span class="sxs-lookup"><span data-stu-id="0dee3-138">Use a partial view in a Razor Pages PageModel</span></span>

<span data-ttu-id="0dee3-139">V ASP.NET Core 2.0 nebo 2.1, vykreslí metodu obslužné rutiny  *\_AuthorPartialRP.cshtml* částečného zobrazení do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="0dee3-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0dee3-140">V ASP.NET Core 2.2 nebo vyšší, můžete také volat metodu obslužné rutiny <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> metodu za účelem vytvoření `PartialViewResult` objektu:</span><span class="sxs-lookup"><span data-stu-id="0dee3-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="0dee3-141">V souboru označení použít částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="0dee3-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0dee3-142">V souboru kódu existuje několik způsobů, jak odkazovat na částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="0dee3-143">Doporučujeme vám, že aplikace použijte jednu z následujících dvou přístupů asynchronní vykreslování:</span><span class="sxs-lookup"><span data-stu-id="0dee3-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="0dee3-144">Pomocná rutina částečné značky</span><span class="sxs-lookup"><span data-stu-id="0dee3-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="0dee3-145">Asynchronous HTML Helper</span><span class="sxs-lookup"><span data-stu-id="0dee3-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="0dee3-146">V souboru kódu existují dva způsoby, jak odkazovat na částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="0dee3-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="0dee3-147">Asynchronous HTML Helper</span><span class="sxs-lookup"><span data-stu-id="0dee3-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="0dee3-148">Synchronní pomocné rutiny HTML</span><span class="sxs-lookup"><span data-stu-id="0dee3-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="0dee3-149">Doporučujeme používat aplikace [asynchronní pomocné rutiny HTML](#asynchronous-html-helper).</span><span class="sxs-lookup"><span data-stu-id="0dee3-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="0dee3-150">Pomocná rutina částečné značky</span><span class="sxs-lookup"><span data-stu-id="0dee3-150">Partial Tag Helper</span></span>

<span data-ttu-id="0dee3-151">[Pomocná rutina částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) vyžaduje ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="0dee3-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="0dee3-152">Pomocná rutina částečné značky vykreslí obsah asynchronně a používá HTML syntaxe:</span><span class="sxs-lookup"><span data-stu-id="0dee3-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="0dee3-153">Pokud přípona souboru je k dispozici, odkazuje na pomocné rutiny značky částečné zobrazení, který musí být ve stejné složce jako soubor značek volání částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="0dee3-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="0dee3-154">Následující příklad odkazuje částečné zobrazení z kořenového adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dee3-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="0dee3-155">Cesty, které začínají lomítkem tilda (`~/`) nebo lomítko (`/`) najdete v kořenovém adresáři aplikace:</span><span class="sxs-lookup"><span data-stu-id="0dee3-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="0dee3-156">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="0dee3-156">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="0dee3-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="0dee3-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="0dee3-158">Následující příklad odkazuje na částečné zobrazení pomocí relativní cesty:</span><span class="sxs-lookup"><span data-stu-id="0dee3-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="0dee3-159">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="0dee3-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="0dee3-160">Asynchronous HTML Helper</span><span class="sxs-lookup"><span data-stu-id="0dee3-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="0dee3-161">Při použití pomocné rutiny HTML, nejlepším postupem je použití <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0dee3-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="0dee3-162">`PartialAsync` Vrátí <xref:Microsoft.AspNetCore.Html.IHtmlContent> typ zabalené v <xref:System.Threading.Tasks.Task%601>.</span><span class="sxs-lookup"><span data-stu-id="0dee3-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="0dee3-163">Metoda odkazuje jsou očekávané volání s `@` znak:</span><span class="sxs-lookup"><span data-stu-id="0dee3-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="0dee3-164">Pokud přípona souboru je k dispozici, odkazuje na pomocné rutiny HTML částečné zobrazení, který musí být ve stejné složce jako soubor značek volání částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="0dee3-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="0dee3-165">Následující příklad odkazuje částečné zobrazení z kořenového adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dee3-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="0dee3-166">Cesty, které začínají lomítkem tilda (`~/`) nebo lomítko (`/`) najdete v kořenovém adresáři aplikace:</span><span class="sxs-lookup"><span data-stu-id="0dee3-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0dee3-167">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="0dee3-167">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="0dee3-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="0dee3-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="0dee3-169">Následující příklad odkazuje na částečné zobrazení pomocí relativní cesty:</span><span class="sxs-lookup"><span data-stu-id="0dee3-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="0dee3-170">Alternativně můžete vykreslení částečného zobrazení s <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0dee3-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="0dee3-171">Tato metoda nevrací <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span><span class="sxs-lookup"><span data-stu-id="0dee3-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="0dee3-172">Jejich streamování vykresleného výstupu přímo do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0dee3-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="0dee3-173">Protože metoda nevrací výsledek, musí být volána v rámci bloku kódu Razor:</span><span class="sxs-lookup"><span data-stu-id="0dee3-173">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="0dee3-174">Protože `RenderPartialAsync` datové proudy vykreslený obsah, poskytuje lepší výkon v některých scénářích.</span><span class="sxs-lookup"><span data-stu-id="0dee3-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="0dee3-175">V situacích, kritickém pro výkon srovnávací testy na stránce pomocí obou metod a použít přístup, který generuje rychlejší odezvu.</span><span class="sxs-lookup"><span data-stu-id="0dee3-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="0dee3-176">Synchronní pomocné rutiny HTML</span><span class="sxs-lookup"><span data-stu-id="0dee3-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="0dee3-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> a <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> jsou synchronní ekvivalenty `PartialAsync` a `RenderPartialAsync`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="0dee3-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="0dee3-178">Synchronní ekvivalenty se nedoporučuje, protože existují scénáře, ve kterých zablokování.</span><span class="sxs-lookup"><span data-stu-id="0dee3-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="0dee3-179">Synchronní metody jsou určené pro odstranění v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="0dee3-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0dee3-180">Pokud potřebujete spustit kód, použijte [zobrazení komponenty](xref:mvc/views/view-components) místo částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0dee3-181">Volání `Partial` nebo `RenderPartial` výsledky v analyzátor upozornění aplikace Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0dee3-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="0dee3-182">Například přítomnost `Partial` provede následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="0dee3-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="0dee3-183">Použití IHtmlHelper.Partial může způsobit zablokování aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dee3-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="0dee3-184">Zvažte použití &lt;částečné&gt; pomocné rutiny značky nebo IHtmlHelper.PartialAsync.</span><span class="sxs-lookup"><span data-stu-id="0dee3-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="0dee3-185">Nahraďte volání `@Html.Partial` s `@await Html.PartialAsync` nebo [pomocná rutina částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0dee3-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="0dee3-186">Další informace o migraci pomocná rutina částečné značky najdete v tématu [migrace ze pomocné rutiny HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span><span class="sxs-lookup"><span data-stu-id="0dee3-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="0dee3-187">Částečné zobrazení zjišťování</span><span class="sxs-lookup"><span data-stu-id="0dee3-187">Partial view discovery</span></span>

<span data-ttu-id="0dee3-188">Když částečné zobrazení se odkazuje jeho názvem bez přípony souboru, následující umístění budou prohledána v uvedené pořadí:</span><span class="sxs-lookup"><span data-stu-id="0dee3-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0dee3-189">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="0dee3-189">**Razor Pages**</span></span>

1. <span data-ttu-id="0dee3-190">Právě probíhá na stránce složky</span><span class="sxs-lookup"><span data-stu-id="0dee3-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="0dee3-191">Directory graphu nahoře na stránce složky</span><span class="sxs-lookup"><span data-stu-id="0dee3-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="0dee3-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="0dee3-192">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="0dee3-193">Částečné zobrazení zjišťování platí následující zásady:</span><span class="sxs-lookup"><span data-stu-id="0dee3-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="0dee3-194">Částečná zobrazení jinou se stejným názvem souboru jsou povoleny v případě, že částečná zobrazení jsou v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="0dee3-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="0dee3-195">Při odkazování na částečné zobrazení podle názvu bez přípony souboru a částečné zobrazení je k dispozici ve složce obou volajícího a *Shared* složky, částečné zobrazení ve složce volajícího, jež poskytuje částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="0dee3-196">Pokud není k dispozici ve složce volajícího částečného zobrazení, částečného zobrazení je k dispozici z *Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="0dee3-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="0dee3-197">Částečné zobrazení v *Shared* složky se nazývají *sdílené částečná zobrazení* nebo *výchozí částečná zobrazení*.</span><span class="sxs-lookup"><span data-stu-id="0dee3-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="0dee3-198">Částečná zobrazení mohou být *zřetězené*&mdash;částečné zobrazení můžete volat jiné částečné zobrazení, pokud cyklický odkaz není vytvořený ve volání.</span><span class="sxs-lookup"><span data-stu-id="0dee3-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="0dee3-199">Relativní cesty jsou vždy relativní vzhledem k aktuální soubor nebyl pro kořenový server WSUS nebo nadřazený soubor.</span><span class="sxs-lookup"><span data-stu-id="0dee3-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="0dee3-200">A [Razor](xref:mvc/views/razor) `section` definované v částečné zobrazení je skryt nadřazené značky souborů.</span><span class="sxs-lookup"><span data-stu-id="0dee3-200">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="0dee3-201">`section` Bude viditelná jenom pro částečné zobrazení, ve kterém je definována.</span><span class="sxs-lookup"><span data-stu-id="0dee3-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="0dee3-202">Přístup k datům z částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="0dee3-202">Access data from partial views</span></span>

<span data-ttu-id="0dee3-203">Při vytváření instance částečného zobrazení přijme *kopírování* z nadřazeného objektu `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="0dee3-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="0dee3-204">Aktualizace provedené v datech v rámci částečného zobrazení se ukládají do nadřazeného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="0dee3-205">`ViewData` provedené změny v částečné zobrazení při vrátí částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="0dee3-206">Následující příklad ukazuje, jak předat instanci [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="0dee3-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="0dee3-207">Model lze předat do částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="0dee3-208">Vlastní objekt může být tento model.</span><span class="sxs-lookup"><span data-stu-id="0dee3-208">The model can be a custom object.</span></span> <span data-ttu-id="0dee3-209">Můžete předat model s `PartialAsync` (vykreslí blok volajícímu) nebo `RenderPartialAsync` (streamování obsahu do výstupu):</span><span class="sxs-lookup"><span data-stu-id="0dee3-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0dee3-210">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="0dee3-210">**Razor Pages**</span></span>

<span data-ttu-id="0dee3-211">Následující kód v ukázkové aplikaci je z *Pages/ArticlesRP/ReadRP.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="0dee3-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="0dee3-212">Tato stránka obsahuje dvě částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-212">The page contains two partial views.</span></span> <span data-ttu-id="0dee3-213">Druhý částečné zobrazení předává do modelu a `ViewData` částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="0dee3-214">`ViewDataDictionary` Přetížení konstruktoru slouží k předání nového `ViewData` slovníku při zachování stávajících `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="0dee3-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="0dee3-215">*Pages/Shared/_AuthorPartialRP.cshtml* je první částečné zobrazení odkazuje *ReadRP.cshtml* souboru označení:</span><span class="sxs-lookup"><span data-stu-id="0dee3-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="0dee3-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* je druhý částečné zobrazení odkazuje *ReadRP.cshtml* souboru označení:</span><span class="sxs-lookup"><span data-stu-id="0dee3-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="0dee3-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="0dee3-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="0dee3-218">Následující kód v ukazuje ukázkové aplikace *Views/Articles/Read.cshtml* zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="0dee3-219">Zobrazení obsahuje dvě částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-219">The view contains two partial views.</span></span> <span data-ttu-id="0dee3-220">Druhý částečné zobrazení předává do modelu a `ViewData` částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0dee3-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="0dee3-221">`ViewDataDictionary` Přetížení konstruktoru slouží k předání nového `ViewData` slovníku při zachování stávajících `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="0dee3-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="0dee3-222">*Views/Shared/_AuthorPartial.cshtml* je první částečné zobrazení odkazuje *ReadRP.cshtml* souboru označení:</span><span class="sxs-lookup"><span data-stu-id="0dee3-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="0dee3-223">*Views/Articles/_ArticleSection.cshtml* je druhý částečné zobrazení odkazuje *Read.cshtml* souboru označení:</span><span class="sxs-lookup"><span data-stu-id="0dee3-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="0dee3-224">Za běhu, jsou vykreslovány částečné do souboru kódu nadřazené vykresleného výstupu, který je vykreslen v rámci sdíleného *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0dee3-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="0dee3-225">První částečné zobrazení vykreslí autoři článku název a datum publikování:</span><span class="sxs-lookup"><span data-stu-id="0dee3-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="0dee3-226">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="0dee3-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="0dee3-227">Toto částečné zobrazení z &lt;cesta k souboru sdílené částečné zobrazení&gt;.</span><span class="sxs-lookup"><span data-stu-id="0dee3-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="0dee3-228">19/11/1863 12:00:00 AM</span><span class="sxs-lookup"><span data-stu-id="0dee3-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="0dee3-229">Druhý částečné zobrazení vykreslí části článku:</span><span class="sxs-lookup"><span data-stu-id="0dee3-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="0dee3-230">Jeden Index části: 0</span><span class="sxs-lookup"><span data-stu-id="0dee3-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="0dee3-231">Čtyři skóre a sedm let před...</span><span class="sxs-lookup"><span data-stu-id="0dee3-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="0dee3-232">Index oddílu dvě: 1</span><span class="sxs-lookup"><span data-stu-id="0dee3-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="0dee3-233">Nyní jsme se zabývají skvělé občanské války, testování...</span><span class="sxs-lookup"><span data-stu-id="0dee3-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="0dee3-234">Index oddílu tři: 2</span><span class="sxs-lookup"><span data-stu-id="0dee3-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="0dee3-235">Ale ve větší smysl, jsme nelze vyhradit...</span><span class="sxs-lookup"><span data-stu-id="0dee3-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0dee3-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0dee3-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
