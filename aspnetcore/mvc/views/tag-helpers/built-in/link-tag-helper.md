---
title: Pomocná pomůcka značky v ASP.NET Core
author: rick-anderson
ms.author: riande
description: Seznamte se s atributy pomocníka značek ASP.NET Core odkazů a role, kterou každý atribut hraje při rozšiřování chování značky odkazu HTML.
ms.custom: mvc
ms.date: 09/24/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 1efd7c1a63baea4312a4a01cd9cd9c7582375d97
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777351"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="212a5-103">Pomocná pomůcka značky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="212a5-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="212a5-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="212a5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="212a5-105">[Pomocník značek odkazů](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generuje odkaz na primární nebo vrácený soubor CSS.</span><span class="sxs-lookup"><span data-stu-id="212a5-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="212a5-106">Primární soubor CSS je obvykle na [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="212a5-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="212a5-107">Pomocník značek odkazů umožňuje zadat CDN pro soubor CSS a zálohu, pokud CDN není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="212a5-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="212a5-108">Pomocník značek odkazů nabízí výkonnou výhodu sítě CDN s odolností místního hostování.</span><span class="sxs-lookup"><span data-stu-id="212a5-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="212a5-109">Následující Razor kód ukazuje `head` prvek souboru rozložení vytvořeného pomocí šablony ASP.NET Core webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="212a5-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="212a5-110">Následující kód vykreslí kód HTML z předchozího kódu (v nevývojovém prostředí):</span><span class="sxs-lookup"><span data-stu-id="212a5-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="212a5-111">V předchozím kódu pomocník značek odkazů vygeneroval `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` prvek a následující JavaScript, který slouží k ověření požadovaného souboru *bootstrap. min. CSS* , je k dispozici v CDN.</span><span class="sxs-lookup"><span data-stu-id="212a5-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="212a5-112">V tomto případě byl k dispozici soubor CSS, aby pomocník značek vygeneroval `<link />` prvek se souborem. CSS CDN.</span><span class="sxs-lookup"><span data-stu-id="212a5-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="212a5-113">Běžně používané pomocné atributy značek odkazů</span><span class="sxs-lookup"><span data-stu-id="212a5-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="212a5-114">U všech pomocných atributů, vlastností a metod odkazů můžete zobrazit nápovědu pro [značku odkazu](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) .</span><span class="sxs-lookup"><span data-stu-id="212a5-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="212a5-115">odkaz</span><span class="sxs-lookup"><span data-stu-id="212a5-115">href</span></span>

<span data-ttu-id="212a5-116">Upřednostňovaná adresa propojeného prostředku</span><span class="sxs-lookup"><span data-stu-id="212a5-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="212a5-117">Adresa je předána na vygenerovaný kód HTML ve všech případech.</span><span class="sxs-lookup"><span data-stu-id="212a5-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="212a5-118">ASP – Fallback – href</span><span class="sxs-lookup"><span data-stu-id="212a5-118">asp-fallback-href</span></span>

<span data-ttu-id="212a5-119">Adresa URL šablony stylů CSS, na kterou se má přejít v případě, že primární adresa URL není úspěšná</span><span class="sxs-lookup"><span data-stu-id="212a5-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="212a5-120">ASP – Fallback-test-třída</span><span class="sxs-lookup"><span data-stu-id="212a5-120">asp-fallback-test-class</span></span>

<span data-ttu-id="212a5-121">Název třídy definovaný v šabloně stylů, která se má použít pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="212a5-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="212a5-122">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="212a5-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="212a5-123">ASP – Fallback-test-vlastnost</span><span class="sxs-lookup"><span data-stu-id="212a5-123">asp-fallback-test-property</span></span>

<span data-ttu-id="212a5-124">Název vlastnosti CSS, který má být použit pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="212a5-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="212a5-125">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="212a5-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="212a5-126">ASP – Fallback-test-hodnota</span><span class="sxs-lookup"><span data-stu-id="212a5-126">asp-fallback-test-value</span></span>

<span data-ttu-id="212a5-127">Hodnota vlastnosti CSS, která má být použita pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="212a5-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="212a5-128">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="212a5-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="212a5-129">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="212a5-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
