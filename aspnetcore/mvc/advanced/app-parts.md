---
title: Části aplikace v ASP.NET Core
author: rick-anderson
description: Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core
ms.author: riande
ms.date: 05/14/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4b4c8c554a7045a180b56cf9998ab1a8496cde1b
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207346"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="f7f3d-103">Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7f3d-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="f7f3d-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7f3d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f7f3d-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7f3d-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f7f3d-106">*Část aplikace* je abstrakcí přes prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="f7f3d-107">Části aplikace umožňují ASP.NET Core zjišťování řadičů, zobrazení komponent, pomocníků značek, Razor Pages, zdrojů kompilace Razor a dalších.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="f7f3d-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) je součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="f7f3d-109">`AssemblyPart`Zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="f7f3d-110">*Poskytovatelé funkcí* fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="f7f3d-111">Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="f7f3d-112">Můžete například chtít sdílet běžné funkce mezi více aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="f7f3d-113">Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor Pages, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="f7f3d-114">Sdílení sestavení je preferované pro duplikování kódu ve více projektech.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="f7f3d-115">ASP.NET Core aplikace načítají funkce <xref:System.Web.WebPages.ApplicationPart>z.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="f7f3d-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> Třída představuje část aplikace, která je zajištěna sestavením.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="f7f3d-117">Načíst ASP.NET Core funkce</span><span class="sxs-lookup"><span data-stu-id="f7f3d-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="f7f3d-118">Pomocí tříd `AssemblyPart` a můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.). `ApplicationPart`</span><span class="sxs-lookup"><span data-stu-id="f7f3d-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="f7f3d-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> Sleduje, jaké součásti aplikace a poskytovatelé funkcí jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="f7f3d-120">`ApplicationPartManager`je nakonfigurované `Startup.ConfigureServices`v:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="f7f3d-121">Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí: `AssemblyPart`</span><span class="sxs-lookup"><span data-stu-id="f7f3d-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="f7f3d-122">Předchozí dva ukázky kódu načtou `SharedController` ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="f7f3d-123">`SharedController` Není v projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="f7f3d-124">Viz stažení ukázkového [řešení WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="f7f3d-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="f7f3d-125">Zahrnutí zobrazení</span><span class="sxs-lookup"><span data-stu-id="f7f3d-125">Include views</span></span>

<span data-ttu-id="f7f3d-126">Zahrnutí zobrazení do sestavení:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-126">To include views in the assembly:</span></span>

* <span data-ttu-id="f7f3d-127">Do souboru sdíleného projektu přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="f7f3d-128"><xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> Přidejte<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>do:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="f7f3d-129">Zabránit načítání prostředků</span><span class="sxs-lookup"><span data-stu-id="f7f3d-129">Prevent loading resources</span></span>

<span data-ttu-id="f7f3d-130">Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="f7f3d-131">Přidáním nebo odebráním členů <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekce můžete skrýt nebo zpřístupnit dostupné prostředky.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="f7f3d-132">Pořadí položek v `ApplicationParts` kolekci není důležité.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="f7f3d-133">`ApplicationPartManager` Nakonfigurujte před tím, než je použijete ke konfiguraci služeb v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="f7f3d-134">Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="f7f3d-135">Chcete `Remove` -li `ApplicationParts` odebrat prostředek, zavolejte na kolekci.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="f7f3d-136">Následující kód používá <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> k odebrání `MyDependentLibrary` z aplikace:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="f7f3d-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="f7f3d-137">`ApplicationPartManager` Obsahuje části pro:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="f7f3d-138">Sestavení aplikace a závislá sestavení.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="f7f3d-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="f7f3d-140">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="f7f3d-141">Poskytovatelé funkcí aplikace</span><span class="sxs-lookup"><span data-stu-id="f7f3d-141">Application feature providers</span></span>

<span data-ttu-id="f7f3d-142">Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="f7f3d-143">K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="f7f3d-144">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="f7f3d-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="f7f3d-145">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="f7f3d-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="f7f3d-146">Zobrazit součásti</span><span class="sxs-lookup"><span data-stu-id="f7f3d-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="f7f3d-147">Poskytovatelé funkcí dědí <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>z, `T` kde je typ funkce.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="f7f3d-148">Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="f7f3d-149">Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="f7f3d-150">Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="f7f3d-151">Funkce obecného kontroleru</span><span class="sxs-lookup"><span data-stu-id="f7f3d-151">Generic controller feature</span></span>

<span data-ttu-id="f7f3d-152">ASP.NET Core ignorují [Obecné řadiče](/dotnet/csharp/programming-guide/generics/generic-classes).</span><span class="sxs-lookup"><span data-stu-id="f7f3d-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="f7f3d-153">Obecný kontroler má parametr typu (například `MyController<T>`).</span><span class="sxs-lookup"><span data-stu-id="f7f3d-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="f7f3d-154">Následující příklad přidá instance obecného kontroleru pro zadaný seznam typů:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="f7f3d-155">Typy jsou definovány v `EntityTypes.Types`:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="f7f3d-156">Poskytovatel funkce je přidaný v `Startup`:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="f7f3d-157">Názvy obecných řadičů používané pro směrování mají tvar *GenericController ' 1 [widget]* , nikoli *widget*.</span><span class="sxs-lookup"><span data-stu-id="f7f3d-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="f7f3d-158">Následující atribut upraví název tak, aby odpovídal obecnému typu používanému řadičem:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="f7f3d-159">Třída `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="f7f3d-160">Například vyžádání adresy URL `https://localhost:5001/Sprocket` s výsledkem je následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="f7f3d-161">Zobrazit dostupné funkce</span><span class="sxs-lookup"><span data-stu-id="f7f3d-161">Display available features</span></span>

<span data-ttu-id="f7f3d-162">Funkce, které jsou k dispozici pro aplikaci, mohou být vyhodnoceny požadavkem `ApplicationPartManager` na [vkládání závislostí](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="f7f3d-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="f7f3d-163">[Ukázka ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7f3d-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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
