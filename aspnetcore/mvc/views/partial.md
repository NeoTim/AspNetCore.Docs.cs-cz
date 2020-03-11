---
title: Částečná zobrazení v ASP.NET Core
author: ardalis
description: Zjistíte, jak použít částečná zobrazení k rozdělení velkých souborů s označením a jak omezit duplicity běžných značek mezi webovými stránkami v ASP.NET Corech aplikacích.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
uid: mvc/views/partial
ms.openlocfilehash: 04b6d6e620f34ac7154728b1b3048195e87c5860
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663049"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="2fe3a-103">Částečná zobrazení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fe3a-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="2fe3a-104">[Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="2fe3a-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="2fe3a-105">Částečné zobrazení je soubor značek [Razor](xref:mvc/views/razor) ( *. cshtml*), který vykresluje výstup HTML *v rámci* jiného vykresleného výstupu souboru s označením.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2fe3a-106">Termín *částečné zobrazení* se používá při vývoji aplikace MVC, kde jsou soubory značek označovány jako *zobrazení*nebo Razor Pages aplikace, kde se soubory označení nazývají *stránky*.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="2fe3a-107">Toto téma obecně odkazuje na zobrazení MVC a stránky Razor Pages jako *soubory značek*.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="2fe3a-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fe3a-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="2fe3a-109">Kdy použít částečná zobrazení</span><span class="sxs-lookup"><span data-stu-id="2fe3a-109">When to use partial views</span></span>

