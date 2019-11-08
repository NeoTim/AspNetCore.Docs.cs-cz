---
title: Části aplikace v ASP.NET Core
author: rick-anderson
description: Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core
ms.author: riande
ms.date: 11/7/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: ff6afa1852a3ee97fc4dbbae970dd746ec92f74c
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799468"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="a6fdd-103">Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6fdd-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6fdd-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6fdd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a6fdd-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a6fdd-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a6fdd-106">*Část aplikace* je abstrakcí přes prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="a6fdd-107">Části aplikace umožňují ASP.NET Core zjišťování řadičů, zobrazení komponent, pomocníků značek, Razor Pages, zdrojů kompilace Razor a dalších.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="a6fdd-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) je součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="a6fdd-109">`AssemblyPart` zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="a6fdd-110">*Poskytovatelé funkcí* fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="a6fdd-111">Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="a6fdd-112">Můžete například chtít sdílet běžné funkce mezi více aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="a6fdd-113">Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor Pages, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="a6fdd-114">Sdílení sestavení je preferované pro duplikování kódu ve více projektech.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="a6fdd-115">ASP.NET Core aplikace načítají funkce z <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="a6fdd-116">Třída <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> představuje část aplikace, která je zajištěna sestavením.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="a6fdd-117">Načíst ASP.NET Core funkce</span><span class="sxs-lookup"><span data-stu-id="a6fdd-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="a6fdd-118">Pomocí tříd `ApplicationPart` a `AssemblyPart` můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.).</span><span class="sxs-lookup"><span data-stu-id="a6fdd-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="a6fdd-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> sleduje součásti aplikace a poskytovatele funkcí, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="a6fdd-120">`ApplicationPartManager` je nakonfigurované v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="a6fdd-121">Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="a6fdd-122">Předchozí dva ukázky kódu načtou `SharedController` ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="a6fdd-123">`SharedController` není v projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="a6fdd-124">Viz stažení ukázkového [řešení WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="a6fdd-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="a6fdd-125">Zahrnutí zobrazení</span><span class="sxs-lookup"><span data-stu-id="a6fdd-125">Include views</span></span>

<span data-ttu-id="a6fdd-126">Zahrnutí zobrazení do sestavení:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-126">To include views in the assembly:</span></span>

* <span data-ttu-id="a6fdd-127">Do souboru sdíleného projektu přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="a6fdd-128">Přidejte <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> do <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="a6fdd-129">Zabránit načítání prostředků</span><span class="sxs-lookup"><span data-stu-id="a6fdd-129">Prevent loading resources</span></span>

<span data-ttu-id="a6fdd-130">Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="a6fdd-131">Přidejte nebo odeberte členy kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> pro skrytí nebo zpřístupnění dostupných prostředků.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="a6fdd-132">Pořadí položek v kolekci `ApplicationParts` není důležité.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="a6fdd-133">Nakonfigurujte `ApplicationPartManager` před tím, než ji použijete ke konfiguraci služeb v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="a6fdd-134">Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="a6fdd-135">Chcete-li odebrat prostředek, zavolejte `Remove` v kolekci `ApplicationParts`.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="a6fdd-136">Následující kód používá <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> k odebrání `MyDependentLibrary` z aplikace: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="a6fdd-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="a6fdd-137">`ApplicationPartManager` obsahuje části pro:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="a6fdd-138">Sestavení aplikace a závislá sestavení.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="a6fdd-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="a6fdd-140">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="a6fdd-141">Poskytovatelé funkcí aplikace</span><span class="sxs-lookup"><span data-stu-id="a6fdd-141">Application feature providers</span></span>

<span data-ttu-id="a6fdd-142">Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="a6fdd-143">K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="a6fdd-144">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="a6fdd-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="a6fdd-145">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="a6fdd-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="a6fdd-146">Zobrazit součásti</span><span class="sxs-lookup"><span data-stu-id="a6fdd-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="a6fdd-147">Poskytovatelé funkcí dědí z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, kde `T` je typ funkce.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="a6fdd-148">Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="a6fdd-149">Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="a6fdd-150">Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="a6fdd-151">Funkce obecného kontroleru</span><span class="sxs-lookup"><span data-stu-id="a6fdd-151">Generic controller feature</span></span>

<span data-ttu-id="a6fdd-152">ASP.NET Core ignorují [Obecné řadiče](/dotnet/csharp/programming-guide/generics/generic-classes).</span><span class="sxs-lookup"><span data-stu-id="a6fdd-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="a6fdd-153">Obecný kontroler má parametr typu (například `MyController<T>`).</span><span class="sxs-lookup"><span data-stu-id="a6fdd-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="a6fdd-154">Následující příklad přidá instance obecného kontroleru pro zadaný seznam typů:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="a6fdd-155">Typy jsou definovány v `EntityTypes.Types`:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="a6fdd-156">Do `Startup`se přidá poskytovatel funkcí:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="a6fdd-157">Názvy obecných řadičů používané pro směrování mají tvar *GenericController ' 1 [widget]* , nikoli *widget*.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="a6fdd-158">Následující atribut upraví název tak, aby odpovídal obecnému typu používanému řadičem:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="a6fdd-159">Třída `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="a6fdd-160">Například vyžádání adresy URL `https://localhost:5001/Sprocket` má za následek následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="a6fdd-161">Zobrazit dostupné funkce</span><span class="sxs-lookup"><span data-stu-id="a6fdd-161">Display available features</span></span>

<span data-ttu-id="a6fdd-162">Funkce, které jsou k dispozici pro aplikaci, mohou být vyčísleny vyžádáním `ApplicationPartManager` prostřednictvím [Injektáže závislosti](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="a6fdd-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="a6fdd-163">[Ukázka ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

::: moniker-end

::: moniker range="<= aspnetcore-3.0"

<span data-ttu-id="a6fdd-164">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6fdd-164">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a6fdd-165">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a6fdd-165">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a6fdd-166">*Část aplikace* je abstrakcí přes prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-166">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="a6fdd-167">Části aplikace umožňují ASP.NET Core zjišťování řadičů, zobrazení komponent, pomocníků značek, Razor Pages, zdrojů kompilace Razor a dalších.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-167">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="a6fdd-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) je součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="a6fdd-169">`AssemblyPart` zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-169">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="a6fdd-170">*Poskytovatelé funkcí* fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-170">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="a6fdd-171">Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-171">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="a6fdd-172">Můžete například chtít sdílet běžné funkce mezi více aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-172">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="a6fdd-173">Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor Pages, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-173">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="a6fdd-174">Sdílení sestavení je preferované pro duplikování kódu ve více projektech.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-174">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="a6fdd-175">ASP.NET Core aplikace načítají funkce z <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-175">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="a6fdd-176">Třída <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> představuje část aplikace, která je zajištěna sestavením.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-176">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="a6fdd-177">Načíst ASP.NET Core funkce</span><span class="sxs-lookup"><span data-stu-id="a6fdd-177">Load ASP.NET Core features</span></span>

<span data-ttu-id="a6fdd-178">Pomocí tříd `ApplicationPart` a `AssemblyPart` můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.).</span><span class="sxs-lookup"><span data-stu-id="a6fdd-178">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="a6fdd-179"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> sleduje součásti aplikace a poskytovatele funkcí, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-179">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="a6fdd-180">`ApplicationPartManager` je nakonfigurované v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-180">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="a6fdd-181">Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-181">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="a6fdd-182">Předchozí dva ukázky kódu načtou `SharedController` ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-182">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="a6fdd-183">`SharedController` není v projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-183">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="a6fdd-184">Viz stažení ukázkového [řešení WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="a6fdd-184">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="a6fdd-185">Zahrnutí zobrazení</span><span class="sxs-lookup"><span data-stu-id="a6fdd-185">Include views</span></span>

<span data-ttu-id="a6fdd-186">Zahrnutí zobrazení do sestavení:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-186">To include views in the assembly:</span></span>

* <span data-ttu-id="a6fdd-187">Do souboru sdíleného projektu přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-187">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="a6fdd-188">Přidejte <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> do <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-188">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="a6fdd-189">Zabránit načítání prostředků</span><span class="sxs-lookup"><span data-stu-id="a6fdd-189">Prevent loading resources</span></span>

<span data-ttu-id="a6fdd-190">Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-190">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="a6fdd-191">Přidejte nebo odeberte členy kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> pro skrytí nebo zpřístupnění dostupných prostředků.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-191">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="a6fdd-192">Pořadí položek v kolekci `ApplicationParts` není důležité.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-192">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="a6fdd-193">Nakonfigurujte `ApplicationPartManager` před tím, než ji použijete ke konfiguraci služeb v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-193">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="a6fdd-194">Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-194">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="a6fdd-195">Chcete-li odebrat prostředek, zavolejte `Remove` v kolekci `ApplicationParts`.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-195">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="a6fdd-196">Následující kód používá <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> k odebrání `MyDependentLibrary` z aplikace: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="a6fdd-196">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="a6fdd-197">`ApplicationPartManager` obsahuje části pro:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-197">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="a6fdd-198">Sestavení aplikace a závislá sestavení.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-198">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="a6fdd-199">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-199">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="a6fdd-200">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-200">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="a6fdd-201">Poskytovatelé funkcí aplikace</span><span class="sxs-lookup"><span data-stu-id="a6fdd-201">Application feature providers</span></span>

<span data-ttu-id="a6fdd-202">Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-202">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="a6fdd-203">K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-203">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="a6fdd-204">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="a6fdd-204">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="a6fdd-205">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="a6fdd-205">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="a6fdd-206">Zobrazit součásti</span><span class="sxs-lookup"><span data-stu-id="a6fdd-206">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="a6fdd-207">Poskytovatelé funkcí dědí z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, kde `T` je typ funkce.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-207">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="a6fdd-208">Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-208">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="a6fdd-209">Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-209">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="a6fdd-210">Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-210">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="a6fdd-211">Funkce obecného kontroleru</span><span class="sxs-lookup"><span data-stu-id="a6fdd-211">Generic controller feature</span></span>

<span data-ttu-id="a6fdd-212">ASP.NET Core ignorují [Obecné řadiče](/dotnet/csharp/programming-guide/generics/generic-classes).</span><span class="sxs-lookup"><span data-stu-id="a6fdd-212">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="a6fdd-213">Obecný kontroler má parametr typu (například `MyController<T>`).</span><span class="sxs-lookup"><span data-stu-id="a6fdd-213">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="a6fdd-214">Následující příklad přidá instance obecného kontroleru pro zadaný seznam typů:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-214">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="a6fdd-215">Typy jsou definovány v `EntityTypes.Types`:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-215">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="a6fdd-216">Do `Startup`se přidá poskytovatel funkcí:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-216">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="a6fdd-217">Názvy obecných řadičů používané pro směrování mají tvar *GenericController ' 1 [widget]* , nikoli *widget*.</span><span class="sxs-lookup"><span data-stu-id="a6fdd-217">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="a6fdd-218">Následující atribut upraví název tak, aby odpovídal obecnému typu používanému řadičem:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-218">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="a6fdd-219">Třída `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-219">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="a6fdd-220">Například vyžádání adresy URL `https://localhost:5001/Sprocket` má za následek následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-220">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="a6fdd-221">Zobrazit dostupné funkce</span><span class="sxs-lookup"><span data-stu-id="a6fdd-221">Display available features</span></span>

<span data-ttu-id="a6fdd-222">Funkce, které jsou k dispozici pro aplikaci, mohou být vyčísleny vyžádáním `ApplicationPartManager` prostřednictvím [Injektáže závislosti](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="a6fdd-222">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="a6fdd-223">[Ukázka ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace:</span><span class="sxs-lookup"><span data-stu-id="a6fdd-223">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

::: moniker-end