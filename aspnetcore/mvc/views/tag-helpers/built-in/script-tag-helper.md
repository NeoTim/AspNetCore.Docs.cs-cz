---
title: Pomocník značek Script v ASP.NET Core
author: rick-anderson
ms.author: riande
description: Seznamte se s atributy pomocníka značky skriptu ASP.NET Core a role, které každý atribut hraje při rozšiřování chování značky skriptu HTML.
ms.custom: mvc
ms.date: 12/02/2019
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 8a90eb5a74ff3f8178a47c59ad7ba1b6a389ab87
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717374"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="69cd3-103">Pomocník značek Script v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69cd3-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="69cd3-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="69cd3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="69cd3-105">[Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) vygeneruje odkaz na primární soubor nebo soubor skriptu.</span><span class="sxs-lookup"><span data-stu-id="69cd3-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="69cd3-106">Obvykle je soubor primárního skriptu na [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="69cd3-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="69cd3-107">Pomocná značka skriptu umožňuje zadat síť CDN pro soubor skriptu a zálohu, pokud CDN není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="69cd3-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="69cd3-108">Pomocník značek Script poskytuje výkonnou výhodu sítě CDN s odolností místního hostování.</span><span class="sxs-lookup"><span data-stu-id="69cd3-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="69cd3-109">Následující kód Razor ukazuje `script` element s Fallback:</span><span class="sxs-lookup"><span data-stu-id="69cd3-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```HTML
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="69cd3-110">Nepoužívejte atribut [odložení](https://developer.mozilla.org/docs/Web/HTML/Element/script) elementu `<script>` pro odložení načtení skriptu CDN.</span><span class="sxs-lookup"><span data-stu-id="69cd3-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="69cd3-111">Pomocník značek Script vykresluje JavaScript, který okamžitě spustí výraz [ASP-Fallback-test](#asp-fallback-test) .</span><span class="sxs-lookup"><span data-stu-id="69cd3-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="69cd3-112">Pokud je načítání skriptu CDN odložené, výraz se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="69cd3-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="69cd3-113">Běžně používané pomocné atributy značek skriptů</span><span class="sxs-lookup"><span data-stu-id="69cd3-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="69cd3-114">Viz [Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) pro všechny pomocné atributy, vlastnosti a metody značky skriptu.</span><span class="sxs-lookup"><span data-stu-id="69cd3-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="69cd3-115">ASP – Fallback-test</span><span class="sxs-lookup"><span data-stu-id="69cd3-115">asp-fallback-test</span></span>

<span data-ttu-id="69cd3-116">Metoda skriptu definovaná v primárním skriptu pro použití pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="69cd3-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="69cd3-117">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="69cd3-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="69cd3-118">ASP – Fallback – src</span><span class="sxs-lookup"><span data-stu-id="69cd3-118">asp-fallback-src</span></span>

<span data-ttu-id="69cd3-119">Adresa URL značky skriptu, na kterou se má přejít v případě, že primární z nich selhala</span><span class="sxs-lookup"><span data-stu-id="69cd3-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="69cd3-120">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="69cd3-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69cd3-121">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="69cd3-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
