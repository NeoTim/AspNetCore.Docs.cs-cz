---
title: Sdílení řadičů, zobrazení, Razor stránek a dalších prvků aplikace v ASP.NET Core
author: rick-anderson
description: Sdílení řadičů, zobrazení, Razor stránek a dalších prvků aplikace v ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/extensibility/app-parts
ms.openlocfilehash: cb1f8b045b8f2b143afc7895234733fbfb02cb07
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399748"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a>Sdílení řadičů, zobrazení, Razor stránek a dalších prvků aplikace

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Jak stáhnout](xref:index#how-to-download-a-sample))

*Část aplikace* je abstrakcí přes prostředky aplikace. Části aplikace umožňují ASP.NET Core zjišťovat řadiče, zobrazovat komponenty, pomáhat pomocníkům s tagy, Razor stránky, zdroje kompilace Razor a další. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>je součást aplikace. `AssemblyPart`Zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.

[Poskytovatelé funkcí](#fp) fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core. Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení. Můžete například chtít sdílet běžné funkce mezi více aplikacemi. Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor stránky, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích. Sdílení sestavení je preferované pro duplikování kódu ve více projektech.

ASP.NET Core aplikace načítají funkce z <xref:System.Web.WebPages.ApplicationPart> . <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Třída představuje část aplikace, která je zajištěna sestavením.

## <a name="load-aspnet-core-features"></a>Načíst ASP.NET Core funkce

Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> tříd a <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.). Sleduje, jaké <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> součásti aplikace a poskytovatelé funkcí jsou k dispozici. `ApplicationPartManager`je nakonfigurované v `Startup.ConfigureServices` :

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí `AssemblyPart` :

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Předchozí dva ukázky kódu načtou `SharedController` ze sestavení. `SharedController`Není v projektu aplikace. Viz stažení ukázkového [řešení WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .

### <a name="include-views"></a>Zahrnutí zobrazení

Použijte [ Razor knihovnu tříd](xref:razor-pages/ui-class) pro zahrnutí zobrazení do sestavení.

### <a name="prevent-loading-resources"></a>Zabránit načítání prostředků

Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění. Přidáním nebo odebráním členů <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekce můžete skrýt nebo zpřístupnit dostupné prostředky. Pořadí položek v `ApplicationParts` kolekci není důležité. Nakonfigurujte `ApplicationPartManager` před tím, než je použijete ke konfiguraci služeb v kontejneru. Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices` . `Remove`Chcete-li `ApplicationParts` Odebrat prostředek, zavolejte na kolekci.

`ApplicationPartManager`Obsahuje části pro:

* Sestavení aplikace a závislá sestavení.
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>Poskytovatelé funkcí

Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části. K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce ASP.NET Core:

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class`[RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Poskytovatelé funkcí dědí z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , kde `T` je typ funkce. Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí. Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu. Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.

### <a name="display-available-features"></a>Zobrazit dostupné funkce

Funkce, které jsou k dispozici pro aplikaci, mohou být vyhodnoceny požadavkem na `ApplicationPartManager` [vkládání závislostí](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Zjišťování v součástech aplikace

Chyby HTTP 404 nejsou běžné při vývoji s součástmi aplikace. Tyto chyby jsou obvykle způsobeny chybějícím základním požadavkem na zjištění částí aplikace. Pokud vaše aplikace vrátí chybu HTTP 404, ověřte, zda byly splněny následující požadavky:

* `applicationName`Nastavení musí být nastavené na kořenové sestavení používané pro zjišťování. Kořenové sestavení použité pro zjišťování je obvykle sestavení vstupního bodu.
* Kořenové sestavení musí mít odkaz na části používané pro zjišťování. Odkaz může být přímý nebo přenosný.
* Kořenové sestavení musí odkazovat na webovou sadu SDK. Rozhraní obsahuje logiku, která označí atributy do kořenového sestavení používaného pro zjišťování.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Jak stáhnout](xref:index#how-to-download-a-sample))

*Část aplikace* je abstrakcí přes prostředky aplikace. Části aplikace umožňují ASP.NET Core zjišťovat řadiče, zobrazovat komponenty, pomáhat pomocníkům s tagy, Razor stránky, zdroje kompilace Razor a další. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) je součást aplikace. `AssemblyPart`Zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.

*Poskytovatelé funkcí* fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core. Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení. Můžete například chtít sdílet běžné funkce mezi více aplikacemi. Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor stránky, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích. Sdílení sestavení je preferované pro duplikování kódu ve více projektech.

ASP.NET Core aplikace načítají funkce z <xref:System.Web.WebPages.ApplicationPart> . <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Třída představuje část aplikace, která je zajištěna sestavením.

## <a name="load-aspnet-core-features"></a>Načíst ASP.NET Core funkce

Pomocí `ApplicationPart` tříd a `AssemblyPart` můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.). Sleduje, jaké <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> součásti aplikace a poskytovatelé funkcí jsou k dispozici. `ApplicationPartManager`je nakonfigurované v `Startup.ConfigureServices` :

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí `AssemblyPart` :

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Předchozí dva ukázky kódu načtou `SharedController` ze sestavení. `SharedController`Není v projektu aplikace. Viz stažení ukázkového [řešení WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .

### <a name="include-views"></a>Zahrnutí zobrazení

Použijte [ Razor knihovnu tříd](xref:razor-pages/ui-class) pro zahrnutí zobrazení do sestavení.

### <a name="prevent-loading-resources"></a>Zabránit načítání prostředků

Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění. Přidáním nebo odebráním členů <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekce můžete skrýt nebo zpřístupnit dostupné prostředky. Pořadí položek v `ApplicationParts` kolekci není důležité. Nakonfigurujte `ApplicationPartManager` před tím, než je použijete ke konfiguraci služeb v kontejneru. Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices` . `Remove`Chcete-li `ApplicationParts` Odebrat prostředek, zavolejte na kolekci.

Následující kód používá <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> k odebrání `MyDependentLibrary` z aplikace:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager`Obsahuje části pro:

* Sestavení aplikace a závislá sestavení.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Poskytovatelé funkcí aplikace

Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části. K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce ASP.NET Core:

* [Kontrolery](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Pomocné rutiny značek](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Zobrazit součásti](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Poskytovatelé funkcí dědí z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , kde `T` je typ funkce. Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí. Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu. Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.

### <a name="display-available-features"></a>Zobrazit dostupné funkce

Funkce, které jsou k dispozici pro aplikaci, mohou být vyhodnoceny požadavkem na `ApplicationPartManager` [vkládání závislostí](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Zjišťování v součástech aplikace

Chyby HTTP 404 nejsou běžné při vývoji s součástmi aplikace. Tyto chyby jsou obvykle způsobeny chybějícím základním požadavkem na zjištění částí aplikace. Pokud vaše aplikace vrátí chybu HTTP 404, ověřte, zda byly splněny následující požadavky:

* `applicationName`Nastavení musí být nastavené na kořenové sestavení používané pro zjišťování. Kořenové sestavení použité pro zjišťování je obvykle sestavení vstupního bodu.
* Kořenové sestavení musí mít odkaz na části používané pro zjišťování. Odkaz může být přímý nebo přenosný.
* Kořenové sestavení musí odkazovat na webovou sadu SDK.
  * Rozhraní ASP.NET Core Framework má vlastní logiku sestavení, která označí atributy do kořenového sestavení používaného pro zjišťování.

::: moniker-end
