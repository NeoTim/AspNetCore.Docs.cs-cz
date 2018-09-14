---
title: Částečná zobrazení v ASP.NET Core
author: ardalis
description: Objevte, jak použít částečné zobrazení k rozdělení souborů velké značek a omezili dvojí společné značky na webových stránkách v aplikacích ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/11/2018
uid: mvc/views/partial
ms.openlocfilehash: a836ed073dfe769fc3cc0cd0622b17937747928b
ms.sourcegitcommit: 70fb7c9d5f2ddfcf4747382a9f7159feca7a6aa7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45601753"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="6db50-103">Částečná zobrazení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6db50-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="6db50-104">Podle [Steve Smith](https://ardalis.com/), [Luke Latham](https://github.com/guardrex), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), a [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="6db50-104">By [Steve Smith](https://ardalis.com/), [Luke Latham](https://github.com/guardrex), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="6db50-105">Částečné zobrazení je [Razor](xref:mvc/views/razor) souboru označení (*.cshtml*), který vykreslí výstupu protokolu HTML *v rámci* jiný soubor značek je vykreslen výstup.</span><span class="sxs-lookup"><span data-stu-id="6db50-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6db50-106">Termín *částečné zobrazení* se používá při vývoji buď aplikace MVC, ve kterém jsou volány soubory označení *zobrazení*, nebo aplikace stránky Razor, ve kterém jsou volány soubory označení *stránky*.</span><span class="sxs-lookup"><span data-stu-id="6db50-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="6db50-107">Toto téma odkazuje obecně na zobrazení MVC a stránek Razor pages jako *soubory označení*.</span><span class="sxs-lookup"><span data-stu-id="6db50-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="6db50-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([stažení](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6db50-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="6db50-109">Kdy použít částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="6db50-109">When to use partial views</span></span>

<span data-ttu-id="6db50-110">Částečná zobrazení jsou účinný způsob, jak:</span><span class="sxs-lookup"><span data-stu-id="6db50-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="6db50-111">Rozdělte soubory velkých značek na menší součásti.</span><span class="sxs-lookup"><span data-stu-id="6db50-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="6db50-112">Ve velkých složitých značek souborů skládá z několika logických částí, je výhodné pracovat se službou každého jednotlivého samostatný do částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="6db50-113">Kód v souboru označení je spravovat, protože kód obsahuje pouze celkovou strukturu stránky a odkazy na částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="6db50-114">Omezili dvojí společný kód obsah značky souborů.</span><span class="sxs-lookup"><span data-stu-id="6db50-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="6db50-115">Při použití stejné prvky kódu v souborech značek částečné zobrazení odebere duplicitní značky obsah do jednoho souboru částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="6db50-116">Při změně kódu v částečné zobrazení aktualizuje vykresleného výstupu souborů značek, které používají částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="6db50-117">Částečná zobrazení nesmí sloužit k udržování společné prvky rozložení.</span><span class="sxs-lookup"><span data-stu-id="6db50-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="6db50-118">Společné prvky rozložení musí být zadán v [_Layout.cshtml](xref:mvc/views/layout) soubory.</span><span class="sxs-lookup"><span data-stu-id="6db50-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="6db50-119">Není použít částečné zobrazení, ve kterém jsou vyžadována k vykreslení kódu vykreslování komplexní logiku nebo spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="6db50-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="6db50-120">Namísto částečné zobrazení, použijte [zobrazení komponenty](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="6db50-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="6db50-121">Deklarujte částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="6db50-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6db50-122">Částečné zobrazení je *.cshtml* souboru označení udržována v rámci *zobrazení* složky (MVC) nebo *stránky* složky (stránky Razor).</span><span class="sxs-lookup"><span data-stu-id="6db50-122">A partial view is a *.cshtml* markup file maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="6db50-123">V ASP.NET Core MVC, řadič <xref:Microsoft.AspNetCore.Mvc.ViewResult> je schopen vracet částečné zobrazení nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="6db50-124">Obdobné funkce plánujeme přidat v 2.2 technologie ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6db50-124">An analogous capability is planned for Razor Pages in ASP.NET Core 2.2.</span></span> <span data-ttu-id="6db50-125">V Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> můžete vrátit <xref:Microsoft.AspNetCore.Mvc.PartialViewResult>.</span><span class="sxs-lookup"><span data-stu-id="6db50-125">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult>.</span></span> <span data-ttu-id="6db50-126">Odkazování na a vykreslení částečných zobrazení je popsána v [odkazovat na částečné zobrazení](#reference-a-partial-view) oddílu.</span><span class="sxs-lookup"><span data-stu-id="6db50-126">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="6db50-127">Na rozdíl od zobrazení MVC nebo vykreslení stránky částečné zobrazení nespustí *soubor _ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6db50-127">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="6db50-128">Další informace o *soubor _ViewStart.cshtml*, naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="6db50-128">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="6db50-129">Názvy souborů částečné zobrazení často začínají podtržítkem (`_`).</span><span class="sxs-lookup"><span data-stu-id="6db50-129">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="6db50-130">Tyto zásady vytváření názvů není povinné, ale pomáhá vizuálně odlišili od zobrazení a stránky částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-130">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span> <span data-ttu-id="6db50-131">Pokud název souboru začíná podtržítkem, stránky Razor nezpracovává souboru označení jako stránky Razor Pages, i v případě, že soubor kódu obsahuje `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="6db50-131">When the file name starts with an underscore, Razor Pages doesn't process the markup file as a Razor Pages page, even when the file's markup includes the `@page` directive.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="6db50-132">Částečné zobrazení je *.cshtml* souboru označení udržována v rámci *zobrazení* složky.</span><span class="sxs-lookup"><span data-stu-id="6db50-132">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="6db50-133">Kontroleru <xref:Microsoft.AspNetCore.Mvc.ViewResult> je schopen vracet částečné zobrazení nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-133">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span>

<span data-ttu-id="6db50-134">Na rozdíl od MVC vykreslení zobrazení, částečná zobrazení nespustí *soubor _ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6db50-134">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="6db50-135">Další informace o *soubor _ViewStart.cshtml*, naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="6db50-135">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="6db50-136">Názvy souborů částečné zobrazení často začínají podtržítkem (`_`).</span><span class="sxs-lookup"><span data-stu-id="6db50-136">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="6db50-137">Tyto zásady vytváření názvů není povinné, ale pomáhá vizuálně odlišili od zobrazení částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-137">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="6db50-138">Odkaz na částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="6db50-138">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6db50-139">V souboru kódu existuje několik způsobů, jak odkazovat na částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-139">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="6db50-140">Doporučujeme vám, že aplikace použijte jednu z následujících dvou přístupů asynchronní vykreslování:</span><span class="sxs-lookup"><span data-stu-id="6db50-140">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="6db50-141">Pomocná rutina částečné značky</span><span class="sxs-lookup"><span data-stu-id="6db50-141">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="6db50-142">Asynchronní pomocné rutiny HTML</span><span class="sxs-lookup"><span data-stu-id="6db50-142">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="6db50-143">V souboru kódu existují dva způsoby, jak odkazovat na částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="6db50-143">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="6db50-144">Asynchronní pomocné rutiny HTML</span><span class="sxs-lookup"><span data-stu-id="6db50-144">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="6db50-145">Synchronní pomocné rutiny HTML</span><span class="sxs-lookup"><span data-stu-id="6db50-145">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="6db50-146">Doporučujeme používat aplikace [asynchronní pomocné rutiny HTML](#asynchronous-html-helper).</span><span class="sxs-lookup"><span data-stu-id="6db50-146">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="6db50-147">Pomocná rutina částečné značky</span><span class="sxs-lookup"><span data-stu-id="6db50-147">Partial Tag Helper</span></span>

<span data-ttu-id="6db50-148">[Pomocná rutina částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) vyžaduje ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="6db50-148">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="6db50-149">Pomocná rutina částečné značky vykreslí obsah asynchronně a používá HTML syntaxe:</span><span class="sxs-lookup"><span data-stu-id="6db50-149">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="6db50-150">Pokud přípona souboru je k dispozici, odkazuje na pomocné rutiny značky částečné zobrazení, který musí být ve stejné složce jako soubor značek volání částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="6db50-150">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="6db50-151">Následující příklad odkazuje částečné zobrazení z kořenového adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="6db50-151">The following example references a partial view from the app root.</span></span> <span data-ttu-id="6db50-152">Cesty, které začínají lomítkem tilda (`~/`) nebo lomítko (`/`) najdete v kořenovém adresáři aplikace:</span><span class="sxs-lookup"><span data-stu-id="6db50-152">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="6db50-153">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="6db50-153">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="6db50-154">**MVC**</span><span class="sxs-lookup"><span data-stu-id="6db50-154">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="6db50-155">Následující příklad odkazuje na částečné zobrazení pomocí relativní cesty:</span><span class="sxs-lookup"><span data-stu-id="6db50-155">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="6db50-156">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="6db50-156">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="6db50-157">Asynchronní pomocné rutiny HTML</span><span class="sxs-lookup"><span data-stu-id="6db50-157">Asynchronous HTML Helper</span></span>

<span data-ttu-id="6db50-158">Při použití pomocné rutiny HTML, nejlepším postupem je použití <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6db50-158">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="6db50-159">`PartialAsync` Vrátí <xref:Microsoft.AspNetCore.Html.IHtmlContent> typ zabalené v <xref:System.Threading.Tasks.Task`1>.</span><span class="sxs-lookup"><span data-stu-id="6db50-159">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task`1>.</span></span> <span data-ttu-id="6db50-160">Metoda odkazuje jsou očekávané volání s `@` znak:</span><span class="sxs-lookup"><span data-stu-id="6db50-160">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="6db50-161">Pokud přípona souboru je k dispozici, odkazuje na pomocné rutiny HTML částečné zobrazení, který musí být ve stejné složce jako soubor značek volání částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="6db50-161">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="6db50-162">Následující příklad odkazuje částečné zobrazení z kořenového adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="6db50-162">The following example references a partial view from the app root.</span></span> <span data-ttu-id="6db50-163">Cesty, které začínají lomítkem tilda (`~/`) nebo lomítko (`/`) najdete v kořenovém adresáři aplikace:</span><span class="sxs-lookup"><span data-stu-id="6db50-163">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6db50-164">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="6db50-164">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="6db50-165">**MVC**</span><span class="sxs-lookup"><span data-stu-id="6db50-165">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="6db50-166">Následující příklad odkazuje na částečné zobrazení pomocí relativní cesty:</span><span class="sxs-lookup"><span data-stu-id="6db50-166">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="6db50-167">Alternativně můžete vykreslení částečného zobrazení s <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6db50-167">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="6db50-168">Tato metoda nevrací <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span><span class="sxs-lookup"><span data-stu-id="6db50-168">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="6db50-169">Jejich streamování vykresleného výstupu přímo do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6db50-169">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="6db50-170">Protože metoda nevrací výsledek, musí být volána v rámci bloku kódu Razor:</span><span class="sxs-lookup"><span data-stu-id="6db50-170">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="6db50-171">Protože `RenderPartialAsync` datové proudy vykreslený obsah, poskytuje lepší výkon v některých scénářích.</span><span class="sxs-lookup"><span data-stu-id="6db50-171">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="6db50-172">V situacích, kritickém pro výkon srovnávací testy na stránce pomocí obou metod a použít přístup, který generuje rychlejší odezvu.</span><span class="sxs-lookup"><span data-stu-id="6db50-172">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="6db50-173">Synchronní pomocné rutiny HTML</span><span class="sxs-lookup"><span data-stu-id="6db50-173">Synchronous HTML Helper</span></span>

<span data-ttu-id="6db50-174"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> a <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> jsou synchronní ekvivalenty `PartialAsync` a `RenderPartialAsync`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="6db50-174"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="6db50-175">Synchronní ekvivalenty se nedoporučuje, protože existují scénáře, ve kterých zablokování.</span><span class="sxs-lookup"><span data-stu-id="6db50-175">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="6db50-176">Synchronní metody jsou určené pro odstranění v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="6db50-176">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6db50-177">Pokud potřebujete spustit kód, použijte [zobrazení komponenty](xref:mvc/views/view-components) místo částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-177">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6db50-178">Volání `Partial` nebo `RenderPartial` výsledky v analyzátor upozornění aplikace Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6db50-178">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="6db50-179">Například přítomnost `Partial` provede následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="6db50-179">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="6db50-180">Použití IHtmlHelper.Partial může způsobit zablokování aplikace.</span><span class="sxs-lookup"><span data-stu-id="6db50-180">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="6db50-181">Zvažte použití &lt;částečné&gt; pomocné rutiny značky nebo IHtmlHelper.PartialAsync.</span><span class="sxs-lookup"><span data-stu-id="6db50-181">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="6db50-182">Nahraďte volání `@Html.Partial` s `@await Html.PartialAsync` nebo [pomocná rutina částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="6db50-182">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="6db50-183">Další informace o migraci pomocná rutina částečné značky najdete v tématu [migrace ze pomocné rutiny HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span><span class="sxs-lookup"><span data-stu-id="6db50-183">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="6db50-184">Částečné zobrazení zjišťování</span><span class="sxs-lookup"><span data-stu-id="6db50-184">Partial view discovery</span></span>

<span data-ttu-id="6db50-185">Když částečné zobrazení se odkazuje jeho názvem bez přípony souboru, následující umístění budou prohledána v uvedené pořadí:</span><span class="sxs-lookup"><span data-stu-id="6db50-185">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6db50-186">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="6db50-186">**Razor Pages**</span></span>

1. <span data-ttu-id="6db50-187">Právě probíhá na stránce složky</span><span class="sxs-lookup"><span data-stu-id="6db50-187">Currently executing page's folder</span></span>
1. <span data-ttu-id="6db50-188">Directory graphu nahoře na stránce složky</span><span class="sxs-lookup"><span data-stu-id="6db50-188">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="6db50-189">**MVC**</span><span class="sxs-lookup"><span data-stu-id="6db50-189">**MVC**</span></span>

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

<span data-ttu-id="6db50-190">Částečné zobrazení zjišťování platí následující zásady:</span><span class="sxs-lookup"><span data-stu-id="6db50-190">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="6db50-191">Částečná zobrazení jinou se stejným názvem souboru jsou povoleny v případě, že částečná zobrazení jsou v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="6db50-191">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="6db50-192">Při odkazování na částečné zobrazení podle názvu bez přípony souboru a částečné zobrazení je k dispozici ve složce obou volajícího a *Shared* složky, částečné zobrazení ve složce volajícího, jež poskytuje částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-192">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="6db50-193">Pokud není k dispozici ve složce volajícího částečného zobrazení, částečného zobrazení je k dispozici z *Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="6db50-193">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="6db50-194">Částečné zobrazení v *Shared* složky se nazývají *sdílené částečná zobrazení* nebo *výchozí částečná zobrazení*.</span><span class="sxs-lookup"><span data-stu-id="6db50-194">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="6db50-195">Částečná zobrazení mohou být *zřetězené*&mdash;částečné zobrazení můžete volat jiné částečné zobrazení, pokud cyklický odkaz není vytvořený ve volání.</span><span class="sxs-lookup"><span data-stu-id="6db50-195">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="6db50-196">Relativní cesty jsou vždy relativní vzhledem k aktuální soubor nebyl pro kořenový server WSUS nebo nadřazený soubor.</span><span class="sxs-lookup"><span data-stu-id="6db50-196">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="6db50-197">A [Razor](xref:mvc/views/razor) `section` definované v částečné zobrazení je skryt nadřazené značky souborů.</span><span class="sxs-lookup"><span data-stu-id="6db50-197">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="6db50-198">`section` Bude viditelná jenom pro částečné zobrazení, ve kterém je definována.</span><span class="sxs-lookup"><span data-stu-id="6db50-198">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="6db50-199">Přístup k datům z částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="6db50-199">Access data from partial views</span></span>

<span data-ttu-id="6db50-200">Při vytváření instance částečného zobrazení přijme *kopírování* z nadřazeného objektu `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="6db50-200">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="6db50-201">Aktualizace provedené v datech v rámci částečného zobrazení se ukládají do nadřazeného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-201">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="6db50-202">`ViewData` provedené změny v částečné zobrazení při vrátí částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-202">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="6db50-203">Následující příklad ukazuje, jak předat instanci [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="6db50-203">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="6db50-204">Model lze předat do částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-204">You can pass a model into a partial view.</span></span> <span data-ttu-id="6db50-205">Vlastní objekt může být tento model.</span><span class="sxs-lookup"><span data-stu-id="6db50-205">The model can be a custom object.</span></span> <span data-ttu-id="6db50-206">Můžete předat model s `PartialAsync` (vykreslí blok volajícímu) nebo `RenderPartialAsync` (streamování obsahu do výstupu):</span><span class="sxs-lookup"><span data-stu-id="6db50-206">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6db50-207">**Stránky Razor**</span><span class="sxs-lookup"><span data-stu-id="6db50-207">**Razor Pages**</span></span>

<span data-ttu-id="6db50-208">Následující kód v ukázkové aplikaci je z *Pages/ArticlesRP/ReadRP.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="6db50-208">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="6db50-209">Tato stránka obsahuje dvě částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-209">The page contains two partial views.</span></span> <span data-ttu-id="6db50-210">Druhý částečné zobrazení předává do modelu a `ViewData` částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-210">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="6db50-211">`ViewDataDictionary` Přetížení konstruktoru slouží k předání nového `ViewData` slovníku při zachování stávajících `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="6db50-211">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-19)]

<span data-ttu-id="6db50-212">*Pages/Shared/_AuthorPartialRP.cshtml* je první částečné zobrazení odkazuje *ReadRP.cshtml* souboru označení:</span><span class="sxs-lookup"><span data-stu-id="6db50-212">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="6db50-213">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* je druhý částečné zobrazení odkazuje *ReadRP.cshtml* souboru označení:</span><span class="sxs-lookup"><span data-stu-id="6db50-213">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="6db50-214">**MVC**</span><span class="sxs-lookup"><span data-stu-id="6db50-214">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="6db50-215">Následující kód v ukazuje ukázkové aplikace *Views/Articles/Read.cshtml* zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-215">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="6db50-216">Zobrazení obsahuje dvě částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-216">The view contains two partial views.</span></span> <span data-ttu-id="6db50-217">Druhý částečné zobrazení předává do modelu a `ViewData` částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6db50-217">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="6db50-218">`ViewDataDictionary` Přetížení konstruktoru slouží k předání nového `ViewData` slovníku při zachování stávajících `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="6db50-218">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-19)]

<span data-ttu-id="6db50-219">*Views/Shared/_AuthorPartial.cshtml* je první částečné zobrazení odkazuje *ReadRP.cshtml* souboru označení:</span><span class="sxs-lookup"><span data-stu-id="6db50-219">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="6db50-220">*Views/Articles/_ArticleSection.cshtml* je druhý částečné zobrazení odkazuje *Read.cshtml* souboru označení:</span><span class="sxs-lookup"><span data-stu-id="6db50-220">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="6db50-221">Za běhu, jsou vykreslovány částečné do souboru kódu nadřazené vykresleného výstupu, který je vykreslen v rámci sdíleného *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6db50-221">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="6db50-222">První částečné zobrazení vykreslí autoři článku název a datum publikování:</span><span class="sxs-lookup"><span data-stu-id="6db50-222">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="6db50-223">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="6db50-223">Abraham Lincoln</span></span>
>
> <span data-ttu-id="6db50-224">Toto částečné zobrazení z &lt;cesta k souboru sdílené částečné zobrazení&gt;.</span><span class="sxs-lookup"><span data-stu-id="6db50-224">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="6db50-225">19/11/1863 12:00:00 AM</span><span class="sxs-lookup"><span data-stu-id="6db50-225">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="6db50-226">Druhý částečné zobrazení vykreslí části článku:</span><span class="sxs-lookup"><span data-stu-id="6db50-226">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="6db50-227">Části jeden Index: 0</span><span class="sxs-lookup"><span data-stu-id="6db50-227">Section One Index: 0</span></span>
>
> <span data-ttu-id="6db50-228">Čtyři skóre a sedm let před...</span><span class="sxs-lookup"><span data-stu-id="6db50-228">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="6db50-229">Index oddílu dvou: 1</span><span class="sxs-lookup"><span data-stu-id="6db50-229">Section Two Index: 1</span></span>
>
> <span data-ttu-id="6db50-230">Nyní jsme se zabývají skvělé občanské války, testování...</span><span class="sxs-lookup"><span data-stu-id="6db50-230">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="6db50-231">Index oddílu 3: 2</span><span class="sxs-lookup"><span data-stu-id="6db50-231">Section Three Index: 2</span></span>
>
> <span data-ttu-id="6db50-232">Ale ve větší smysl, jsme nelze vyhradit...</span><span class="sxs-lookup"><span data-stu-id="6db50-232">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6db50-233">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6db50-233">Additional resources</span></span>

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
