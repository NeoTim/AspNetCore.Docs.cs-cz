---
title: Pomocník značek Script v ASP.NET Core
author: rick-anderson
ms.author: riande
description: Seznamte se s atributy pomocníka značky skriptu ASP.NET Core a role, které každý atribut hraje při rozšiřování chování značky skriptu HTML.
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 5f2fb8a45048804afa8aff2989cd53489e45a33b
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256496"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="dc624-103">Pomocník značek Script v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dc624-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="dc624-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dc624-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dc624-105">[Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) vygeneruje odkaz na primární soubor nebo soubor skriptu.</span><span class="sxs-lookup"><span data-stu-id="dc624-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="dc624-106">Obvykle je soubor primárního skriptu na [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="dc624-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="dc624-107">Pomocná značka skriptu umožňuje zadat síť CDN pro soubor skriptu a zálohu, pokud CDN není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="dc624-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="dc624-108">Pomocník značek Script poskytuje výkonnou výhodu sítě CDN s odolností místního hostování.</span><span class="sxs-lookup"><span data-stu-id="dc624-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="dc624-109">Následující kód Razor ukazuje `script` prvek souboru rozložení vytvořeného pomocí šablony ASP.NET Core webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="dc624-109">The following Razor markup shows the `script` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

<span data-ttu-id="dc624-110">Následující text je podobný vykreslenému HTML z předchozího kódu (v nevývojovém prostředí):</span><span class="sxs-lookup"><span data-stu-id="dc624-110">The following is similar to the rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

<span data-ttu-id="dc624-111">V předchozím kódu pomocník značky skriptu vygeneroval druhý element Script ( `<script>  (window.jQuery || document.write(`), který testuje pro. `window.jQuery`</span><span class="sxs-lookup"><span data-stu-id="dc624-111">In the preceding code, the Script Tag Helper generated the second script ( `<script>  (window.jQuery || document.write(`) element, which tests for `window.jQuery`.</span></span> <span data-ttu-id="dc624-112">Pokud `window.jQuery` se nenajde, `document.write(` spustí a vytvoří skript.</span><span class="sxs-lookup"><span data-stu-id="dc624-112">If `window.jQuery` is not found, `document.write(` runs and creates a script</span></span> 

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="dc624-113">Běžně používané pomocné atributy značek skriptů</span><span class="sxs-lookup"><span data-stu-id="dc624-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="dc624-114">Viz [Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) pro všechny pomocné atributy, vlastnosti a metody značky skriptu.</span><span class="sxs-lookup"><span data-stu-id="dc624-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="dc624-115">href</span><span class="sxs-lookup"><span data-stu-id="dc624-115">href</span></span>

<span data-ttu-id="dc624-116">Upřednostňovaná adresa propojeného prostředku</span><span class="sxs-lookup"><span data-stu-id="dc624-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="dc624-117">Adresa je předána na vygenerovaný kód HTML ve všech případech.</span><span class="sxs-lookup"><span data-stu-id="dc624-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="dc624-118">ASP – Fallback – href</span><span class="sxs-lookup"><span data-stu-id="dc624-118">asp-fallback-href</span></span>

<span data-ttu-id="dc624-119">Adresa URL šablony stylů CSS, na kterou se má přejít v případě, že primární adresa URL není úspěšná</span><span class="sxs-lookup"><span data-stu-id="dc624-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="dc624-120">ASP – Fallback-test-třída</span><span class="sxs-lookup"><span data-stu-id="dc624-120">asp-fallback-test-class</span></span>

<span data-ttu-id="dc624-121">Název třídy definovaný v šabloně stylů, která se má použít pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="dc624-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="dc624-122">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="dc624-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="dc624-123">ASP – Fallback-test-vlastnost</span><span class="sxs-lookup"><span data-stu-id="dc624-123">asp-fallback-test-property</span></span>

<span data-ttu-id="dc624-124">Název vlastnosti CSS, který má být použit pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="dc624-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="dc624-125">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="dc624-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="dc624-126">ASP – Fallback-test-hodnota</span><span class="sxs-lookup"><span data-stu-id="dc624-126">asp-fallback-test-value</span></span>

<span data-ttu-id="dc624-127">Hodnota vlastnosti CSS, která má být použita pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="dc624-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="dc624-128">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="dc624-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="dc624-129">ASP – Fallback-test-hodnota</span><span class="sxs-lookup"><span data-stu-id="dc624-129">asp-fallback-test-value</span></span>

<span data-ttu-id="dc624-130">Hodnota vlastnosti CSS, která má být použita pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="dc624-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="dc624-131">Další informace najdete v tématu.<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span><span class="sxs-lookup"><span data-stu-id="dc624-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="dc624-132">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dc624-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
