---
title: Rozšiřitelnost lokalizace
author: hishamco
description: Přečtěte si, jak rozšířit lokalizační api v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
uid: fundamentals/localization-extensibility
ms.openlocfilehash: dfa2efe78b2e1e118e6b3f09bfc41f3330e1d721
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662097"
---
# <a name="localization-extensibility"></a><span data-ttu-id="64d23-103">Rozšiřitelnost lokalizace</span><span class="sxs-lookup"><span data-stu-id="64d23-103">Localization Extensibility</span></span>

<span data-ttu-id="64d23-104">Podle [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="64d23-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="64d23-105">Tento článek:</span><span class="sxs-lookup"><span data-stu-id="64d23-105">This article:</span></span>

* <span data-ttu-id="64d23-106">Zobrazí seznam bodů rozšiřitelnosti v lokalizačních apich.</span><span class="sxs-lookup"><span data-stu-id="64d23-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="64d23-107">Obsahuje pokyny, jak rozšířit ASP.NET lokalizaci aplikace Core.</span><span class="sxs-lookup"><span data-stu-id="64d23-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="64d23-108">Rozšiřitelné body v lokalizačních API</span><span class="sxs-lookup"><span data-stu-id="64d23-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="64d23-109">ASP.NET jádra lokalizační api jsou postaveny tak, aby rozšiřitelné.</span><span class="sxs-lookup"><span data-stu-id="64d23-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="64d23-110">Rozšiřitelnost umožňuje vývojářům přizpůsobit lokalizaci podle jejich potřeb.</span><span class="sxs-lookup"><span data-stu-id="64d23-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="64d23-111">Například [OrchardCore](https://github.com/orchardCMS/OrchardCore/) má `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="64d23-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="64d23-112">`POStringLocalizer`Popisuje podrobně pomocí [lokalizace přenosných objektů](xref:fundamentals/portable-object-localization) k použití `PO` souborů k ukládání prostředků lokalizace.</span><span class="sxs-lookup"><span data-stu-id="64d23-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="64d23-113">Tento článek uvádí dva hlavní body rozšiřitelnosti, které poskytují lokalizační prostředí API:</span><span class="sxs-lookup"><span data-stu-id="64d23-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="64d23-114">Zprostředkovatelé jazykové verze lokalizace</span><span class="sxs-lookup"><span data-stu-id="64d23-114">Localization Culture Providers</span></span>

<span data-ttu-id="64d23-115">ASP.NET jádrová lokalizační api mají čtyři výchozí zprostředkovatele, kteří mohou určit aktuální jazykovou verzi spuštěného požadavku:</span><span class="sxs-lookup"><span data-stu-id="64d23-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="64d23-116">Předchozí zprostředkovatelé jsou podrobně popsány v [dokumentaci middlewaru lokalizace.](xref:fundamentals/localization)</span><span class="sxs-lookup"><span data-stu-id="64d23-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="64d23-117">Pokud výchozí zprostředkovatelé nesplňují vaše potřeby, vytvořte vlastního zprostředkovatele pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="64d23-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="64d23-118">Použít zprostředkovatele CustomRequestCulture</span><span class="sxs-lookup"><span data-stu-id="64d23-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="64d23-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>poskytuje vlastní, <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> který používá jednoduchý delegát k určení aktuální jazykovou verzi lokalizace:</span><span class="sxs-lookup"><span data-stu-id="64d23-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="64d23-120">Použít novou implemetation RequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="64d23-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="64d23-121">Můžete vytvořit <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> novou implementaci, která určuje informace o jazykové verzi požadavku z vlastního zdroje.</span><span class="sxs-lookup"><span data-stu-id="64d23-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="64d23-122">Vlastní zdroj může být například konfigurační soubor nebo databáze.</span><span class="sxs-lookup"><span data-stu-id="64d23-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="64d23-123">Následující příklad `AppSettingsRequestCultureProvider`ukazuje , který <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> rozšiřuje k určení informace o jazykové verzi požadavku z *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="64d23-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a><span data-ttu-id="64d23-124">Prostředky lokalizace</span><span class="sxs-lookup"><span data-stu-id="64d23-124">Localization resources</span></span>

<span data-ttu-id="64d23-125">ASP.NET core lokalizace poskytuje <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span><span class="sxs-lookup"><span data-stu-id="64d23-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="64d23-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>je <xref:Microsoft.Extensions.Localization.IStringLocalizer> implementace, která `resx` se používá k ukládání prostředků lokalizace.</span><span class="sxs-lookup"><span data-stu-id="64d23-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="64d23-127">Nejste omezeni na `resx` používání souborů.</span><span class="sxs-lookup"><span data-stu-id="64d23-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="64d23-128">Implementací `IStringLocalized`lze použít libovolný zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="64d23-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="64d23-129">Následující příklad projektů <xref:Microsoft.Extensions.Localization.IStringLocalizer>implementovat :</span><span class="sxs-lookup"><span data-stu-id="64d23-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="64d23-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="64d23-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="64d23-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="64d23-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="64d23-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="64d23-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
