---
title: Části aplikace v ASP.NET Core
author: rick-anderson
description: Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core
ms.author: riande
ms.date: 05/14/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: ad0372f25377115e6fc7c8ea42db75de56b3e6d2
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187012"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a>Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core
=======

<!-- DO NOT MAKE CHANGES BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12376 Merges -->

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([stažení](xref:index#how-to-download-a-sample))

*Část aplikace* je abstrakcí přes prostředky aplikace. Části aplikace umožňují ASP.NET Core zjišťování řadičů, zobrazení komponent, pomocníků značek, Razor Pages, zdrojů kompilace Razor a dalších. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) je součást aplikace. `AssemblyPart`Zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.

*Poskytovatelé funkcí* fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core. Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení. Můžete například chtít sdílet běžné funkce mezi více aplikacemi. Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor Pages, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích. Sdílení sestavení je preferované pro duplikování kódu ve více projektech.

ASP.NET Core aplikace načítají funkce <xref:System.Web.WebPages.ApplicationPart>z. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> Třída představuje část aplikace, která je zajištěna sestavením.

## <a name="load-aspnet-core-features"></a>Načíst ASP.NET Core funkce

Pomocí tříd `AssemblyPart` a můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.). `ApplicationPart` <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> Sleduje, jaké součásti aplikace a poskytovatelé funkcí jsou k dispozici. `ApplicationPartManager`je nakonfigurované `Startup.ConfigureServices`v:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí: `AssemblyPart`

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Předchozí dva ukázky kódu načtou `SharedController` ze sestavení. `SharedController` Není v projektu aplikace. Viz stažení ukázkového [řešení WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .

### <a name="include-views"></a>Zahrnutí zobrazení

Zahrnutí zobrazení do sestavení:

* Do souboru sdíleného projektu přidejte následující kód:

  ```csproj
    <ItemGroup>
      <EmbeddedResource Include = "Views\**\*.cshtml" />
    </ ItemGroup >
  ```

* <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> Přidejte<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>do:

[!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>Zabránit načítání prostředků

Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění. Přidáním nebo odebráním členů <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekce můžete skrýt nebo zpřístupnit dostupné prostředky. Pořadí položek v `ApplicationParts` kolekci není důležité. `ApplicationPartManager` Nakonfigurujte před tím, než je použijete ke konfiguraci služeb v kontejneru. Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices`. Chcete `Remove` -li `ApplicationParts` odebrat prostředek, zavolejte na kolekci.

Následující kód používá <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> k odebrání `MyDependentLibrary` z aplikace:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager` Obsahuje části pro:

* Sestavení aplikace a závislá sestavení.
* `Microsoft.AspNetCore.Mvc.TagHelpers`
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Poskytovatelé funkcí aplikace

Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části. K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce ASP.NET Core:

* [Kontrolery](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Pomocné rutiny značek](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Zobrazit součásti](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Poskytovatelé funkcí dědí <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>z, `T` kde je typ funkce. Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí. Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu. Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.

### <a name="generic-controller-feature"></a>Funkce obecného kontroleru

ASP.NET Core ignorují [Obecné řadiče](/dotnet/csharp/programming-guide/generics/generic-classes). Obecný kontroler má parametr typu (například `MyController<T>`). Následující příklad přidá instance obecného kontroleru pro zadaný seznam typů.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

Typy jsou definovány v `EntityTypes.Types`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

Poskytovatel funkce je přidaný v `Startup`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

Názvy obecných řadičů používané pro směrování mají tvar *GenericController ' 1 [widget]* , nikoli *widget*. Následující atribut upraví název tak, aby odpovídal obecnému typu používanému řadičem:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

Třída `GenericController`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

### <a name="display-available-features"></a>Zobrazit dostupné funkce

Funkce, které jsou k dispozici pro aplikaci, mohou být `ApplicationPartManager` vyhodnoceny pomocí injektáže prostřednictvím [Injektáže závislosti](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[Ukázka ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace.