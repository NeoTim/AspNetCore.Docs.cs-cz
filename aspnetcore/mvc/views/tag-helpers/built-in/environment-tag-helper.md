---
title: Pomocná značka prostředí v ASP.NET Core
author: pkellner
description: ASP.NET Core pomocná podpora značek prostředí, včetně všech vlastností
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 23ddad23214d3e1a66415fc8706c30de838357fa
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407431"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="3ff2f-103">Pomocná značka prostředí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ff2f-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="3ff2f-104">Od [Petra Kellner](https://peterkellner.net) a [Hisham bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="3ff2f-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="3ff2f-105">Pomocník pro tag prostředí podmíněně vykreslí svůj obsah na základě aktuálního [hostitelského prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="3ff2f-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="3ff2f-106">Jediný atribut pomocníka značky prostředí `names` je čárkami oddělený seznam názvů prostředí.</span><span class="sxs-lookup"><span data-stu-id="3ff2f-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="3ff2f-107">Pokud se některý z poskytnutých názvů prostředí shoduje s aktuálním prostředím, zobrazí se obsah přiložený k obsahu.</span><span class="sxs-lookup"><span data-stu-id="3ff2f-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="3ff2f-108">Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="3ff2f-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="3ff2f-109">Pomocné atributy značek prostředí</span><span class="sxs-lookup"><span data-stu-id="3ff2f-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="3ff2f-110">názvy</span><span class="sxs-lookup"><span data-stu-id="3ff2f-110">names</span></span>

<span data-ttu-id="3ff2f-111">`names`přijímá jeden název hostitelského prostředí nebo seznam názvů hostitelských prostředí oddělených čárkami, které aktivují vykreslování vloženého obsahu.</span><span class="sxs-lookup"><span data-stu-id="3ff2f-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="3ff2f-112">Hodnoty prostředí jsou porovnány s aktuální hodnotou vrácenou funkcí [IHostingEnvironment. Environment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="3ff2f-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="3ff2f-113">Porovnání ignoruje velikost písmen.</span><span class="sxs-lookup"><span data-stu-id="3ff2f-113">The comparison ignores case.</span></span>

<span data-ttu-id="3ff2f-114">V následujícím příkladu je použita pomocná pomůcka značek prostředí.</span><span class="sxs-lookup"><span data-stu-id="3ff2f-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="3ff2f-115">Obsah se vykreslí, pokud je hostující prostředí pracovní nebo produkční:</span><span class="sxs-lookup"><span data-stu-id="3ff2f-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="3ff2f-116">zahrnutí a vyloučení atributů</span><span class="sxs-lookup"><span data-stu-id="3ff2f-116">include and exclude attributes</span></span>

<span data-ttu-id="3ff2f-117">`include`& `exclude` ovládací prvek atributy vykreslovat vložený obsah na základě zahrnutých nebo vyloučených názvů hostitelských prostředí.</span><span class="sxs-lookup"><span data-stu-id="3ff2f-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="3ff2f-118">include</span><span class="sxs-lookup"><span data-stu-id="3ff2f-118">include</span></span>

<span data-ttu-id="3ff2f-119">`include`Vlastnost vykazuje podobné chování `names` atributu.</span><span class="sxs-lookup"><span data-stu-id="3ff2f-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="3ff2f-120">Prostředí uvedené v `include` hodnotě atributu se musí shodovat s hostitelským prostředím aplikace ([IHostingEnvironment. Environment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) pro vykreslení obsahu `<environment>` značky.</span><span class="sxs-lookup"><span data-stu-id="3ff2f-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="3ff2f-121">vyloučení</span><span class="sxs-lookup"><span data-stu-id="3ff2f-121">exclude</span></span>

<span data-ttu-id="3ff2f-122">Na rozdíl od `include` atributu je obsah značky vykreslen v případě, že se `<environment>` hostitelské prostředí neshoduje s prostředím uvedeným v `exclude` hodnotě atributu.</span><span class="sxs-lookup"><span data-stu-id="3ff2f-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3ff2f-123">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3ff2f-123">Additional resources</span></span>

* <xref:fundamentals/environments>
