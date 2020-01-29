---
title: Pomocná pomůcka značky v ASP.NET Core
author: rick-anderson
ms.author: riande
description: Seznamte se s atributy pomocníka značek ASP.NET Core odkazů a role, kterou každý atribut hraje při rozšiřování chování značky odkazu HTML.
ms.custom: mvc
ms.date: 09/24/2019
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: d7514433bee8a138cd7d75bfd15c9798d4fd31a3
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809104"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="b3b84-103">Pomocná pomůcka značky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b3b84-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="b3b84-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b3b84-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b3b84-105">[Pomocník značek odkazů](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generuje odkaz na primární nebo vrácený soubor CSS.</span><span class="sxs-lookup"><span data-stu-id="b3b84-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="b3b84-106">Primární soubor CSS je obvykle na [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="b3b84-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="b3b84-107">Pomocník značek odkazů umožňuje zadat CDN pro soubor CSS a zálohu, pokud CDN není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="b3b84-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="b3b84-108">Pomocník značek odkazů nabízí výkonnou výhodu sítě CDN s odolností místního hostování.</span><span class="sxs-lookup"><span data-stu-id="b3b84-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="b3b84-109">Následující kód Razor ukazuje `head` element souboru rozložení vytvořeného pomocí šablony ASP.NET Core webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="b3b84-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="b3b84-110">Následující kód vykreslí kód HTML z předchozího kódu (v nevývojovém prostředí):</span><span class="sxs-lookup"><span data-stu-id="b3b84-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="b3b84-111">V předchozím kódu pomocník značek odkazů vygeneroval prvek `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` a následující JavaScript, který slouží k ověření požadovaného souboru *bootstrap. min. CSS* , je k dispozici v CDN.</span><span class="sxs-lookup"><span data-stu-id="b3b84-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="b3b84-112">V tomto případě byl soubor CSS k dispozici, takže pomocná rutina značky vygenerovala prvek `<link />` se souborem. CSS pro CDN.</span><span class="sxs-lookup"><span data-stu-id="b3b84-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="b3b84-113">Běžně používané pomocné atributy značek odkazů</span><span class="sxs-lookup"><span data-stu-id="b3b84-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="b3b84-114">U všech pomocných atributů, vlastností a metod odkazů můžete zobrazit nápovědu pro [značku odkazu](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) .</span><span class="sxs-lookup"><span data-stu-id="b3b84-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="b3b84-115">href</span><span class="sxs-lookup"><span data-stu-id="b3b84-115">href</span></span>

<span data-ttu-id="b3b84-116">Upřednostňovaná adresa propojeného prostředku</span><span class="sxs-lookup"><span data-stu-id="b3b84-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="b3b84-117">Adresa je předána na vygenerovaný kód HTML ve všech případech.</span><span class="sxs-lookup"><span data-stu-id="b3b84-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="b3b84-118">ASP – Fallback – href</span><span class="sxs-lookup"><span data-stu-id="b3b84-118">asp-fallback-href</span></span>

<span data-ttu-id="b3b84-119">Adresa URL šablony stylů CSS, na kterou se má přejít v případě, že primární adresa URL není úspěšná</span><span class="sxs-lookup"><span data-stu-id="b3b84-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="b3b84-120">ASP – Fallback-test-třída</span><span class="sxs-lookup"><span data-stu-id="b3b84-120">asp-fallback-test-class</span></span>

<span data-ttu-id="b3b84-121">Název třídy definovaný v šabloně stylů, která se má použít pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="b3b84-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="b3b84-122">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="b3b84-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="b3b84-123">ASP – Fallback-test-vlastnost</span><span class="sxs-lookup"><span data-stu-id="b3b84-123">asp-fallback-test-property</span></span>

<span data-ttu-id="b3b84-124">Název vlastnosti CSS, který má být použit pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="b3b84-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="b3b84-125">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="b3b84-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="b3b84-126">ASP – Fallback-test-hodnota</span><span class="sxs-lookup"><span data-stu-id="b3b84-126">asp-fallback-test-value</span></span>

<span data-ttu-id="b3b84-127">Hodnota vlastnosti CSS, která má být použita pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="b3b84-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="b3b84-128">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="b3b84-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3b84-129">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b3b84-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
