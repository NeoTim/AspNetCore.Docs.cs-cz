---
title: Poradce při potížích s lokalizací jádra ASP.NET
author: hishamco
description: Přečtěte si, jak diagnostikovat problémy s lokalizací v aplikacích ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660375"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Poradce při potížích s lokalizací jádra ASP.NET

Podle [Hisham Bin Ateya](https://github.com/hishamco)

Tento článek obsahuje pokyny, jak diagnostikovat ASP.NET problémy s lokalizací aplikace Core.

## <a name="localization-configuration-issues"></a>Problémy s konfigurací lokalizace

**Pořadí middlewaru lokalizace**  
Aplikace nemusí lokalizovat, protože middleware lokalizace není objednané podle očekávání.

Chcete-li tento problém vyřešit, ujistěte se, že middleware lokalizace je registrovánpřed MVC middleware. V opačném případě není použit middleware lokalizace.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Cesta prostředků lokalizace nebyla nalezena.**

**Podporované jazykové verze v RequestCultureProvider neodpovídají registrované jednou**  

## <a name="resource-file-naming-issues"></a>Problémy s pojmenováním souborů prostředků

ASP.NET Core má předdefinovaná pravidla a pokyny pro názvy souborů lokalizačních prostředků, které jsou podrobně popsány [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

## <a name="missing-resources"></a>Chybějící zdroje

Mezi běžné příčiny nenalezených prostředků patří:

- Názvy prostředků jsou chybně `resx` napsané v souboru nebo v požadavku lokalizačního systému.
- Prostředek chybí pro `resx` některé jazyky, ale existuje v jiných jazycích.
- Pokud máte stále potíže, zkontrolujte zprávy protokolu lokalizace (které jsou na `Debug` úrovni protokolu) další podrobnosti o chybějící prostředky.

_**Tip:** Při `CookieRequestCultureProvider`použití ověřte, zda se jednoduché uvozovky nepoužívají s jazyky uvnitř hodnoty souboru cookie lokalizace. Například `c='en-UK'|uic='en-US'` je neplatná hodnota `c=en-UK|uic=en-US` cookie, zatímco je platná._

## <a name="resources--class-libraries-issues"></a>Zdroje & problémy s knihovnami tříd

ASP.NET Core ve výchozím nastavení poskytuje způsob, jak knihovnám tříd umožnit, aby nalezly své soubory prostředků prostřednictvím [resourcelocationattribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).

Mezi běžné problémy s knihovnami tříd patří:
- Chybějící `ResourceLocationAttribute` v knihovně tříd `ResourceManagerStringLocalizerFactory` zabrání zjišťování prostředků.
- Pojmenování souboru prostředků. Další informace naleznete v části [Problémy s pojmenováním souborů prostředků.](#resource-file-naming-issues)
- Změna kořenového oboru názvů knihovny tříd. Další informace naleznete v části [Problémy kořenového oboru názvů.](#root-namespace-issues)

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider nefunguje podle očekávání

Třída `RequestLocalizationOptions` má tři výchozí zprostředkovatele:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) umožňuje přizpůsobit, jak je ve vaší aplikaci poskytována jazyková verze lokalizace. Používá `CustomRequestCultureProvider` se v případě, že výchozí zprostředkovatelé nesplňují vaše požadavky.

- Běžným důvodem, proč vlastní zprostředkovatel nefunguje správně, je `RequestCultureProviders` to, že není prvním poskytovatelem v seznamu. Řešení tohoto problému:

- Vložte vlastního zprostředkovatele na `RequestCultureProviders` pozici 0 v seznamu takto:

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

- Pomocí `AddInitialRequestCultureProvider` metody rozšíření nastavte vlastního zprostředkovatele jako počátečního zprostředkovatele.

## <a name="root-namespace-issues"></a>Problémy s kořenovým oborem názvů

Pokud kořenový obor názvů sestavení se liší od názvu sestavení, lokalizace nefunguje ve výchozím nastavení. Chcete-li se tomuto problému vyhnout, použijte [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), který je podrobně popsán [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)

> [!WARNING]
> Tato situace může nastat, pokud název projektu není platný identifikátor .NET. Například `my-project-name.csproj` bude používat kořenový obor `my_project_name` `my-project-name` názvů a název sestavení vedoucí k této chybě. 

## <a name="resources--build-action"></a>Zdroje & build akce

Pokud používáte soubory prostředků pro lokalizaci, je důležité, aby měly odpovídající akci sestavení. Měly by být **vložené zdroje**, jinak `ResourceStringLocalizer` není schopen najít tyto prostředky.
