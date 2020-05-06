---
title: Rozšiřitelnost lokalizace
author: hishamco
description: Naučte se, jak roztáhnout rozhraní API lokalizace v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization-extensibility
ms.openlocfilehash: 152146992f1ea3cace14a2e702762af942a6c289
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774337"
---
# <a name="localization-extensibility"></a><span data-ttu-id="9ab40-103">Rozšiřitelnost lokalizace</span><span class="sxs-lookup"><span data-stu-id="9ab40-103">Localization Extensibility</span></span>

<span data-ttu-id="9ab40-104">Podle [Hisham bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="9ab40-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="9ab40-105">Tento článek:</span><span class="sxs-lookup"><span data-stu-id="9ab40-105">This article:</span></span>

* <span data-ttu-id="9ab40-106">Uvádí body rozšiřitelnosti na rozhraních API lokalizace.</span><span class="sxs-lookup"><span data-stu-id="9ab40-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="9ab40-107">Poskytuje pokyny, jak roztáhnout lokalizaci aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9ab40-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="9ab40-108">Rozšiřitelné body v rozhraních API lokalizace</span><span class="sxs-lookup"><span data-stu-id="9ab40-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="9ab40-109">Rozhraní API pro lokalizaci ASP.NET Core jsou sestavená tak, aby byla rozšiřitelná.</span><span class="sxs-lookup"><span data-stu-id="9ab40-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="9ab40-110">Rozšiřitelnost umožňuje vývojářům přizpůsobit lokalizaci podle jejich potřeb.</span><span class="sxs-lookup"><span data-stu-id="9ab40-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="9ab40-111">Například [OrchardCore](https://github.com/orchardCMS/OrchardCore/) má `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="9ab40-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="9ab40-112">`POStringLocalizer`podrobně popisuje použití [lokalizace přenosných objektů](xref:fundamentals/portable-object-localization) k `PO` používání souborů k ukládání prostředků lokalizace.</span><span class="sxs-lookup"><span data-stu-id="9ab40-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="9ab40-113">Tento článek obsahuje seznam dvou hlavních bodů rozšiřitelnosti, které poskytují rozhraní API pro lokalizaci:</span><span class="sxs-lookup"><span data-stu-id="9ab40-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="9ab40-114">Poskytovatelé jazykové verze lokalizace</span><span class="sxs-lookup"><span data-stu-id="9ab40-114">Localization Culture Providers</span></span>

<span data-ttu-id="9ab40-115">Rozhraní API pro ASP.NET Core lokalizace mají čtyři výchozí poskytovatele, kteří mohou určit aktuální jazykovou verzi zpracovávané žádosti:</span><span class="sxs-lookup"><span data-stu-id="9ab40-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="9ab40-116">Předchozí poskytovatelé jsou podrobněji popsáni v dokumentaci k [lokalizačnímu middlewaru](xref:fundamentals/localization) .</span><span class="sxs-lookup"><span data-stu-id="9ab40-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="9ab40-117">Pokud výchozí zprostředkovatelé nevyhovují vašim potřebám, sestavte vlastního zprostředkovatele pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9ab40-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="9ab40-118">Použití CustomRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="9ab40-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="9ab40-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>poskytuje vlastní <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> použití jednoduchého delegáta k určení aktuální jazykové verze lokalizace:</span><span class="sxs-lookup"><span data-stu-id="9ab40-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

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

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="9ab40-120">Použít nový implemetation z RequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="9ab40-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="9ab40-121">Je <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> možné vytvořit novou implementaci, která určuje informace o jazykové verzi žádosti z vlastního zdroje.</span><span class="sxs-lookup"><span data-stu-id="9ab40-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="9ab40-122">Vlastní zdroj může být například konfigurační soubor nebo databáze.</span><span class="sxs-lookup"><span data-stu-id="9ab40-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="9ab40-123">Následující příklad ukazuje `AppSettingsRequestCultureProvider`, který rozšiřuje na <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> k určení informací o jazykové verzi žádosti z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9ab40-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

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

## <a name="localization-resources"></a><span data-ttu-id="9ab40-124">Prostředky lokalizace</span><span class="sxs-lookup"><span data-stu-id="9ab40-124">Localization resources</span></span>

<span data-ttu-id="9ab40-125">ASP.NET Core lokalizace <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span><span class="sxs-lookup"><span data-stu-id="9ab40-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="9ab40-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>je implementací <xref:Microsoft.Extensions.Localization.IStringLocalizer> , která se používá `resx` k ukládání prostředků lokalizace.</span><span class="sxs-lookup"><span data-stu-id="9ab40-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="9ab40-127">Nejste omezeni používáním `resx` souborů.</span><span class="sxs-lookup"><span data-stu-id="9ab40-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="9ab40-128">Implementací `IStringLocalized`lze použít libovolný zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="9ab40-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="9ab40-129">Následující příklady projektů implementují <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span><span class="sxs-lookup"><span data-stu-id="9ab40-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="9ab40-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="9ab40-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="9ab40-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="9ab40-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="9ab40-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="9ab40-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
