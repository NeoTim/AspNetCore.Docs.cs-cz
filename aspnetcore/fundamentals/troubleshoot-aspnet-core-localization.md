---
title: Řešení potíží s ASP.NET Core lokalizací
author: hishamco
description: Naučte se diagnostikovat problémy s lokalizací v ASP.NET Corech aplikacích.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660375"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Řešení potíží s ASP.NET Core lokalizací

Podle [Hisham bin Ateya](https://github.com/hishamco)

Tento článek poskytuje pokyny, jak diagnostikovat problémy s lokalizací ASP.NET Core aplikací.

## <a name="localization-configuration-issues"></a>Problémy s konfigurací lokalizace

**Lokalizace pořadí middlewaru**  
Aplikace nemusí být lokalizována, protože middleware lokalizace není uspořádána podle očekávání.

Chcete-li tento problém vyřešit, zajistěte, aby middleware lokalizace byl zaregistrován před middlewarem MVC. V opačném případě se middleware lokalizace nepoužije.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Cesta k prostředkům lokalizace nebyla nalezena.**

**Podporované jazykové verze v RequestCultureProvider se neshodují s registrací.**  

## <a name="resource-file-naming-issues"></a>Problémy s pojmenovávání souborů prostředků

ASP.NET Core má předdefinovaná pravidla a pokyny pro pojmenování souborů lokalizačních prostředků, které jsou [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)podrobně popsané.

## <a name="missing-resources"></a>Chybějící prostředky

Mezi běžné příčiny nenalezených prostředků patří:

- V souboru `resx` nebo v žádosti lokalizátora jsou nesprávně napsané názvy prostředků.
- Prostředek chybí v `resx` pro některé jazyky, ale existuje v ostatních jazycích.
- Pokud pořád máte problémy, podívejte se na zprávy protokolu lokalizace (které jsou na úrovni protokolu `Debug`), kde najdete další podrobnosti o chybějících prostředcích.

_**Pomocný parametr:** Při použití `CookieRequestCultureProvider`ověřte, že jednoduché uvozovky nejsou použity s kulturami uvnitř hodnoty souboru cookie lokalizace. Například `c='en-UK'|uic='en-US'` je neplatná hodnota souboru cookie, zatímco `c=en-UK|uic=en-US` je platná._

## <a name="resources--class-libraries-issues"></a>Zdroje & problémy knihoven tříd

ASP.NET Core ve výchozím nastavení poskytuje způsob, jak umožníte knihovnám tříd najít své soubory prostředků prostřednictvím [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).

Mezi běžné problémy s knihovnami tříd patří:
- Chybějící `ResourceLocationAttribute` v knihovně tříd zabrání `ResourceManagerStringLocalizerFactory`mu zjišťování prostředků.
- Pojmenovávání zdrojového souboru. Další informace najdete v části [problémy s pojmenovávání souborů prostředků](#resource-file-naming-issues) .
- Změna kořenového oboru názvů knihovny tříd. Další informace najdete v části [problémy kořenového oboru názvů](#root-namespace-issues) .

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider nefunguje podle očekávání

Třída `RequestLocalizationOptions` má tři výchozí zprostředkovatele:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) umožňuje přizpůsobit, jak je v aplikaci k dispozici kultura lokalizace. `CustomRequestCultureProvider` se používá v případě, že výchozí zprostředkovatelé nesplňují vaše požadavky.

- Běžný důvod, který vlastní zprostředkovatel nepracuje správně, je, že se nejedná o prvního poskytovatele v seznamu `RequestCultureProviders`. Řešení tohoto problému:

- Vložte vlastního zprostředkovatele na pozici 0 v seznamu `RequestCultureProviders` následujícím způsobem:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- Pomocí `AddInitialRequestCultureProvider` metody rozšíření nastavte vlastního zprostředkovatele jako počátečního poskytovatele.

## <a name="root-namespace-issues"></a>Problémy s kořenovým oborem názvů

Pokud se kořenový obor názvů sestavení liší od názvu sestavení, lokalizace ve výchozím nastavení nefunguje. Chcete-li se tomuto problému vyhnout, použijte [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), který je [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) podrobně popsán.

> [!WARNING]
> K tomu může dojít, když název projektu není platným identifikátorem .NET. Instance `my-project-name.csproj` například použije kořenový obor názvů `my_project_name` a název sestavení `my-project-name` což vede k této chybě. 

## <a name="resources--build-action"></a>Prostředky & akci sestavení

Pokud používáte soubory prostředků k lokalizaci, je důležité, aby měly odpovídající akci sestavení. Měly by být **vložené prostředky**, jinak `ResourceStringLocalizer` není schopné tyto prostředky najít.
