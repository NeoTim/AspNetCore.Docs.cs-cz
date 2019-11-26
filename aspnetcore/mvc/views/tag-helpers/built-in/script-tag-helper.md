---
title: Pomocník značek Script v ASP.NET Core
author: rick-anderson
ms.author: riande
description: Seznamte se s atributy pomocníka značky skriptu ASP.NET Core a role, které každý atribut hraje při rozšiřování chování značky skriptu HTML.
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: c3d9148bd62dcc045873cc3a72884ae458349d70
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317124"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="b9538-103">Pomocník značek Script v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9538-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="b9538-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b9538-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b9538-105">[Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) vygeneruje odkaz na primární soubor nebo soubor skriptu.</span><span class="sxs-lookup"><span data-stu-id="b9538-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="b9538-106">Obvykle je soubor primárního skriptu na [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="b9538-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="b9538-107">Pomocná značka skriptu umožňuje zadat síť CDN pro soubor skriptu a zálohu, pokud CDN není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="b9538-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="b9538-108">Pomocník značek Script poskytuje výkonnou výhodu sítě CDN s odolností místního hostování.</span><span class="sxs-lookup"><span data-stu-id="b9538-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="b9538-109">Následující kód Razor ukazuje `script` element s Fallback:</span><span class="sxs-lookup"><span data-stu-id="b9538-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```HTML
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="b9538-110">Běžně používané pomocné atributy značek skriptů</span><span class="sxs-lookup"><span data-stu-id="b9538-110">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="b9538-111">Viz [Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) pro všechny pomocné atributy, vlastnosti a metody značky skriptu.</span><span class="sxs-lookup"><span data-stu-id="b9538-111">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="b9538-112">ASP – Fallback-test</span><span class="sxs-lookup"><span data-stu-id="b9538-112">asp-fallback-test</span></span>

<span data-ttu-id="b9538-113">Metoda skriptu definovaná v primárním skriptu pro použití pro záložní test.</span><span class="sxs-lookup"><span data-stu-id="b9538-113">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="b9538-114">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="b9538-114">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="b9538-115">ASP – Fallback – src</span><span class="sxs-lookup"><span data-stu-id="b9538-115">asp-fallback-src</span></span>

<span data-ttu-id="b9538-116">Adresa URL značky skriptu, na kterou se má přejít v případě, že primární z nich selhala</span><span class="sxs-lookup"><span data-stu-id="b9538-116">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="b9538-117">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="b9538-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9538-118">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="b9538-118">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
