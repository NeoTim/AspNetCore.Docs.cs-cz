---
title: Rozšiřitelnost lokalizace
author: hishamco
description: Naučte se, jak roztáhnout rozhraní API lokalizace v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization-extensibility
ms.openlocfilehash: 2e1041ed4cce3c3919d75ff47e2bc24fc446e9c3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627608"
---
# <a name="localization-extensibility"></a>Rozšiřitelnost lokalizace

Podle [Hisham bin Ateya](https://github.com/hishamco)

Tento článek:

* Uvádí body rozšiřitelnosti na rozhraních API lokalizace.
* Poskytuje pokyny, jak roztáhnout lokalizaci aplikace ASP.NET Core.

## <a name="extensible-points-in-localization-apis"></a>Rozšiřitelné body v rozhraních API lokalizace

Rozhraní API pro lokalizaci ASP.NET Core jsou sestavená tak, aby byla rozšiřitelná. Rozšiřitelnost umožňuje vývojářům přizpůsobit lokalizaci podle jejich potřeb. Například [OrchardCore](https://github.com/orchardCMS/OrchardCore/) má `POStringLocalizer` . `POStringLocalizer` podrobně popisuje použití [lokalizace přenosných objektů](xref:fundamentals/portable-object-localization) k používání `PO` souborů k ukládání prostředků lokalizace.

Tento článek obsahuje seznam dvou hlavních bodů rozšiřitelnosti, které poskytují rozhraní API pro lokalizaci: 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Poskytovatelé jazykové verze lokalizace

Rozhraní API pro ASP.NET Core lokalizace mají čtyři výchozí poskytovatele, kteří mohou určit aktuální jazykovou verzi zpracovávané žádosti:

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Předchozí poskytovatelé jsou podrobněji popsáni v dokumentaci k [lokalizačnímu middlewaru](xref:fundamentals/localization) . Pokud výchozí zprostředkovatelé nevyhovují vašim potřebám, sestavte vlastního zprostředkovatele pomocí jednoho z následujících přístupů:

### <a name="use-customrequestcultureprovider"></a>Použití CustomRequestCultureProvider

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> poskytuje vlastní <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> použití jednoduchého delegáta k určení aktuální jazykové verze lokalizace:

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

    var requestCulture = new ProviderCultureResult(currentCulture);
    
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

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>Použít nový implemetation z RequestCultureProvider

Je <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> možné vytvořit novou implementaci, která určuje informace o jazykové verzi žádosti z vlastního zdroje. Vlastní zdroj může být například konfigurační soubor nebo databáze.

Následující příklad ukazuje `AppSettingsRequestCultureProvider` , který rozšiřuje na <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> k určení informací o jazykové verzi žádosti z *appsettings.jsna*:

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

ASP.NET Core lokalizace <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> . <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> je implementací <xref:Microsoft.Extensions.Localization.IStringLocalizer> , která se používá `resx` k ukládání prostředků lokalizace.

Nejste omezeni používáním `resx` souborů. Implementací `IStringLocalized` lze použít libovolný zdroj dat.

Následující příklady projektů implementují <xref:Microsoft.Extensions.Localization.IStringLocalizer> : 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