<span data-ttu-id="2fe3a-110">Částečná zobrazení představují efektivní způsob:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="2fe3a-111">Rozdělte velké soubory s označením na menší součásti.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="2fe3a-112">Ve velkém komplexním souboru označení složeném z několika logických částí je výhodná práce s každou částí izolovanou do částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="2fe3a-113">Kód v souboru označení lze spravovat, protože značky obsahují pouze celkovou strukturu stránky a odkazy na částečná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="2fe3a-114">Snižte duplicitu společného obsahu značek napříč soubory značek.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="2fe3a-115">Když se stejné prvky značek používají napříč soubory značek, částečné zobrazení odstraní duplikaci obsahu kódu do jednoho souboru částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="2fe3a-116">Při změně kódu v částečném zobrazení aktualizuje Vykreslený výstup souborů označení, které používají částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="2fe3a-117">Částečná zobrazení by se neměla používat k údržbě běžných prvků rozložení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="2fe3a-118">V souboru [_Layout. cshtml](xref:mvc/views/layout) by měly být zadány běžné prvky rozložení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="2fe3a-119">Nepoužívejte částečné zobrazení, kde je pro vykreslení značky nutná složitá logika vykreslování nebo provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="2fe3a-120">Místo částečného zobrazení použijte [zobrazení komponenty](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="2fe3a-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="2fe3a-121">Deklarovat částečná zobrazení</span><span class="sxs-lookup"><span data-stu-id="2fe3a-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="2fe3a-122">Částečné zobrazení je soubor s označením *. cshtml* udržovaný ve složce *zobrazení* (MVC) nebo ve složce *stránek* (Razor Pages).</span><span class="sxs-lookup"><span data-stu-id="2fe3a-122">A partial view is a *.cshtml* markup file maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="2fe3a-123">V ASP.NET Core MVC je <xref:Microsoft.AspNetCore.Mvc.ViewResult> kontroleru schopný vracet buď zobrazení, nebo částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="2fe3a-124">V Razor Pages může <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> vracet částečné zobrazení reprezentované jako objekt <xref:Microsoft.AspNetCore.Mvc.PartialViewResult>.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-124">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="2fe3a-125">Odkazování na částečné zobrazení a jejich vykreslování je popsané v části [Reference k částečnému zobrazení](#reference-a-partial-view) .</span><span class="sxs-lookup"><span data-stu-id="2fe3a-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="2fe3a-126">Na rozdíl od zobrazení MVC nebo vykreslování stránky neběží částečné zobrazení *_ViewStart. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="2fe3a-127">Další informace o *_ViewStart. cshtml*naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-127">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="2fe3a-128">Názvy souborů částečného zobrazení často začínají podtržítkem (`_`).</span><span class="sxs-lookup"><span data-stu-id="2fe3a-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="2fe3a-129">Tato konvence pojmenování není povinná, ale pomáhá vizuálně odlišit částečná zobrazení ze zobrazení a stránek.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="2fe3a-130">Částečné zobrazení je soubor označení *. cshtml* spravovaný ve složce *views* .</span><span class="sxs-lookup"><span data-stu-id="2fe3a-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="2fe3a-131"><xref:Microsoft.AspNetCore.Mvc.ViewResult> kontroleru může vracet buď zobrazení, nebo částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="2fe3a-132">Odkazování na částečné zobrazení a jejich vykreslování je popsané v části [Reference k částečnému zobrazení](#reference-a-partial-view) .</span><span class="sxs-lookup"><span data-stu-id="2fe3a-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="2fe3a-133">Na rozdíl od vykreslování zobrazení MVC neběží částečné zobrazení *_ViewStart. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="2fe3a-134">Další informace o *_ViewStart. cshtml*naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-134">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="2fe3a-135">Názvy souborů částečného zobrazení často začínají podtržítkem (`_`).</span><span class="sxs-lookup"><span data-stu-id="2fe3a-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="2fe3a-136">Tato konvence pojmenování není povinná, ale pomáhá vizuálně odlišit částečná zobrazení ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="2fe3a-137">Odkaz na částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="2fe3a-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-razor-pages-pagemodel"></a><span data-ttu-id="2fe3a-138">Použití částečného zobrazení v Razor Pages PageModel</span><span class="sxs-lookup"><span data-stu-id="2fe3a-138">Use a partial view in a Razor Pages PageModel</span></span>

<span data-ttu-id="2fe3a-139">V ASP.NET Core 2,0 nebo 2,1 následující metoda obslužné rutiny vykresluje částečné zobrazení *\_AuthorPartialRP. cshtml* na odpověď:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

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

<span data-ttu-id="2fe3a-140">V ASP.NET Core 2,2 nebo novější, metoda obslužné rutiny může alternativně volat metodu <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> a vytvořit `PartialViewResult` objekt:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="2fe3a-141">Použití částečného zobrazení v souboru označení</span><span class="sxs-lookup"><span data-stu-id="2fe3a-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2fe3a-142">V souboru označení existuje několik způsobů, jak odkazovat na částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="2fe3a-143">Doporučujeme, aby aplikace používaly jeden z následujících přístupů k asynchronnímu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="2fe3a-144">Pomocná rutina částečné značky</span><span class="sxs-lookup"><span data-stu-id="2fe3a-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="2fe3a-145">Asynchronní pomocník HTML</span><span class="sxs-lookup"><span data-stu-id="2fe3a-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="2fe3a-146">V souboru označení existují dva způsoby, jak odkazovat na částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="2fe3a-147">Asynchronní pomocník HTML</span><span class="sxs-lookup"><span data-stu-id="2fe3a-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="2fe3a-148">Synchronní pomocník HTML</span><span class="sxs-lookup"><span data-stu-id="2fe3a-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="2fe3a-149">Doporučujeme, aby aplikace používaly [asynchronní NÁPOVĚDU HTML](#asynchronous-html-helper).</span><span class="sxs-lookup"><span data-stu-id="2fe3a-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="2fe3a-150">Pomocník s částečnou značkou</span><span class="sxs-lookup"><span data-stu-id="2fe3a-150">Partial Tag Helper</span></span>

<span data-ttu-id="2fe3a-151">[Pomocník pro částečnou značku](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) vyžaduje ASP.NET Core 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="2fe3a-152">Pomocník pro částečnou značku vykresluje obsah asynchronně a používá syntaxi podobnou HTML:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="2fe3a-153">Pokud je k dispozici Přípona souboru, pomocník značek odkazuje na částečné zobrazení, které musí být ve stejné složce jako soubor označení, který volá částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="2fe3a-154">Následující příklad odkazuje na částečné zobrazení z kořene aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="2fe3a-155">Cesty, které začínají znakem tildy (`~/`) nebo lomítkem (`/`), odkazují na kořen aplikace:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="2fe3a-156">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="2fe3a-156">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="2fe3a-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="2fe3a-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="2fe3a-158">Následující příklad odkazuje na částečné zobrazení s relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="2fe3a-159">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="2fe3a-160">Asynchronní pomocník HTML</span><span class="sxs-lookup"><span data-stu-id="2fe3a-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="2fe3a-161">Při použití pomocníka jazyka HTML je osvědčeným postupem použití <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="2fe3a-162">`PartialAsync` vrátí typ <xref:Microsoft.AspNetCore.Html.IHtmlContent> zabalený do <xref:System.Threading.Tasks.Task%601>.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="2fe3a-163">Na metodu se odkazuje pomocí předpony očekávaného volání s `@`m znakem:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="2fe3a-164">Pokud je k dispozici Přípona souboru, pomocník HTML odkazuje na částečné zobrazení, které musí být ve stejné složce jako soubor označení, který volá částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="2fe3a-165">Následující příklad odkazuje na částečné zobrazení z kořene aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="2fe3a-166">Cesty, které začínají znakem tildy (`~/`) nebo lomítkem (`/`), odkazují na kořen aplikace:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2fe3a-167">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="2fe3a-167">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="2fe3a-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="2fe3a-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="2fe3a-169">Následující příklad odkazuje na částečné zobrazení s relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="2fe3a-170">Alternativně můžete vykreslit částečné zobrazení pomocí <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="2fe3a-171">Tato metoda nevrací <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="2fe3a-172">Streamuje Vykreslený výstup přímo k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="2fe3a-173">Vzhledem k tomu, že metoda nevrací výsledek, musí být volána v rámci bloku kódu Razor:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-173">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="2fe3a-174">Vzhledem k tomu, že `RenderPartialAsync` streamy vykreslený obsah, poskytuje v některých scénářích lepší výkon.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="2fe3a-175">V situacích, které kritické pro výkon, srovnávací testy stránky pomocí obou přístupů a používejte přístup, který generuje rychlejší odpověď.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="2fe3a-176">Synchronní pomocník HTML</span><span class="sxs-lookup"><span data-stu-id="2fe3a-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="2fe3a-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> a <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> jsou synchronní ekvivalent `PartialAsync` a `RenderPartialAsync`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="2fe3a-178">Synchronní ekvivalenty se nedoporučují, protože existují scénáře, ve kterých se zablokují.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="2fe3a-179">Synchronní metody jsou zaměřené na odebrání v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2fe3a-180">Pokud potřebujete spustit kód, použijte místo částečného zobrazení [komponentu zobrazení](xref:mvc/views/view-components) .</span><span class="sxs-lookup"><span data-stu-id="2fe3a-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2fe3a-181">Volání `Partial` nebo `RenderPartial` vede k upozornění analyzátoru sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="2fe3a-182">Například přítomnost `Partial` vrací následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="2fe3a-183">Použití IHtmlHelper. Partial může způsobit zablokování aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="2fe3a-184">Zvažte použití rutiny &lt;částečné&gt; značky nebo IHtmlHelper. PartialAsync.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="2fe3a-185">Nahraďte volání `@Html.Partial` pomocí `@await Html.PartialAsync` nebo [Pomocníka s částečnou značkou](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2fe3a-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="2fe3a-186">Další informace o migraci pomocníka částečné značky najdete v tématu [migrace z pomocníka jazyka HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span><span class="sxs-lookup"><span data-stu-id="2fe3a-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="2fe3a-187">Zjišťování částečného zobrazení</span><span class="sxs-lookup"><span data-stu-id="2fe3a-187">Partial view discovery</span></span>

<span data-ttu-id="2fe3a-188">Pokud se na částečné zobrazení odkazuje pomocí názvu bez přípony souboru, prohledávají se v uvedeném pořadí následující umístění:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2fe3a-189">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="2fe3a-189">**Razor Pages**</span></span>

1. <span data-ttu-id="2fe3a-190">Aktuálně prováděná složka stránky</span><span class="sxs-lookup"><span data-stu-id="2fe3a-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="2fe3a-191">Adresář grafu nad složkou stránky</span><span class="sxs-lookup"><span data-stu-id="2fe3a-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="2fe3a-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="2fe3a-192">**MVC**</span></span>

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

<span data-ttu-id="2fe3a-193">Následující konvence se vztahují na zjišťování částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="2fe3a-194">Různá částečná zobrazení se stejným názvem souboru jsou povolena, pokud jsou částečná zobrazení v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="2fe3a-195">Při odkazování na částečné zobrazení podle názvu bez přípony souboru a částečného zobrazení se nachází ve složce volajícího i ve *sdílené* složce, částečné zobrazení ve složce volajícího poskytuje částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="2fe3a-196">Pokud se částečné zobrazení nenachází ve složce volajícího, částečné zobrazení se poskytuje ze *sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="2fe3a-197">Částečná zobrazení ve *sdílené* složce se nazývají *sdílená částečná zobrazení* nebo *výchozí částečná zobrazení*.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="2fe3a-198">Částečná zobrazení lze *řetězit*&mdash;částečné zobrazení může volat jiné částečné zobrazení, pokud cyklický odkaz není vytvořen voláními.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="2fe3a-199">Relativní cesty jsou vždycky relativní vzhledem k aktuálnímu souboru, ne ke kořenu nebo nadřazenému souboru.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="2fe3a-200">`section` [Razor](xref:mvc/views/razor) definovaná v částečném zobrazení je neviditelná pro nadřazené soubory značek.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-200">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="2fe3a-201">`section` je viditelné pouze pro částečné zobrazení, ve kterém je definováno.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="2fe3a-202">Přístup k datům z částečných zobrazení</span><span class="sxs-lookup"><span data-stu-id="2fe3a-202">Access data from partial views</span></span>

<span data-ttu-id="2fe3a-203">Když je vytvořena instance částečného zobrazení, obdrží *kopii* `ViewData` slovníku nadřazených objektů.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="2fe3a-204">Aktualizace dat v rámci částečného zobrazení nejsou trvale uložené v nadřazeném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="2fe3a-205">Při vrácení částečného zobrazení dojde ke ztrátě `ViewData`ch změn v částečném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="2fe3a-206">Následující příklad ukazuje, jak předat instanci [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) do částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="2fe3a-207">Model můžete předat do částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="2fe3a-208">Modelem může být vlastní objekt.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-208">The model can be a custom object.</span></span> <span data-ttu-id="2fe3a-209">Model můžete předat `PartialAsync` (vykreslí blok obsahu volajícímu) nebo `RenderPartialAsync` (streamuje obsah do výstupu):</span><span class="sxs-lookup"><span data-stu-id="2fe3a-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2fe3a-210">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="2fe3a-210">**Razor Pages**</span></span>

<span data-ttu-id="2fe3a-211">Následující značky v ukázkové aplikaci jsou ze stránky *stránky/ArticlesRP/ReadRP. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2fe3a-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="2fe3a-212">Stránka obsahuje dvě částečná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-212">The page contains two partial views.</span></span> <span data-ttu-id="2fe3a-213">Druhé částečné zobrazení projde model a `ViewData` částečnému zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="2fe3a-214">Přetížení konstruktoru `ViewDataDictionary` slouží k předání nového slovníku `ViewData` při zachování existujícího slovníku `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="2fe3a-215">*Pages/Shared/_AuthorPartialRP. cshtml* je první částečné zobrazení, na které odkazuje soubor s označením *ReadRP. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2fe3a-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="2fe3a-216">*Pages/ArticlesRP/_ArticleSectionRP. cshtml* je druhé částečné zobrazení, na které odkazuje soubor s označením *ReadRP. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2fe3a-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="2fe3a-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="2fe3a-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="2fe3a-218">Následující kód v ukázkové aplikaci zobrazuje zobrazení */články/čtení. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2fe3a-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="2fe3a-219">Zobrazení obsahuje dvě částečná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-219">The view contains two partial views.</span></span> <span data-ttu-id="2fe3a-220">Druhé částečné zobrazení projde model a `ViewData` částečnému zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="2fe3a-221">Přetížení konstruktoru `ViewDataDictionary` slouží k předání nového slovníku `ViewData` při zachování existujícího slovníku `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="2fe3a-222">Views */Shared/_AuthorPartial. cshtml* je první částečné zobrazení, na které odkazuje soubor s označením *Read. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2fe3a-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="2fe3a-223">Views */articles/_ArticleSection. cshtml* je druhé částečné zobrazení, na které odkazuje soubor s označením *Read. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2fe3a-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="2fe3a-224">V době běhu jsou fragmenty vykresleny do vykresleného výstupu nadřazeného souboru označení, který je vykreslen v rámci sdíleného *_Layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="2fe3a-225">První částečné zobrazení vykreslí jméno autora článku a datum publikování:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="2fe3a-226">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="2fe3a-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="2fe3a-227">Toto částečné zobrazení z &lt;sdílené cesty k souboru částečného zobrazení&gt;.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="2fe3a-228">11/19/1863 12:00:00 DOP.</span><span class="sxs-lookup"><span data-stu-id="2fe3a-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="2fe3a-229">Druhé částečné zobrazení vykreslí oddíly článku:</span><span class="sxs-lookup"><span data-stu-id="2fe3a-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="2fe3a-230">Jeden index oddílu: 0</span><span class="sxs-lookup"><span data-stu-id="2fe3a-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="2fe3a-231">Před čtyřmi a sedmi lety...</span><span class="sxs-lookup"><span data-stu-id="2fe3a-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="2fe3a-232">Oddíl dva rejstřík: 1</span><span class="sxs-lookup"><span data-stu-id="2fe3a-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="2fe3a-233">Teď jsme se zabývají skvělým občanském válečným testováním...</span><span class="sxs-lookup"><span data-stu-id="2fe3a-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="2fe3a-234">Oddíl tři rejstřík: 2</span><span class="sxs-lookup"><span data-stu-id="2fe3a-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="2fe3a-235">Ale ve větším smyslu nemůžeme vyhradit...</span><span class="sxs-lookup"><span data-stu-id="2fe3a-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fe3a-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2fe3a-236">Additional resources</span></span>

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
