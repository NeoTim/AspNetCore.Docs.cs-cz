---
title: Pomocná rutina částečné značky v ASP.NET Core
author: scottaddie
description: Zjišťování ASP.NET Core částečné pomocné rutiny značky a role, každá z jeho atributy přehrávání ve vykreslení částečného zobrazení.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/13/2018
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 0a8caf09d1764278da4a0566844b0efaf4eeb567
ms.sourcegitcommit: 18339e3cb5a891a3ca36d8146fa83cf91c32e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37433867"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="f483f-103">Pomocná rutina částečné značky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f483f-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="f483f-104">Podle [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="f483f-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="f483f-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([stažení](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f483f-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="f483f-106">Přehled</span><span class="sxs-lookup"><span data-stu-id="f483f-106">Overview</span></span>

<span data-ttu-id="f483f-107">Pomocná rutina částečné značky se používá pro vykreslování [částečné zobrazení](xref:mvc/views/partial) v aplikacích pro Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="f483f-107">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="f483f-108">Vezměte v úvahu, že:</span><span class="sxs-lookup"><span data-stu-id="f483f-108">Consider that it:</span></span>

* <span data-ttu-id="f483f-109">Vyžaduje ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="f483f-109">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="f483f-110">Představuje alternativu k [pomocné rutiny HTML syntaxe](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="f483f-110">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="f483f-111">Vykreslí částečné zobrazení asynchronně.</span><span class="sxs-lookup"><span data-stu-id="f483f-111">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="f483f-112">Možnosti pomocné rutiny HTML pro vykreslení částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f483f-112">The HTML Helper options for rendering a partial view include:</span></span>

* [<span data-ttu-id="f483f-113">@await Html.PartialAsync</span><span class="sxs-lookup"><span data-stu-id="f483f-113">@await Html.PartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [<span data-ttu-id="f483f-114">@await Html.RenderPartialAsync</span><span class="sxs-lookup"><span data-stu-id="f483f-114">@await Html.RenderPartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [@Html.Partial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [@Html.RenderPartial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="f483f-115">*Produktu* model se používá v ukázky v tomto dokumentu:</span><span class="sxs-lookup"><span data-stu-id="f483f-115">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="f483f-116">Následuje seznam atributů pomocná rutina částečné značky.</span><span class="sxs-lookup"><span data-stu-id="f483f-116">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="f483f-117">name</span><span class="sxs-lookup"><span data-stu-id="f483f-117">name</span></span>

<span data-ttu-id="f483f-118">`name` Atribut je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="f483f-118">The `name` attribute is required.</span></span> <span data-ttu-id="f483f-119">Označuje název nebo cesta k vykreslení částečného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f483f-119">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="f483f-120">Když je zadaný název částečného zobrazení, [zobrazení zjišťování](xref:mvc/views/overview#view-discovery) zahájit proces.</span><span class="sxs-lookup"><span data-stu-id="f483f-120">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="f483f-121">Tento proces je obejít, když je k dispozici explicitní cestu.</span><span class="sxs-lookup"><span data-stu-id="f483f-121">That process is bypassed when an explicit path is provided.</span></span>

<span data-ttu-id="f483f-122">Následující kód používá cestu k explicitní, což indikuje, že *_ProductPartial.cshtml* má být načtena z *Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="f483f-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="f483f-123">Použití [pro](#for) atribut, modelu je předán do částečné zobrazení pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="f483f-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="f483f-124">pro</span><span class="sxs-lookup"><span data-stu-id="f483f-124">for</span></span>

<span data-ttu-id="f483f-125">`for` Atribut přiřadí [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) má být porovnán s aktuální model.</span><span class="sxs-lookup"><span data-stu-id="f483f-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="f483f-126">A `ModelExpression` odvodí, `@Model.` syntaxe.</span><span class="sxs-lookup"><span data-stu-id="f483f-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="f483f-127">Například `for="Product"` lze použít místo `for="@Model.Product"`.</span><span class="sxs-lookup"><span data-stu-id="f483f-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="f483f-128">Toto výchozí chování odvození přepsán pomocí `@` symbolů k definování vložený výraz.</span><span class="sxs-lookup"><span data-stu-id="f483f-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span> <span data-ttu-id="f483f-129">`for` Atributu nelze použít s [modelu](#model) atribut.</span><span class="sxs-lookup"><span data-stu-id="f483f-129">The `for` attribute can't be used with the [model](#model) attribute.</span></span>

<span data-ttu-id="f483f-130">Následující kód načte *_ProductPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f483f-130">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="f483f-131">Částečné zobrazení je vázán na příslušné stránce modelu `Product` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f483f-131">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="f483f-132">model</span><span class="sxs-lookup"><span data-stu-id="f483f-132">model</span></span>

<span data-ttu-id="f483f-133">`model` Atribut přiřadí instance modelu má být předána do částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f483f-133">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="f483f-134">`model` Atributu nelze použít s [pro](#for) atribut.</span><span class="sxs-lookup"><span data-stu-id="f483f-134">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="f483f-135">V následujícím kódu novou `Product` objektu je vytvořena instance a předat `model` atribut pro vazbu:</span><span class="sxs-lookup"><span data-stu-id="f483f-135">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="f483f-136">view-data</span><span class="sxs-lookup"><span data-stu-id="f483f-136">view-data</span></span>

<span data-ttu-id="f483f-137">`view-data` Atribut přiřadí [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) k předání do částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f483f-137">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="f483f-138">Následující kód zpřístupňuje celou kolekci slovníku ViewData částečného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f483f-138">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="f483f-139">V předchozím kódu `IsNumberReadOnly` nastavena na hodnotu klíče `true` a přidat do kolekce slovníku ViewData.</span><span class="sxs-lookup"><span data-stu-id="f483f-139">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="f483f-140">V důsledku toho `ViewData["IsNumberReadOnly"]` zpřístupněna v rámci následující částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f483f-140">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="f483f-141">V tomto příkladu hodnota `ViewData["IsNumberReadOnly"]` Určuje, zda *číslo* zobrazí pole jako jen pro čtení.</span><span class="sxs-lookup"><span data-stu-id="f483f-141">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f483f-142">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f483f-142">Additional resources</span></span>

* [<span data-ttu-id="f483f-143">Částečná zobrazení</span><span class="sxs-lookup"><span data-stu-id="f483f-143">Partial views</span></span>](xref:mvc/views/partial)
* [<span data-ttu-id="f483f-144">Slabě typované data (ViewData, atribut ViewData a objekt ViewBag)</span><span class="sxs-lookup"><span data-stu-id="f483f-144">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>](xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag)
