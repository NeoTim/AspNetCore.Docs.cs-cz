---
title: Microsoft. AspNetCore. app Metapackage pro ASP.NET Core
author: Rick-Anderson
description: Sdílené rozhraní Microsoft. AspNetCore. app
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/metapackage-app
ms.openlocfilehash: 5f3511b31b45b2c4ad4467bb49d4eaacf22ad437
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775554"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="58d51-103">Microsoft. AspNetCore. app pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58d51-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="58d51-104">Rozhraní ASP.NET Core Shared Framework`Microsoft.AspNetCore.App`() obsahuje sestavení vyvinutá a podporovaná společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="58d51-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="58d51-105">`Microsoft.AspNetCore.App`se nainstaluje, když je nainstalovaná [sada .NET Core 3,0 nebo novější SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="58d51-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="58d51-106">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a zahrnují komponentu modulu runtime a sadu targeting pack.</span><span class="sxs-lookup"><span data-stu-id="58d51-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="58d51-107">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="58d51-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="58d51-108">Projekty, které cílí `Microsoft.NET.Sdk.Web` na sadu SDK, implicitně odkazují na `Microsoft.AspNetCore.App` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="58d51-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="58d51-109">Pro tyto projekty nejsou vyžadovány žádné další odkazy:</span><span class="sxs-lookup"><span data-stu-id="58d51-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="58d51-110">ASP.NET Core sdílené rozhraní:</span><span class="sxs-lookup"><span data-stu-id="58d51-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="58d51-111">Neobsahuje závislosti třetích stran.</span><span class="sxs-lookup"><span data-stu-id="58d51-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="58d51-112">Zahrnuje všechny podporované balíčky ASP.NET Core týmu.</span><span class="sxs-lookup"><span data-stu-id="58d51-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58d51-113">Tato funkce vyžaduje ASP.NET Core 2. x cílící na rozhraní .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="58d51-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="58d51-114">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [Metapackage](/dotnet/core/packages#metapackages) pro ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="58d51-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="58d51-115">Nezahrnuje závislosti třetích stran kromě [JSON.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [remotion. Linq](https://www.nuget.org/packages/Remotion.Linq/)a [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="58d51-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="58d51-116">Tyto závislosti třetích stran se považují za nezbytné, aby se zajistilo, že funkce hlavních architektur funguje.</span><span class="sxs-lookup"><span data-stu-id="58d51-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="58d51-117">Zahrnuje všechny podporované balíčky ASP.NET Core týmu s výjimkou těch, které obsahují závislosti třetích stran (jiné než výše zmíněné).</span><span class="sxs-lookup"><span data-stu-id="58d51-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="58d51-118">Zahrnuje všechny podporované balíčky Entity Framework Core týmu s výjimkou těch, které obsahují závislosti třetích stran (jiné než výše zmíněné).</span><span class="sxs-lookup"><span data-stu-id="58d51-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="58d51-119">Do `Microsoft.AspNetCore.App` balíčku jsou zahrnuté všechny funkce ASP.NET Core 2. x a Entity Framework Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="58d51-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="58d51-120">Výchozí šablony projektu, které cílí na ASP.NET Core 2. x, používají tento balíček.</span><span class="sxs-lookup"><span data-stu-id="58d51-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="58d51-121">Doporučujeme, aby byly `Microsoft.AspNetCore.App` aplikace cílené ASP.NET Core 2. x a Entity Framework Core 2. x použijte balíček.</span><span class="sxs-lookup"><span data-stu-id="58d51-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="58d51-122">Číslo verze `Microsoft.AspNetCore.App` Metapackage představuje minimální verzi ASP.NET Core a Entity Framework Core verzi.</span><span class="sxs-lookup"><span data-stu-id="58d51-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="58d51-123">Použití `Microsoft.AspNetCore.App` Metapackage poskytuje omezení verze, která chrání vaši aplikaci:</span><span class="sxs-lookup"><span data-stu-id="58d51-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="58d51-124">Pokud je zahrnut balíček, který má přenositelný (nepřímý) závislý na balíčku v `Microsoft.AspNetCore.App`nástroji, přičemž se tato čísla verzí liší, NuGet vygeneruje chybu.</span><span class="sxs-lookup"><span data-stu-id="58d51-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="58d51-125">Jiné balíčky přidané do vaší aplikace nemůžou měnit verzi balíčků zahrnutých v `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="58d51-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="58d51-126">Konzistence verzí zajišťuje spolehlivé prostředí.</span><span class="sxs-lookup"><span data-stu-id="58d51-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="58d51-127">`Microsoft.AspNetCore.App`byla navržena tak, aby bránila netestovým kombinacím verzí souvisejících bitů společně ve stejné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="58d51-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="58d51-128">Aplikace, které používají `Microsoft.AspNetCore.App` Metapackage, automaticky využijí ASP.NET Core sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="58d51-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="58d51-129">Při použití `Microsoft.AspNetCore.App` Metapackage nejsou nasazeny **žádné** prostředky z odkazovaného ASP.NET Core balíčků NuGet spolu s aplikací&mdash;, ASP.NET Core sdílená architektura obsahuje tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="58d51-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="58d51-130">Prostředky ve sdíleném rozhraní jsou předkompilovány pro zlepšení času spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="58d51-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="58d51-131">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="58d51-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="58d51-132">Následující soubor projektu odkazuje na `Microsoft.AspNetCore.App` metapackage pro ASP.NET Core a představuje typickou šablonu ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="58d51-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

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

<span data-ttu-id="58d51-133">Předchozí kód představuje typickou šablonu ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="58d51-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="58d51-134">Neurčuje číslo verze odkazu na `Microsoft.AspNetCore.App` balíček.</span><span class="sxs-lookup"><span data-stu-id="58d51-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="58d51-135">Není-li zadána verze, je [implicitní](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) verze určena sadou SDK, tedy `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="58d51-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="58d51-136">Doporučujeme, abyste se spoléhali na implicitní verzi určenou sadou SDK a neexplicitně nastavoval číslo verze na odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="58d51-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="58d51-137">Pokud máte na tento přístup nějaké dotazy, ponechte si komentář GitHubu v [diskuzi o implicitní verzi Microsoft. AspNetCore. app](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="58d51-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="58d51-138">Implicitní verze je nastavená na `major.minor.0` pro přenosné aplikace.</span><span class="sxs-lookup"><span data-stu-id="58d51-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="58d51-139">Mechanismus pro přeposílání sdíleného rozhraní spustí aplikaci na nejnovější kompatibilní verzi z nainstalovaných sdílených rozhraní.</span><span class="sxs-lookup"><span data-stu-id="58d51-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="58d51-140">Aby bylo zaručeno, že stejná verze se používá ve vývoji, testování a produkčním prostředí, zajistěte, aby byla stejná verze sdíleného rozhraní nainstalovaná ve všech prostředích.</span><span class="sxs-lookup"><span data-stu-id="58d51-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="58d51-141">U samostatných aplikací je implicitní číslo verze nastaveno na sdílené rozhraní, které `major.minor.patch` je součástí sady SDK.</span><span class="sxs-lookup"><span data-stu-id="58d51-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="58d51-142">Zadáním čísla verze na `Microsoft.AspNetCore.App` odkaz nezaručujete, že bude zvolena verze sdíleného rozhraní. **not**</span><span class="sxs-lookup"><span data-stu-id="58d51-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="58d51-143">Například Předpokládejme, že je zadána verze "2.2.1", ale je nainstalována "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="58d51-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="58d51-144">V takovém případě bude aplikace používat "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="58d51-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="58d51-145">I když se to nedoporučuje, můžete zablokovat přeposlání (oprava nebo podverze).</span><span class="sxs-lookup"><span data-stu-id="58d51-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="58d51-146">Další informace o tom, jak integrovat hostitele dotnet a jak nakonfigurovat jeho chování, najdete v tématu [dotnet Host – přesměrování](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="58d51-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="58d51-147">`<Project Sdk`musí být nastaven na `Microsoft.NET.Sdk.Web` hodnotu pro použití implicitní verze `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="58d51-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="58d51-148">When `<Project Sdk="Microsoft.NET.Sdk">` (bez koncového `.Web`) se používá:</span><span class="sxs-lookup"><span data-stu-id="58d51-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="58d51-149">Vygeneruje se následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="58d51-149">The following warning is generated:</span></span>

  <span data-ttu-id="58d51-150">*Upozornění NU1604: závislost projektu Microsoft. AspNetCore. app neobsahuje žádné zahrnutí dolní meze. Zahrňte do verze závislosti dolní mez, aby se zajistilo konzistentní výsledky obnovení.*</span><span class="sxs-lookup"><span data-stu-id="58d51-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="58d51-151">Jedná se o známý problém se sadou .NET Core 2,1 SDK.</span><span class="sxs-lookup"><span data-stu-id="58d51-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="58d51-152">Aktualizovat ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58d51-152">Update ASP.NET Core</span></span>

<span data-ttu-id="58d51-153">`Microsoft.AspNetCore.App` [Metapackage](/dotnet/core/packages#metapackages) není tradiční balíček, který je aktualizovaný z NuGet.</span><span class="sxs-lookup"><span data-stu-id="58d51-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="58d51-154">Podobně jako `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` představuje sdílený modul runtime, který má sémantiku se speciálními verzemi zpracovávanými mimo NuGet.</span><span class="sxs-lookup"><span data-stu-id="58d51-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="58d51-155">Další informace najdete v tématu [balíčky, metabalíčky a rozhraní](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="58d51-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="58d51-156">Aktualizace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="58d51-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="58d51-157">Ve vývojových počítačích a serverech sestavení: Stáhněte a nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="58d51-157">On development machines and build servers: Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="58d51-158">Na serverech nasazení: Stáhněte a nainstalujte [modul runtime .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="58d51-158">On deployment servers: Download and install the [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>

 <span data-ttu-id="58d51-159">Aplikace přestanou až na nejnovější nainstalovanou verzi při restartu aplikace.</span><span class="sxs-lookup"><span data-stu-id="58d51-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="58d51-160">V souboru projektu není nutné aktualizovat číslo `Microsoft.AspNetCore.App` verze.</span><span class="sxs-lookup"><span data-stu-id="58d51-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="58d51-161">Další informace najdete v tématu [předejte vám aplikace závislé na rozhraní](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="58d51-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="58d51-162">Pokud se vaše aplikace dřív `Microsoft.AspNetCore.All`používala, přečtěte si téma [migrace z Microsoft. AspNetCore. All do Microsoft. AspNetCore. app](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="58d51-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
