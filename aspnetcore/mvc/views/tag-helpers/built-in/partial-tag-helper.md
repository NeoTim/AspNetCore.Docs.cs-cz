---
title: Podpora částečné značky v ASP.NET Core
author: scottaddie
description: Seznamte se s pomocníkem pro ASP.NET Core částečné značky a role, kde každý z jeho atributů hraje při vykreslování částečného zobrazení.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 5925594b5429f011a0694dbe14218211a52d6377
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018452"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="ad18f-103">Podpora částečné značky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad18f-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="ad18f-104">[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="ad18f-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="ad18f-105">Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="ad18f-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="ad18f-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ad18f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="ad18f-107">Přehled</span><span class="sxs-lookup"><span data-stu-id="ad18f-107">Overview</span></span>

<span data-ttu-id="ad18f-108">Pomocník s částečnou značkou se používá pro vykreslování [částečného zobrazení](xref:mvc/views/partial) na Razor stránkách a aplikacích MVC.</span><span class="sxs-lookup"><span data-stu-id="ad18f-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="ad18f-109">Vezměte v úvahu:</span><span class="sxs-lookup"><span data-stu-id="ad18f-109">Consider that it:</span></span>

* <span data-ttu-id="ad18f-110">Vyžaduje ASP.NET Core 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="ad18f-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="ad18f-111">Je alternativou k [syntaxi pomocníka HTML](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="ad18f-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="ad18f-112">Vykreslí částečné zobrazení asynchronně.</span><span class="sxs-lookup"><span data-stu-id="ad18f-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="ad18f-113">Možnosti pomocníka HTML pro vykreslování částečného zobrazení zahrnují:</span><span class="sxs-lookup"><span data-stu-id="ad18f-113">The HTML Helper options for rendering a partial view include:</span></span>

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="ad18f-114">*Produktový* model se používá v ukázkách v tomto dokumentu:</span><span class="sxs-lookup"><span data-stu-id="ad18f-114">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="ad18f-115">Následuje inventarizace atributů pomocníka částečné značky.</span><span class="sxs-lookup"><span data-stu-id="ad18f-115">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="ad18f-116">name</span><span class="sxs-lookup"><span data-stu-id="ad18f-116">name</span></span>

<span data-ttu-id="ad18f-117">`name` Atribut je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="ad18f-117">The `name` attribute is required.</span></span> <span data-ttu-id="ad18f-118">Označuje název nebo cestu k částečnému zobrazení, které se má vykreslit.</span><span class="sxs-lookup"><span data-stu-id="ad18f-118">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="ad18f-119">Je-li zadán název částečného zobrazení, je zahájen proces [zjišťování zobrazení](xref:mvc/views/overview#view-discovery) .</span><span class="sxs-lookup"><span data-stu-id="ad18f-119">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="ad18f-120">Tento proces se obejít, když je zadána explicitní cesta.</span><span class="sxs-lookup"><span data-stu-id="ad18f-120">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="ad18f-121">Všechny přijatelné `name` hodnoty najdete v tématu [zjišťování částečného zobrazení](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="ad18f-121">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="ad18f-122">Následující kód používá explicitní cestu, která značí, že *_ProductPartial. cshtml* má být načten ze *sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="ad18f-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="ad18f-123">Pomocí atributu [for](#for) je model předán do částečného zobrazení pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="ad18f-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="ad18f-124">pro</span><span class="sxs-lookup"><span data-stu-id="ad18f-124">for</span></span>

<span data-ttu-id="ad18f-125">`for`Atribut přiřadí [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) k vyhodnocení proti aktuálnímu modelu.</span><span class="sxs-lookup"><span data-stu-id="ad18f-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="ad18f-126">A `ModelExpression` odvodí `@Model.` syntaxi.</span><span class="sxs-lookup"><span data-stu-id="ad18f-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="ad18f-127">Například `for="Product"` lze použít místo `for="@Model.Product"` .</span><span class="sxs-lookup"><span data-stu-id="ad18f-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="ad18f-128">Toto výchozí chování odvození je potlačeno pomocí `@` symbolu pro definování vloženého výrazu.</span><span class="sxs-lookup"><span data-stu-id="ad18f-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="ad18f-129">Následující kód načte *_ProductPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ad18f-129">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="ad18f-130">Částečné zobrazení je vázáno na vlastnost přidruženého modelu stránky `Product` :</span><span class="sxs-lookup"><span data-stu-id="ad18f-130">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="ad18f-131">model</span><span class="sxs-lookup"><span data-stu-id="ad18f-131">model</span></span>

<span data-ttu-id="ad18f-132">`model`Atribut přiřadí instanci modelu, aby byla předána částečnému zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ad18f-132">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="ad18f-133">`model`Atribut nelze použít s atributem [for](#for) .</span><span class="sxs-lookup"><span data-stu-id="ad18f-133">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="ad18f-134">V následujícím kódu `Product` je vytvořena instance nového objektu a předána do `model` atributu pro vazbu:</span><span class="sxs-lookup"><span data-stu-id="ad18f-134">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="ad18f-135">zobrazení dat</span><span class="sxs-lookup"><span data-stu-id="ad18f-135">view-data</span></span>

<span data-ttu-id="ad18f-136">`view-data`Atribut přiřadí objekt [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) , který se má předat částečnému zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ad18f-136">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="ad18f-137">Následující kód zpřístupňuje celou kolekci ViewData pro částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="ad18f-137">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="ad18f-138">V předchozím kódu `IsNumberReadOnly` je klíčová hodnota nastavena na `true` a přidána do kolekce ViewData.</span><span class="sxs-lookup"><span data-stu-id="ad18f-138">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="ad18f-139">V důsledku toho `ViewData["IsNumberReadOnly"]` je k dispozici v následujícím částečném zobrazení:</span><span class="sxs-lookup"><span data-stu-id="ad18f-139">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="ad18f-140">V tomto příkladu hodnota `ViewData["IsNumberReadOnly"]` Určuje, zda se pole *číslo* zobrazuje pouze pro čtení.</span><span class="sxs-lookup"><span data-stu-id="ad18f-140">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="ad18f-141">Migrace z pomocníka jazyka HTML</span><span class="sxs-lookup"><span data-stu-id="ad18f-141">Migrate from an HTML Helper</span></span>

<span data-ttu-id="ad18f-142">Zvažte následující příklad asynchronní pomocné rutiny HTML.</span><span class="sxs-lookup"><span data-stu-id="ad18f-142">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="ad18f-143">Kolekce produktů je iterovaná a zobrazená.</span><span class="sxs-lookup"><span data-stu-id="ad18f-143">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="ad18f-144">Na `PartialAsync` první parametr metody je načteno částečné zobrazení *_ProductPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ad18f-144">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="ad18f-145">Instance `Product` modelu je předána do částečného zobrazení pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="ad18f-145">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="ad18f-146">Následující pomocník částečné značky dosáhne stejné chování při asynchronním vykreslování jako `PartialAsync` Pomocník HTML.</span><span class="sxs-lookup"><span data-stu-id="ad18f-146">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="ad18f-147">`model`Atributu je přiřazena `Product` instance modelu pro vazbu k částečnému zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ad18f-147">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="ad18f-148">Další materiály</span><span class="sxs-lookup"><span data-stu-id="ad18f-148">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
