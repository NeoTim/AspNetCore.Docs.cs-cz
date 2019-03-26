---
title: Řešení potíží s ASP.NET Core lokalizace
author: hishamco
description: Zjistěte, jak diagnostikovat problémy s lokalizace v aplikacích ASP.NET Core.
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 73405f539c89d79096e7b536407cd9730679d478
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58489006"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Řešení potíží s ASP.NET Core lokalizace

Podle [Ateya Hisham Bin](https://github.com/hishamco)

Tento článek obsahuje pokyny o tom, jak diagnostikovat problémy lokalizace aplikace ASP.NET Core.

## <a name="localization-configuration-issues"></a>Konfigurace lokalizace

**Lokalizace middleware pořadí**  
Aplikace nemusí lokalizují, protože middleware lokalizace není seřazené podle očekávání.

Chcete-li vyřešit tento problém, zkontrolujte, jestli že tento middleware lokalizace zaregistrován před MVC middlewaru. V opačném případě se nepoužije middleware lokalizace.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Lokalizace prostředků cesta nebyla nalezena.**

**Podporované jazykové verze v RequestCultureProvider se neshodují s zaregistrovat**  

## <a name="resource-file-naming-issues"></a>Problémy pojmenování souboru prostředků

ASP.NET Core má předdefinovaná pravidla a pokyny pro lokalizaci prostředky souboru vytváření názvů, které jsou podrobně popsány v [tady](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

## <a name="missing-resources"></a>Chybějící prostředky

Mezi běžné příčiny prostředků nebyl nalezen patří:

- Názvy prostředků jsou chybně buď `resx` souboru nebo lokalizátora žádost.
- Chybí prostředek `resx` pro některé jazyky, ale existuje v jiné.
- Pokud stále máte potíže, zkontrolujte zprávy protokolu lokalizace (které jsou v `Debug` úrovně protokolování) pro další podrobnosti o chybějících prostředků.

_**Tip:** Při použití `CookieRequestCultureProvider`, ověřte, jednoduché uvozovky nejsou použity s jazykovými verzemi uvnitř hodnoty souboru cookie lokalizace. Například `c='en-UK'|uic='en-US'` je hodnota neplatný soubor cookie, zatímco `c=en-UK|uic=en-US` je platný._

## <a name="resources--class-libraries-issues"></a>Problémy s prostředky a knihovny tříd

ASP.NET Core ve výchozím nastavení poskytuje způsob, jak povolit knihovny tříd k vyhledání svých souborů prostředků prostřednictvím [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).

Běžné problémy s knihovny tříd patří:
- Chybí `ResourceLocationAttribute` ve třídě knihovny zabrání `ResourceManagerStringLocalizerFactory` od zjišťování prostředků.
- Pojmenovávání souborů prostředků. Další informace najdete v tématu [souboru prostředků pojmenování problémy](#resource-file-naming-issues) oddílu.
- Změna kořenového oboru názvů knihovny tříd. Další informace najdete v tématu [vystavuje kořenové Namespace](#root-namespace-issues) oddílu.

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider nebude fungovat podle očekávání

`RequestLocalizationOptions` Třída má tři výchozí poskytovatele:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) vám umožní přizpůsobit, jak se ve vaší aplikaci poskytuje jazykovou verzi lokalizace. `CustomRequestCultureProvider` Se používá, když výchozí poskytovatele nesplňují vaše požadavky.

- Vlastní zprostředkovatel nefungují správně běžným důvodem je, že není prvním poskytovatelem v `RequestCultureProviders` seznamu. K vyřešení tohoto problému:

- Vložení vlastního zprostředkovatele na pozici 0 v `RequestCultureProviders` seznamu následujícím způsobem:

```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```

- Použití `AddInitialRequestCultureProvider` metodu rozšíření k nastavení vlastního zprostředkovatele jako počáteční zprostředkovatele.

## <a name="root-namespace-issues"></a>Hlavní problémy Namespace

Pokud kořenový obor názvů sestavení se liší od názvu sestavení, lokalizace nefunguje ve výchozím nastavení. Abyste tento problém, použijte [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), který je podrobně popsaný [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)

## <a name="resources--build-action"></a>Prostředky a akci sestavení

Pokud používáte soubory prostředků pro lokalizaci, je důležité, ke kterým mají příslušná sestavení akci. By měl být **integrovaný prostředek**, jinak `ResourceStringLocalizer` není schopen najít tyto prostředky.
