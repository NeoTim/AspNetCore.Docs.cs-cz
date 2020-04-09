---
title: Metabalíček Microsoft.AspNetCore.App pro ASP.NET jádro
author: Rick-Anderson
description: Sdílený rámec Microsoft.AspNetCore.App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: b30c90116f5a53ba487f88544514f36e388233d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511376"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="2fc35-103">Microsoft.AspNetCore.App pro ASP.NET jádro</span><span class="sxs-lookup"><span data-stu-id="2fc35-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="2fc35-104">Sdílené rozhraní ASP.NET`Microsoft.AspNetCore.App`Core ( ) obsahuje sestavení, která jsou vyvíjena a podporována společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2fc35-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="2fc35-105">`Microsoft.AspNetCore.App`je nainstalována při instalaci sady [.NET Core 3.0 nebo novější SDK.](https://dotnet.microsoft.com/download/dotnet-core/3.0)</span><span class="sxs-lookup"><span data-stu-id="2fc35-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="2fc35-106">*Sdílená architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a obsahují součást runtime a balíček cílení.</span><span class="sxs-lookup"><span data-stu-id="2fc35-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="2fc35-107">Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="2fc35-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="2fc35-108">Projekty, `Microsoft.NET.Sdk.Web` které se zaměřují na `Microsoft.AspNetCore.App` sdk implicitně odkazovat na rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2fc35-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="2fc35-109">Pro tyto projekty nejsou vyžadovány žádné další odkazy:</span><span class="sxs-lookup"><span data-stu-id="2fc35-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="2fc35-110">Sdílený rámec ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2fc35-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="2fc35-111">Nezahrnuje závislosti třetích stran.</span><span class="sxs-lookup"><span data-stu-id="2fc35-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="2fc35-112">Zahrnuje všechny podporované balíčky ASP.NET týmu Core.</span><span class="sxs-lookup"><span data-stu-id="2fc35-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2fc35-113">Tato funkce vyžaduje ASP.NET cílení na jádro 2.x .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="2fc35-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="2fc35-114">[Metabalíček](/dotnet/core/packages#metapackages) [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) pro ASP.NET jádro:</span><span class="sxs-lookup"><span data-stu-id="2fc35-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="2fc35-115">Nezahrnuje závislosti třetích stran s výjimkou [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/)a [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="2fc35-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="2fc35-116">Tyto závislosti třetích stran jsou považovány za nezbytné k zajištění funkce funkce hlavní architektury.</span><span class="sxs-lookup"><span data-stu-id="2fc35-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="2fc35-117">Zahrnuje všechny podporované balíčky ASP.NET základního týmu s výjimkou těch, které obsahují závislosti třetích stran (jiné než ty, které byly zmíněny).</span><span class="sxs-lookup"><span data-stu-id="2fc35-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="2fc35-118">Zahrnuje všechny podporované balíčky týmu Entity Framework Core s výjimkou těch, které obsahují závislosti třetích stran (jiné než ty, které byly zmíněny).</span><span class="sxs-lookup"><span data-stu-id="2fc35-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="2fc35-119">Všechny funkce ASP.NET Core 2.x a Entity Framework Core `Microsoft.AspNetCore.App` 2.x jsou součástí balíčku.</span><span class="sxs-lookup"><span data-stu-id="2fc35-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="2fc35-120">Výchozí šablony projektu zaměřené na ASP.NET Core 2.x používají tento balíček.</span><span class="sxs-lookup"><span data-stu-id="2fc35-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="2fc35-121">Doporučujeme aplikace zaměřené ASP.NET Core 2.x a Entity `Microsoft.AspNetCore.App` Framework Core 2.x použít balíček.</span><span class="sxs-lookup"><span data-stu-id="2fc35-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="2fc35-122">Číslo verze `Microsoft.AspNetCore.App` metabalíčku představuje minimální verzi ASP.NET Core a core verze entity frameworku.</span><span class="sxs-lookup"><span data-stu-id="2fc35-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="2fc35-123">Použití `Microsoft.AspNetCore.App` metabalíčku poskytuje omezení verze, která chrání vaši aplikaci:</span><span class="sxs-lookup"><span data-stu-id="2fc35-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="2fc35-124">Pokud je součástí balíčku, který má přenosnou (ne přímou) závislost na balíčku v `Microsoft.AspNetCore.App`aplikaci a tato čísla verzí se liší, NuGet vygeneruje chybu.</span><span class="sxs-lookup"><span data-stu-id="2fc35-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="2fc35-125">Další balíčky přidané do vaší aplikace nemohou `Microsoft.AspNetCore.App`změnit verzi balíčků zahrnutých v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="2fc35-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="2fc35-126">Konzistence verze zajišťuje spolehlivé prostředí.</span><span class="sxs-lookup"><span data-stu-id="2fc35-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="2fc35-127">`Microsoft.AspNetCore.App`byl navržen tak, aby se zabránilo netestované verze kombinace souvisejících bitů se používá společně ve stejné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2fc35-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="2fc35-128">Aplikace, které `Microsoft.AspNetCore.App` používají metabalíček automaticky využít ASP.NET základní sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2fc35-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="2fc35-129">Při použití `Microsoft.AspNetCore.App` metabalíčku **žádné** prostředky z odkazované ASP.NET balíčky Core NuGet jsou nasazeny s aplikací&mdash;ASP.NET základní sdílené rozhraní obsahuje tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="2fc35-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="2fc35-130">Prostředky ve sdíleném rámci jsou předkompilovány pro zlepšení doby spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fc35-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="2fc35-131">Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="2fc35-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="2fc35-132">Následující soubor projektu odkazuje `Microsoft.AspNetCore.App` na metabalíček pro ASP.NET Core a představuje typickou šablonu ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="2fc35-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="2fc35-133">Předchozí značky představuje typickou šablonu ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="2fc35-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="2fc35-134">Neurčuje číslo verze pro odkaz `Microsoft.AspNetCore.App` na balíček.</span><span class="sxs-lookup"><span data-stu-id="2fc35-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="2fc35-135">Pokud není zadána verze, [implicitní](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) verze je určena sadou `Microsoft.NET.Sdk.Web`SDK, to znamená .</span><span class="sxs-lookup"><span data-stu-id="2fc35-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="2fc35-136">Doporučujeme spoléhat se na implicitní verzi určenou sadou SDK a neexplicitně nastavit číslo verze v odkazu na balíček.</span><span class="sxs-lookup"><span data-stu-id="2fc35-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="2fc35-137">Pokud máte dotazy k tomuto přístupu, zanechte komentář GitHub u [implicitní verze Diskuse o microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="2fc35-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="2fc35-138">Implicitní verze je `major.minor.0` nastavena na pro přenosné aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fc35-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="2fc35-139">Mechanismus pro předávání sdílených architektur spustí aplikaci v nejnovější kompatibilní verzi mezi nainstalovanými sdílenými rámci.</span><span class="sxs-lookup"><span data-stu-id="2fc35-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="2fc35-140">Chcete-li zaručit, že stejná verze se používá ve vývoji, testování a výrobě, ujistěte se, že stejná verze sdíleného rozhraní je nainstalována ve všech prostředích.</span><span class="sxs-lookup"><span data-stu-id="2fc35-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="2fc35-141">U samostatných aplikací je implicitní číslo `major.minor.patch` verze nastaveno na sdílenou architekturu, která je součástí nainstalované sady SDK.</span><span class="sxs-lookup"><span data-stu-id="2fc35-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="2fc35-142">Zadání čísla verze `Microsoft.AspNetCore.App` v odkazu **nezaručuje,** že bude vybrána verze sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2fc35-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="2fc35-143">Předpokládejme například, že je zadána verze "2.2.1", ale je nainstalována verze "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="2fc35-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="2fc35-144">V takovém případě bude aplikace používat "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="2fc35-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="2fc35-145">I když se nedoporučuje, můžete zakázat roll vpřed (patch a / nebo menší).</span><span class="sxs-lookup"><span data-stu-id="2fc35-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="2fc35-146">Další informace týkající se dotnet hostitele roll-forward a jak nakonfigurovat jeho chování, naleznete [v tématu dotnet hostitele posunout vpřed](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="2fc35-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="2fc35-147">`<Project Sdk`musí být `Microsoft.NET.Sdk.Web` nastavena tak, `Microsoft.AspNetCore.App`aby používala implicitní verzi .</span><span class="sxs-lookup"><span data-stu-id="2fc35-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="2fc35-148">Při `<Project Sdk="Microsoft.NET.Sdk">` použití (bez `.Web`koncového) použití:</span><span class="sxs-lookup"><span data-stu-id="2fc35-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="2fc35-149">Je generováno následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="2fc35-149">The following warning is generated:</span></span>

  <span data-ttu-id="2fc35-150">*Upozornění NU1604: Závislost projektu Microsoft.AspNetCore.App neobsahuje včetně dolní mez. Zahrnout dolní mez ve verzi závislosti zajistit konzistentní výsledky obnovení.*</span><span class="sxs-lookup"><span data-stu-id="2fc35-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="2fc35-151">Toto je známý problém s .NET Core 2.1 SDK.</span><span class="sxs-lookup"><span data-stu-id="2fc35-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="2fc35-152">Aktualizovat ASP.NET jádro</span><span class="sxs-lookup"><span data-stu-id="2fc35-152">Update ASP.NET Core</span></span>

<span data-ttu-id="2fc35-153">`Microsoft.AspNetCore.App` [Metapackage](/dotnet/core/packages#metapackages) není tradiční balíček, který je aktualizován z NuGet.</span><span class="sxs-lookup"><span data-stu-id="2fc35-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="2fc35-154">Podobně `Microsoft.NETCore.App`jako `Microsoft.AspNetCore.App` , představuje sdílený runtime, který má speciální sémantiku správu verzí zpracovány mimo NuGet.</span><span class="sxs-lookup"><span data-stu-id="2fc35-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="2fc35-155">Další informace naleznete [v tématu Balíčky, metabalíčky a rámce](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="2fc35-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="2fc35-156">Aktualizace ASP.NET jádra:</span><span class="sxs-lookup"><span data-stu-id="2fc35-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="2fc35-157">Na vývojových počítačích a serverech sestavení: Stáhněte a nainstalujte sadu [.NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="2fc35-157">On development machines and build servers: Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="2fc35-158">Na nasazení serverů: Stáhněte a nainstalujte [runtime .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="2fc35-158">On deployment servers: Download and install the [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>

 <span data-ttu-id="2fc35-159">Aplikace se při restartování aplikace posunou na nejnovější nainstalovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="2fc35-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="2fc35-160">Není nutné aktualizovat číslo `Microsoft.AspNetCore.App` verze v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="2fc35-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="2fc35-161">Další informace najdete v [tématu Aplikace závislé na rozhraní framework ustoupit](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="2fc35-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="2fc35-162">Pokud vaše aplikace `Microsoft.AspNetCore.All`dříve používala , přečtěte si informace [o migraci z microsoft.AspNetCore.All na Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="2fc35-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
