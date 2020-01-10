---
title: Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core
author: rick-anderson
description: Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace v ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: a95c344410db0651b9f8f1c1eb7551029f084c25
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829072"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a><span data-ttu-id="0dc8b-103">Sdílení řadičů, zobrazení, Razor Pages a dalších prvků aplikace</span><span class="sxs-lookup"><span data-stu-id="0dc8b-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0dc8b-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0dc8b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0dc8b-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0dc8b-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0dc8b-106">*Část aplikace* je abstrakcí přes prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="0dc8b-107">Části aplikace umožňují ASP.NET Core zjišťování řadičů, zobrazení komponent, pomocníků značek, Razor Pages, zdrojů kompilace Razor a dalších.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="0dc8b-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> je součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="0dc8b-109">`AssemblyPart` zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="0dc8b-110">[Poskytovatelé funkcí](#fp) fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="0dc8b-111">Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="0dc8b-112">Můžete například chtít sdílet běžné funkce mezi více aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="0dc8b-113">Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor Pages, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="0dc8b-114">Sdílení sestavení je preferované pro duplikování kódu ve více projektech.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="0dc8b-115">ASP.NET Core aplikace načítají funkce z <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="0dc8b-116">Třída <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> představuje část aplikace, která je zajištěna sestavením.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="0dc8b-117">Načíst ASP.NET Core funkce</span><span class="sxs-lookup"><span data-stu-id="0dc8b-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="0dc8b-118">Pomocí tříd <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> a <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.).</span><span class="sxs-lookup"><span data-stu-id="0dc8b-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="0dc8b-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> sleduje součásti aplikace a poskytovatele funkcí, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="0dc8b-120">`ApplicationPartManager` je nakonfigurované v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="0dc8b-121">Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="0dc8b-122">Předchozí dva ukázky kódu načtou `SharedController` ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="0dc8b-123">`SharedController` není v projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="0dc8b-124">Viz stažení ukázkového [řešení WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="0dc8b-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="0dc8b-125">Zahrnutí zobrazení</span><span class="sxs-lookup"><span data-stu-id="0dc8b-125">Include views</span></span>

<span data-ttu-id="0dc8b-126">Použijte [knihovnu tříd Razor](xref:razor-pages/ui-class) pro zahrnutí zobrazení do sestavení.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="0dc8b-127">Zabránit načítání prostředků</span><span class="sxs-lookup"><span data-stu-id="0dc8b-127">Prevent loading resources</span></span>

<span data-ttu-id="0dc8b-128">Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="0dc8b-129">Přidejte nebo odeberte členy kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> pro skrytí nebo zpřístupnění dostupných prostředků.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="0dc8b-130">Pořadí položek v kolekci `ApplicationParts` není důležité.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="0dc8b-131">Nakonfigurujte `ApplicationPartManager` před tím, než ji použijete ke konfiguraci služeb v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="0dc8b-132">Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="0dc8b-133">Chcete-li odebrat prostředek, zavolejte `Remove` v kolekci `ApplicationParts`.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="0dc8b-134">`ApplicationPartManager` obsahuje části pro:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="0dc8b-135">Sestavení aplikace a závislá sestavení.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="0dc8b-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="0dc8b-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="0dc8b-138">Poskytovatelé funkcí</span><span class="sxs-lookup"><span data-stu-id="0dc8b-138">Feature providers</span></span>

<span data-ttu-id="0dc8b-139">Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="0dc8b-140">K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="0dc8b-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="0dc8b-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="0dc8b-142">Poskytovatelé funkcí dědí z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, kde `T` je typ funkce.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="0dc8b-143">Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="0dc8b-144">Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="0dc8b-145">Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="0dc8b-146">Zobrazit dostupné funkce</span><span class="sxs-lookup"><span data-stu-id="0dc8b-146">Display available features</span></span>

<span data-ttu-id="0dc8b-147">Funkce, které jsou k dispozici pro aplikaci, mohou být vyčísleny vyžádáním `ApplicationPartManager` prostřednictvím [Injektáže závislosti](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="0dc8b-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="0dc8b-148">[Ukázka ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-148">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

## <a name="discovery-in-application-parts"></a><span data-ttu-id="0dc8b-149">Zjišťování v součástech aplikace</span><span class="sxs-lookup"><span data-stu-id="0dc8b-149">Discovery in application parts</span></span>

<span data-ttu-id="0dc8b-150">Chyby HTTP 404 nejsou běžné při vývoji s součástmi aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="0dc8b-151">Tyto chyby jsou obvykle způsobeny chybějícím základním požadavkem na zjištění částí aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="0dc8b-152">Pokud vaše aplikace vrátí chybu HTTP 404, ověřte, zda byly splněny následující požadavky:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="0dc8b-153">Nastavení `applicationName` musí být nastavené na kořenové sestavení používané pro zjišťování.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="0dc8b-154">Kořenové sestavení použité pro zjišťování je obvykle sestavení vstupního bodu.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="0dc8b-155">Kořenové sestavení musí mít odkaz na části používané pro zjišťování.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="0dc8b-156">Odkaz může být přímý nebo přenosný.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="0dc8b-157">Kořenové sestavení musí odkazovat na webovou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="0dc8b-158">Rozhraní obsahuje logiku, která označí atributy do kořenového sestavení používaného pro zjišťování.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0dc8b-159">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0dc8b-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0dc8b-160">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0dc8b-160">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0dc8b-161">*Část aplikace* je abstrakcí přes prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="0dc8b-162">Části aplikace umožňují ASP.NET Core zjišťování řadičů, zobrazení komponent, pomocníků značek, Razor Pages, zdrojů kompilace Razor a dalších.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="0dc8b-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) je součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="0dc8b-164">`AssemblyPart` zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="0dc8b-165">*Poskytovatelé funkcí* fungují s součástmi aplikace k naplnění funkcí aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="0dc8b-166">Hlavní případ použití pro části aplikace je konfigurace aplikace pro zjišťování (nebo zamezení načítání) ASP.NET Core funkcí ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="0dc8b-167">Můžete například chtít sdílet běžné funkce mezi více aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="0dc8b-168">Pomocí částí aplikace můžete sdílet sestavení (DLL) obsahující řadiče, zobrazení, Razor Pages, zdroje kompilace Razor, pomocníka značek a další informace o více aplikacích.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="0dc8b-169">Sdílení sestavení je preferované pro duplikování kódu ve více projektech.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="0dc8b-170">ASP.NET Core aplikace načítají funkce z <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="0dc8b-171">Třída <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> představuje část aplikace, která je zajištěna sestavením.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="0dc8b-172">Načíst ASP.NET Core funkce</span><span class="sxs-lookup"><span data-stu-id="0dc8b-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="0dc8b-173">Pomocí tříd `ApplicationPart` a `AssemblyPart` můžete zjišťovat a načítat ASP.NET Core funkce (řadiče, zobrazit součásti atd.).</span><span class="sxs-lookup"><span data-stu-id="0dc8b-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="0dc8b-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> sleduje součásti aplikace a poskytovatele funkcí, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="0dc8b-175">`ApplicationPartManager` je nakonfigurované v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="0dc8b-176">Následující kód poskytuje alternativní přístup ke konfiguraci `ApplicationPartManager` pomocí `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="0dc8b-177">Předchozí dva ukázky kódu načtou `SharedController` ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="0dc8b-178">`SharedController` není v projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="0dc8b-179">Viz stažení ukázkového [řešení WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="0dc8b-179">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="0dc8b-180">Zahrnutí zobrazení</span><span class="sxs-lookup"><span data-stu-id="0dc8b-180">Include views</span></span>

<span data-ttu-id="0dc8b-181">Použijte [knihovnu tříd Razor](xref:razor-pages/ui-class) pro zahrnutí zobrazení do sestavení.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="0dc8b-182">Zabránit načítání prostředků</span><span class="sxs-lookup"><span data-stu-id="0dc8b-182">Prevent loading resources</span></span>

<span data-ttu-id="0dc8b-183">Části aplikace lze použít k *zamezení* načítání prostředků do konkrétního sestavení nebo umístění.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="0dc8b-184">Přidejte nebo odeberte členy kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> pro skrytí nebo zpřístupnění dostupných prostředků.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="0dc8b-185">Pořadí položek v kolekci `ApplicationParts` není důležité.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="0dc8b-186">Nakonfigurujte `ApplicationPartManager` před tím, než ji použijete ke konfiguraci služeb v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="0dc8b-187">Například nakonfigurujte `ApplicationPartManager` před vyvoláním `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="0dc8b-188">Chcete-li odebrat prostředek, zavolejte `Remove` v kolekci `ApplicationParts`.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="0dc8b-189">Následující kód používá <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> k odebrání `MyDependentLibrary` z aplikace: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="0dc8b-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="0dc8b-190">`ApplicationPartManager` obsahuje části pro:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="0dc8b-191">Sestavení aplikace a závislá sestavení.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="0dc8b-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="0dc8b-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="0dc8b-194">Poskytovatelé funkcí aplikace</span><span class="sxs-lookup"><span data-stu-id="0dc8b-194">Application feature providers</span></span>

<span data-ttu-id="0dc8b-195">Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="0dc8b-196">K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="0dc8b-197">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="0dc8b-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="0dc8b-198">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="0dc8b-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="0dc8b-199">Zobrazit součásti</span><span class="sxs-lookup"><span data-stu-id="0dc8b-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="0dc8b-200">Poskytovatelé funkcí dědí z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, kde `T` je typ funkce.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="0dc8b-201">Poskytovatele funkcí lze implementovat pro některý z dříve uvedených typů funkcí.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="0dc8b-202">Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` může ovlivnit chování za běhu.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="0dc8b-203">Později přidaní poskytovatelé můžou reagovat na akce provedené dříve přidanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="0dc8b-204">Zobrazit dostupné funkce</span><span class="sxs-lookup"><span data-stu-id="0dc8b-204">Display available features</span></span>

<span data-ttu-id="0dc8b-205">Funkce, které jsou k dispozici pro aplikaci, mohou být vyčísleny vyžádáním `ApplicationPartManager` prostřednictvím [Injektáže závislosti](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="0dc8b-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="0dc8b-206">[Ukázka ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) používá předchozí kód k zobrazení funkcí aplikace:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-206">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

## <a name="discovery-in-application-parts"></a><span data-ttu-id="0dc8b-207">Zjišťování v součástech aplikace</span><span class="sxs-lookup"><span data-stu-id="0dc8b-207">Discovery in application parts</span></span>

<span data-ttu-id="0dc8b-208">Chyby HTTP 404 nejsou běžné při vývoji s součástmi aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="0dc8b-209">Tyto chyby jsou obvykle způsobeny chybějícím základním požadavkem na zjištění částí aplikace.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="0dc8b-210">Pokud vaše aplikace vrátí chybu HTTP 404, ověřte, zda byly splněny následující požadavky:</span><span class="sxs-lookup"><span data-stu-id="0dc8b-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="0dc8b-211">Nastavení `applicationName` musí být nastavené na kořenové sestavení používané pro zjišťování.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="0dc8b-212">Kořenové sestavení použité pro zjišťování je obvykle sestavení vstupního bodu.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="0dc8b-213">Kořenové sestavení musí mít odkaz na části používané pro zjišťování.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="0dc8b-214">Odkaz může být přímý nebo přenosný.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="0dc8b-215">Kořenové sestavení musí odkazovat na webovou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="0dc8b-216">Rozhraní ASP.NET Core Framework má vlastní logiku sestavení, která označí atributy do kořenového sestavení používaného pro zjišťování.</span><span class="sxs-lookup"><span data-stu-id="0dc8b-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
