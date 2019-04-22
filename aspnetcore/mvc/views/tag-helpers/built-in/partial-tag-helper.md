---
title: Pomocná rutina částečné značky v ASP.NET Core
author: scottaddie
description: Zjišťování ASP.NET Core částečné pomocné rutiny značky a role, každá z jeho atributy přehrávání ve vykreslení částečného zobrazení.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 116fce7af5dc138fbbb0351a4f38f59e88c8f338
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468679"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="2a51b-103">Pomocná rutina částečné značky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a51b-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="2a51b-104">Podle [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="2a51b-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2a51b-105">Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="2a51b-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="2a51b-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2a51b-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="2a51b-107">Přehled</span><span class="sxs-lookup"><span data-stu-id="2a51b-107">Overview</span></span>

<span data-ttu-id="2a51b-108">Pomocná rutina částečné značky se používá pro vykreslování [částečné zobrazení](xref:mvc/views/partial) v aplikacích pro Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="2a51b-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="2a51b-109">Vezměte v úvahu, že:</span><span class="sxs-lookup"><span data-stu-id="2a51b-109">Consider that it:</span></span>

* <span data-ttu-id="2a51b-110">Vyžaduje ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="2a51b-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="2a51b-111">Představuje alternativu k [pomocné rutiny HTML syntaxe](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="2a51b-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="2a51b-112">Vykreslí částečné zobrazení asynchronně.</span><span class="sxs-lookup"><span data-stu-id="2a51b-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="2a51b-113">Možnosti pomocné rutiny HTML pro vykreslení částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2a51b-113">The HTML Helper options for rendering a partial view include:</span></span>

* [<span data-ttu-id="2a51b-114">@await Html.PartialAsync</span><span class="sxs-lookup"><span data-stu-id="2a51b-114">@await Html.PartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [<span data-ttu-id="2a51b-115">@await Html.RenderPartialAsync</span><span class="sxs-lookup"><span data-stu-id="2a51b-115">@await Html.RenderPartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [@Html.Partial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [@Html.RenderPartial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="2a51b-116">*Produktu* model se používá v ukázky v tomto dokumentu:</span><span class="sxs-lookup"><span data-stu-id="2a51b-116">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="2a51b-117">Následuje seznam atributů pomocná rutina částečné značky.</span><span class="sxs-lookup"><span data-stu-id="2a51b-117">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="2a51b-118">name</span><span class="sxs-lookup"><span data-stu-id="2a51b-118">name</span></span>

<span data-ttu-id="2a51b-119">`name` Atribut je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="2a51b-119">The `name` attribute is required.</span></span> <span data-ttu-id="2a51b-120">Označuje název nebo cesta k vykreslení částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2a51b-120">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="2a51b-121">Když je zadaný název částečného zobrazení, [zobrazení zjišťování](xref:mvc/views/overview#view-discovery) zahájit proces.</span><span class="sxs-lookup"><span data-stu-id="2a51b-121">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="2a51b-122">Tento proces je obejít, když je k dispozici explicitní cestu.</span><span class="sxs-lookup"><span data-stu-id="2a51b-122">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="2a51b-123">Pro všechny přijatelné `name` hodnoty, najdete v článku [částečné zobrazení zjišťování](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="2a51b-123">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="2a51b-124">Následující kód používá cestu k explicitní, což indikuje, že *_ProductPartial.cshtml* má být načtena z *Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="2a51b-124">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="2a51b-125">Použití [pro](#for) atribut, modelu je předán do částečné zobrazení pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="2a51b-125">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="2a51b-126">pro</span><span class="sxs-lookup"><span data-stu-id="2a51b-126">for</span></span>

<span data-ttu-id="2a51b-127">`for` Atribut přiřadí [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) má být porovnán s aktuální model.</span><span class="sxs-lookup"><span data-stu-id="2a51b-127">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="2a51b-128">A `ModelExpression` odvodí, `@Model.` syntaxe.</span><span class="sxs-lookup"><span data-stu-id="2a51b-128">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="2a51b-129">Například `for="Product"` lze použít místo `for="@Model.Product"`.</span><span class="sxs-lookup"><span data-stu-id="2a51b-129">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="2a51b-130">Toto výchozí chování odvození přepsán pomocí `@` symbolů k definování vložený výraz.</span><span class="sxs-lookup"><span data-stu-id="2a51b-130">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="2a51b-131">Následující kód načte *_ProductPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2a51b-131">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="2a51b-132">Částečné zobrazení je vázán na příslušné stránce modelu `Product` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="2a51b-132">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="2a51b-133">model</span><span class="sxs-lookup"><span data-stu-id="2a51b-133">model</span></span>

<span data-ttu-id="2a51b-134">`model` Atribut přiřadí instance modelu má být předána do částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2a51b-134">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="2a51b-135">`model` Atributu nelze použít s [pro](#for) atribut.</span><span class="sxs-lookup"><span data-stu-id="2a51b-135">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="2a51b-136">V následujícím kódu novou `Product` objektu je vytvořena instance a předat `model` atribut pro vazbu:</span><span class="sxs-lookup"><span data-stu-id="2a51b-136">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="2a51b-137">view-data</span><span class="sxs-lookup"><span data-stu-id="2a51b-137">view-data</span></span>

<span data-ttu-id="2a51b-138">`view-data` Atribut přiřadí [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) k předání do částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2a51b-138">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="2a51b-139">Následující kód zpřístupňuje celou kolekci slovníku ViewData částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2a51b-139">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="2a51b-140">V předchozím kódu `IsNumberReadOnly` nastavena na hodnotu klíče `true` a přidat do kolekce slovníku ViewData.</span><span class="sxs-lookup"><span data-stu-id="2a51b-140">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="2a51b-141">V důsledku toho `ViewData["IsNumberReadOnly"]` zpřístupněna v rámci následující částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2a51b-141">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="2a51b-142">V tomto příkladu hodnota `ViewData["IsNumberReadOnly"]` Určuje, zda *číslo* zobrazí pole jako jen pro čtení.</span><span class="sxs-lookup"><span data-stu-id="2a51b-142">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="2a51b-143">Migrace ze pomocné rutiny HTML</span><span class="sxs-lookup"><span data-stu-id="2a51b-143">Migrate from an HTML Helper</span></span>

<span data-ttu-id="2a51b-144">Zvažte následující příklad asynchronní pomocné rutiny HTML.</span><span class="sxs-lookup"><span data-stu-id="2a51b-144">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="2a51b-145">Kolekce produktů provést iteraci a zobrazit.</span><span class="sxs-lookup"><span data-stu-id="2a51b-145">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="2a51b-146">Za `PartialAsync` první parametr metody, *_ProductPartial.cshtml* načtení částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2a51b-146">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="2a51b-147">Instance `Product` model částečného zobrazení předán pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="2a51b-147">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="2a51b-148">Následující pomocná rutina částečné značky dosáhne stejné chování jako asynchronní vykreslování `PartialAsync` pomocné rutiny HTML.</span><span class="sxs-lookup"><span data-stu-id="2a51b-148">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="2a51b-149">`model` Atributu je přiřazena `Product` instanci modelu pro vazbu na částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2a51b-149">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="2a51b-150">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2a51b-150">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
