---
title: Microsoft. AspNetCore. app Metapackage pro ASP.NET Core
author: Rick-Anderson
description: Sdílené rozhraní Microsoft. AspNetCore. app
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 8435445890ce00f33ab9a8692f5442b1609192da
ms.sourcegitcommit: 8a36be1bfee02eba3b07b7a86085ec25c38bae6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219112"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="f8cd4-103">Microsoft. AspNetCore. app pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8cd4-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="f8cd4-104">Rozhraní ASP.NET Core Shared Framework`Microsoft.AspNetCore.App`() obsahuje sestavení vyvinutá a podporovaná společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="f8cd4-105">`Microsoft.AspNetCore.App`se nainstaluje, když je nainstalovaná [sada .NET Core 3,0 nebo novější SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="f8cd4-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="f8cd4-106">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a zahrnují komponentu modulu runtime a sadu targeting pack.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="f8cd4-107">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="f8cd4-108">Projekty, které cílí `Microsoft.NET.Sdk.Web` na sadu SDK, implicitně odkazují na `Microsoft.AspNetCore.App` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="f8cd4-109">Pro tyto projekty nejsou vyžadovány žádné další odkazy:</span><span class="sxs-lookup"><span data-stu-id="f8cd4-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="f8cd4-110">ASP.NET Core sdílené rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f8cd4-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="f8cd4-111">Neobsahuje závislosti třetích stran.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="f8cd4-112">Zahrnuje všechny podporované balíčky ASP.NET Core týmu.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8cd4-113">Tato funkce vyžaduje ASP.NET Core 2. x cílící na rozhraní .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="f8cd4-114">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [Metapackage](/dotnet/core/packages#metapackages) pro ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f8cd4-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="f8cd4-115">Nezahrnuje závislosti třetích stran kromě [JSON.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [remotion. Linq](https://www.nuget.org/packages/Remotion.Linq/)a [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="f8cd4-116">Tyto závislosti třetích stran se považují za nezbytné, aby se zajistilo, že funkce hlavních architektur funguje.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="f8cd4-117">Zahrnuje všechny podporované balíčky ASP.NET Core týmu s výjimkou těch, které obsahují závislosti třetích stran (jiné než výše zmíněné).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="f8cd4-118">Zahrnuje všechny podporované balíčky Entity Framework Core týmu s výjimkou těch, které obsahují závislosti třetích stran (jiné než výše zmíněné).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="f8cd4-119">Do `Microsoft.AspNetCore.App` balíčku jsou zahrnuté všechny funkce ASP.NET Core 2. x a Entity Framework Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="f8cd4-120">Výchozí šablony projektu, které cílí na ASP.NET Core 2. x, používají tento balíček.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="f8cd4-121">Doporučujeme, aby byly `Microsoft.AspNetCore.App` aplikace cílené ASP.NET Core 2. x a Entity Framework Core 2. x použijte balíček.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="f8cd4-122">Číslo `Microsoft.AspNetCore.App` verze Metapackage představuje minimální verzi ASP.NET Core a Entity Framework Core verzi.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="f8cd4-123">`Microsoft.AspNetCore.App` Použití Metapackage poskytuje omezení verze, která chrání vaši aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f8cd4-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="f8cd4-124">Pokud je zahrnut balíček, který má přenositelný (nepřímý) závislý na balíčku v `Microsoft.AspNetCore.App`nástroji, přičemž se tato čísla verzí liší, NuGet vygeneruje chybu.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="f8cd4-125">Jiné balíčky přidané do vaší aplikace nemůžou měnit verzi balíčků zahrnutých v `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="f8cd4-126">Konzistence verzí zajišťuje spolehlivé prostředí.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="f8cd4-127">`Microsoft.AspNetCore.App`byla navržena tak, aby bránila netestovým kombinacím verzí souvisejících bitů společně ve stejné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="f8cd4-128">Aplikace, které používají `Microsoft.AspNetCore.App` Metapackage, automaticky využijí ASP.NET Core sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="f8cd4-129">Při použití `Microsoft.AspNetCore.App` Metapackage nejsou nasazeny **žádné** prostředky z odkazovaného ASP.NET Core balíčků NuGet spolu s aplikací&mdash;, ASP.NET Core sdílená architektura obsahuje tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="f8cd4-130">Prostředky ve sdíleném rozhraní jsou předkompilovány pro zlepšení času spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="f8cd4-131">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="f8cd4-132">Následující soubor projektu odkazuje na `Microsoft.AspNetCore.App` Metapackage pro ASP.NET Core a představuje typickou šablonu ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="f8cd4-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

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

<span data-ttu-id="f8cd4-133">Předchozí kód představuje typickou šablonu ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="f8cd4-134">Neurčuje číslo verze odkazu na `Microsoft.AspNetCore.App` balíček.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="f8cd4-135">Není-li zadána verze, je [implicitní](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) verze určena sadou SDK, tedy `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="f8cd4-136">Doporučujeme, abyste se spoléhali na implicitní verzi určenou sadou SDK a neexplicitně nastavoval číslo verze na odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="f8cd4-137">Pokud máte na tento přístup nějaké dotazy, ponechte si komentář GitHubu v [diskuzi o implicitní verzi Microsoft. AspNetCore. app](https://github.com/aspnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/aspnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="f8cd4-138">Implicitní verze je nastavená na `major.minor.0` pro přenosné aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="f8cd4-139">Mechanismus pro přeposílání sdíleného rozhraní spustí aplikaci na nejnovější kompatibilní verzi z nainstalovaných sdílených rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="f8cd4-140">Aby bylo zaručeno, že stejná verze se používá ve vývoji, testování a produkčním prostředí, zajistěte, aby byla stejná verze sdíleného rozhraní nainstalovaná ve všech prostředích.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="f8cd4-141">U samostatných aplikací je implicitní číslo verze nastaveno na `major.minor.patch` sdílené rozhraní, které je součástí sady SDK.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="f8cd4-142">Zadáním čísla verze na `Microsoft.AspNetCore.App` odkaz **nezaručujete**, že bude zvolena verze sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="f8cd4-143">Například Předpokládejme, že je zadána verze "2.2.1", ale je nainstalována "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="f8cd4-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="f8cd4-144">V takovém případě bude aplikace používat "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="f8cd4-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="f8cd4-145">I když se to nedoporučuje, můžete zablokovat přeposlání (oprava nebo podverze).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="f8cd4-146">Další informace o tom, jak integrovat hostitele dotnet a jak nakonfigurovat jeho chování, najdete v tématu [dotnet Host – přesměrování](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f8cd4-147">`<Project Sdk`musí být nastaven na `Microsoft.NET.Sdk.Web` hodnotu pro použití implicitní verze `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="f8cd4-148">When `<Project Sdk="Microsoft.NET.Sdk">` (bez `.Web`koncového) se používá:</span><span class="sxs-lookup"><span data-stu-id="f8cd4-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="f8cd4-149">Vygeneruje se následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="f8cd4-149">The following warning is generated:</span></span>

  <span data-ttu-id="f8cd4-150">*Upozornění NU1604: Závislost projektu Microsoft. AspNetCore. app neobsahuje zahrnutí dolní meze. Zahrňte do verze závislosti dolní mez, aby se zajistilo konzistentní výsledky obnovení.*</span><span class="sxs-lookup"><span data-stu-id="f8cd4-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="f8cd4-151">Jedná se o známý problém se sadou .NET Core 2,1 SDK.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="f8cd4-152">Aktualizovat ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8cd4-152">Update ASP.NET Core</span></span>

<span data-ttu-id="f8cd4-153">Metapackage není tradiční balíček, který je aktualizovaný z NuGet. [](/dotnet/core/packages#metapackages) `Microsoft.AspNetCore.App`</span><span class="sxs-lookup"><span data-stu-id="f8cd4-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="f8cd4-154">Podobně jako `Microsoft.AspNetCore.App` , představuje sdílený modul runtime, který má sémantiku se speciálními verzemi zpracovávanými mimo NuGet. `Microsoft.NETCore.App`</span><span class="sxs-lookup"><span data-stu-id="f8cd4-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="f8cd4-155">Další informace najdete v tématu [balíčky, metabalíčky a rozhraní](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="f8cd4-156">Aktualizace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f8cd4-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="f8cd4-157">Ve vývojových počítačích a serverech sestavení: Stáhněte a nainstalujte [.NET Core SDK](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-157">On development machines and build servers: Download and install the [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="f8cd4-158">Na serverech nasazení: Stáhněte a nainstalujte [modul runtime .NET Core](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-158">On deployment servers: Download and install the [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>

 <span data-ttu-id="f8cd4-159">Aplikace přestanou až na nejnovější nainstalovanou verzi při restartu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="f8cd4-160">V souboru projektu není nutné aktualizovat `Microsoft.AspNetCore.App` číslo verze.</span><span class="sxs-lookup"><span data-stu-id="f8cd4-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="f8cd4-161">Další informace najdete v tématu [předejte vám aplikace závislé na rozhraní](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="f8cd4-162">Pokud se vaše aplikace dřív `Microsoft.AspNetCore.All`používala, přečtěte si téma [migrace z Microsoft. AspNetCore. All do Microsoft. AspNetCore. app](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="f8cd4-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
