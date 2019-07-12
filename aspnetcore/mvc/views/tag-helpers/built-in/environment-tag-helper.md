---
title: Pomocná rutina značky prostředí v ASP.NET Core
author: pkellner
description: Pomocná rutina značky prostředí ASP.NET Core definované, včetně všech vlastností
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: e2e038fe69da696b67f7aef61795e23dc8512fdf
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856134"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="64579-103">Pomocná rutina značky prostředí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64579-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="64579-104">Podle [Peter Kellner](https://peterkellner.net), [Hisham Bin Ateya](https://twitter.com/hishambinateya), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="64579-104">By [Peter Kellner](https://peterkellner.net), [Hisham Bin Ateya](https://twitter.com/hishambinateya), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="64579-105">Pomocná rutina značky prostředí podmíněně vykreslí obsah uzavřené na základě aktuálního [hostitelské prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="64579-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="64579-106">Pomocná rutina značky prostředí jeden atribut, `names`, je čárkou oddělený seznam názvů prostředí.</span><span class="sxs-lookup"><span data-stu-id="64579-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="64579-107">Pokud se názvy zadané prostředí shodují v aktuálním prostředí, se uzavřené obsah zobrazí.</span><span class="sxs-lookup"><span data-stu-id="64579-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="64579-108">Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="64579-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="64579-109">Atributy pomocné rutiny značky prostředí</span><span class="sxs-lookup"><span data-stu-id="64579-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="64579-110">názvy</span><span class="sxs-lookup"><span data-stu-id="64579-110">names</span></span>

<span data-ttu-id="64579-111">`names` Přijímá jediný název hostitelské prostředí nebo čárkami oddělený seznam hostování názvy prostředí, které aktivují vykreslování uzavřené obsah.</span><span class="sxs-lookup"><span data-stu-id="64579-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="64579-112">Hodnoty prostředí jsou ve srovnání s aktuální hodnotu vrácenou příkazem [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="64579-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="64579-113">Porovnání ignoruje velikost písmen.</span><span class="sxs-lookup"><span data-stu-id="64579-113">The comparison ignores case.</span></span>

<span data-ttu-id="64579-114">Následující příklad používá pomocná rutina značky prostředí.</span><span class="sxs-lookup"><span data-stu-id="64579-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="64579-115">Pokud hostitelského prostředí je pracovní nebo produkční prostředí, je zobrazení obsahu:</span><span class="sxs-lookup"><span data-stu-id="64579-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="64579-116">zahrnutí a vyloučení atributy</span><span class="sxs-lookup"><span data-stu-id="64579-116">include and exclude attributes</span></span>

<span data-ttu-id="64579-117">`include` & `exclude` atributy ovládacího prvku vykreslování uzavřené obsahu na základě zahrnuté ani vyloučené hostování prostředí názvů.</span><span class="sxs-lookup"><span data-stu-id="64579-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="64579-118">include</span><span class="sxs-lookup"><span data-stu-id="64579-118">include</span></span>

<span data-ttu-id="64579-119">`include` Vlastnost vykazuje podobné chování jako `names` atribut.</span><span class="sxs-lookup"><span data-stu-id="64579-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="64579-120">Uvedené v prostředí `include` hodnota atributu musí odpovídat aplikace hostitelské prostředí ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) k vykreslení obsahu `<environment>` značky.</span><span class="sxs-lookup"><span data-stu-id="64579-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="64579-121">exclude</span><span class="sxs-lookup"><span data-stu-id="64579-121">exclude</span></span>

<span data-ttu-id="64579-122">Rozdíl od `include` atribut obsah `<environment>` je vykreslen při hostování prostředí neodpovídá uvedené v prostředí `exclude` hodnotu atributu.</span><span class="sxs-lookup"><span data-stu-id="64579-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="64579-123">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="64579-123">Additional resources</span></span>

* <xref:fundamentals/environments>
