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
# <a name="localization-extensibility"></a>Rozšiřitelnost lokalizace

Podle [Hisham Bin Ateya](https://github.com/hishamco)

Tento článek:

* Zobrazí seznam bodů rozšiřitelnosti v lokalizačních apich.
* Obsahuje pokyny, jak rozšířit ASP.NET lokalizaci aplikace Core.

## <a name="extensible-points-in-localization-apis"></a>Rozšiřitelné body v lokalizačních API

ASP.NET jádra lokalizační api jsou postaveny tak, aby rozšiřitelné. Rozšiřitelnost umožňuje vývojářům přizpůsobit lokalizaci podle jejich potřeb. Například [OrchardCore](https://github.com/orchardCMS/OrchardCore/) má `POStringLocalizer`. `POStringLocalizer`Popisuje podrobně pomocí [lokalizace přenosných objektů](xref:fundamentals/portable-object-localization) k použití `PO` souborů k ukládání prostředků lokalizace.

Tento článek uvádí dva hlavní body rozšiřitelnosti, které poskytují lokalizační prostředí API: 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Zprostředkovatelé jazykové verze lokalizace

ASP.NET jádrová lokalizační api mají čtyři výchozí zprostředkovatele, kteří mohou určit aktuální jazykovou verzi spuštěného požadavku:

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Předchozí zprostředkovatelé jsou podrobně popsány v [dokumentaci middlewaru lokalizace.](xref:fundamentals/localization) Pokud výchozí zprostředkovatelé nesplňují vaše potřeby, vytvořte vlastního zprostředkovatele pomocí jednoho z následujících přístupů:

### <a name="use-customrequestcultureprovider"></a>Použít zprostředkovatele CustomRequestCulture

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>poskytuje vlastní, <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> který používá jednoduchý delegát k určení aktuální jazykovou verzi lokalizace:

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

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>Použít novou implemetation RequestCultureProvider

Můžete vytvořit <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> novou implementaci, která určuje informace o jazykové verzi požadavku z vlastního zdroje. Vlastní zdroj může být například konfigurační soubor nebo databáze.

Následující příklad `AppSettingsRequestCultureProvider`ukazuje , který <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> rozšiřuje k určení informace o jazykové verzi požadavku z *appsettings.json*:

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

## <a name="localization-resources"></a>Prostředky lokalizace

ASP.NET core lokalizace poskytuje <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>je <xref:Microsoft.Extensions.Localization.IStringLocalizer> implementace, která `resx` se používá k ukládání prostředků lokalizace.

Nejste omezeni na `resx` používání souborů. Implementací `IStringLocalized`lze použít libovolný zdroj dat.

Následující příklad projektů <xref:Microsoft.Extensions.Localization.IStringLocalizer>implementovat : 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
