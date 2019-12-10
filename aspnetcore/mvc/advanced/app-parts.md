---
title: Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core
author: rick-anderson
description: Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: a102511478c40ae64aada919fee7072c3027ddcd
ms.sourcegitcommit: 4e3edff24ba6e43a103fee1b126c9826241bb37b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74958992"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a>Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace

::: moniker range=">= aspnetcore-3.0"

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([stažení](xref:index#how-to-download-a-sample))

*Část aplikace* je abstrakcí přes prostředky aplikace. Části aplikace umožňují ASP.NET Core zjišťování řadičů, zobrazení komponent, pomocníků značek, Razor Pages, zdrojů kompilace Razor a dalších. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> je součást aplikace. `AssemblyPart` zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.

[Poskytovatelé funkcí](#fp) fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core. Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení. Můžete například chtít sdílet běžné funkce mezi více aplikacemi. Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor Pages, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích. Sdílení sestavení je preferované pro duplikování kódu ve více projektech.

ASP.NET Core aplikace načítají funkce z <xref:System.Web.WebPages.ApplicationPart>. Třída <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> představuje část aplikace, která je zajištěna sestavením.

## <a name="load-aspnet-core-features"></a>Načíst ASP.NET Core funkce

Pomocí tříd <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> a <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.). <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> sleduje součásti aplikace a poskytovatele funkcí, které jsou k dispozici. `ApplicationPartManager` je nakonfigurované v `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí `AssemblyPart`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Předchozí dva ukázky kódu načtou `SharedController` ze sestavení. `SharedController` není v projektu aplikace. Viz stažení ukázkového [řešení WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .

### <a name="include-views"></a>Zahrnutí zobrazení

Použijte [knihovnu tříd Razor](xref:razor-pages/ui-class) pro zahrnutí zobrazení do sestavení.

### <a name="prevent-loading-resources"></a>Zabránit načítání prostředků

Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění. Přidejte nebo odeberte členy kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> pro skrytí nebo zpřístupnění dostupných prostředků. Pořadí položek v kolekci `ApplicationParts` není důležité. Nakonfigurujte `ApplicationPartManager` před tím, než ji použijete ke konfiguraci služeb v kontejneru. Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices`. Chcete-li odebrat prostředek, zavolejte `Remove` v kolekci `ApplicationParts`.

`ApplicationPartManager` obsahuje části pro:

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
* `internal class` [RazorCompiledItemFeatureProvider](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Poskytovatelé funkcí dědí z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, kde `T` je typ funkce. Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí. Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu. Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.

### <a name="display-available-features"></a>Zobrazit dostupné funkce

Funkce, které jsou k dispozici pro aplikaci, mohou být vyčísleny vyžádáním `ApplicationPartManager` prostřednictvím [Injektáže závislosti](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[Ukázka ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace:

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

* Nastavení `applicationName` musí být nastavené na kořenové sestavení používané pro zjišťování. Kořenové sestavení použité pro zjišťování je obvykle sestavení vstupního bodu.
* Kořenové sestavení musí mít odkaz na části používané pro zjišťování. Odkaz může být přímý nebo přenosný.
* Kořenové sestavení musí odkazovat na webovou sadu SDK. Rozhraní obsahuje logiku, která označí atributy do kořenového sestavení používaného pro zjišťování.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([stažení](xref:index#how-to-download-a-sample))

*Část aplikace* je abstrakcí přes prostředky aplikace. Části aplikace umožňují ASP.NET Core zjišťování řadičů, zobrazení komponent, pomocníků značek, Razor Pages, zdrojů kompilace Razor a dalších. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) je součást aplikace. `AssemblyPart` zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.

*Poskytovatelé funkcí* fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core. Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení. Můžete například chtít sdílet běžné funkce mezi více aplikacemi. Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor Pages, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích. Sdílení sestavení je preferované pro duplikování kódu ve více projektech.

ASP.NET Core aplikace načítají funkce z <xref:System.Web.WebPages.ApplicationPart>. Třída <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> představuje část aplikace, která je zajištěna sestavením.

## <a name="load-aspnet-core-features"></a>Načíst ASP.NET Core funkce

Pomocí tříd `ApplicationPart` a `AssemblyPart` můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.). <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> sleduje součásti aplikace a poskytovatele funkcí, které jsou k dispozici. `ApplicationPartManager` je nakonfigurované v `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí `AssemblyPart`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Předchozí dva ukázky kódu načtou `SharedController` ze sestavení. `SharedController` není v projektu aplikace. Viz stažení ukázkového [řešení WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .

### <a name="include-views"></a>Zahrnutí zobrazení

Použijte [knihovnu tříd Razor](xref:razor-pages/ui-class) pro zahrnutí zobrazení do sestavení.

### <a name="prevent-loading-resources"></a>Zabránit načítání prostředků

Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění. Přidejte nebo odeberte členy kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> pro skrytí nebo zpřístupnění dostupných prostředků. Pořadí položek v kolekci `ApplicationParts` není důležité. Nakonfigurujte `ApplicationPartManager` před tím, než ji použijete ke konfiguraci služeb v kontejneru. Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices`. Chcete-li odebrat prostředek, zavolejte `Remove` v kolekci `ApplicationParts`.

Následující kód používá <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> k odebrání `MyDependentLibrary` z aplikace: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager` obsahuje části pro:

* Sestavení aplikace a závislá sestavení.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Poskytovatelé funkcí aplikace

Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části. K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce ASP.NET Core:

* [Kontrolery](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Pomocné rutiny značek](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Zobrazit součásti](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Poskytovatelé funkcí dědí z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, kde `T` je typ funkce. Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí. Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu. Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.

### <a name="display-available-features"></a>Zobrazit dostupné funkce

Funkce, které jsou k dispozici pro aplikaci, mohou být vyčísleny vyžádáním `ApplicationPartManager` prostřednictvím [Injektáže závislosti](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[Ukázka ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace:

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

* Nastavení `applicationName` musí být nastavené na kořenové sestavení používané pro zjišťování. Kořenové sestavení použité pro zjišťování je obvykle sestavení vstupního bodu.
* Kořenové sestavení musí mít odkaz na části používané pro zjišťování. Odkaz může být přímý nebo přenosný.
* Kořenové sestavení musí odkazovat na webovou sadu SDK.
  * Rozhraní ASP.NET Core Framework má vlastní logiku sestavení, která označí atributy do kořenového sestavení používaného pro zjišťování.

::: moniker-end
