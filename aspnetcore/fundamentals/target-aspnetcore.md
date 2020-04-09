---
title: Použití ASP.NET základních api v knihovně tříd
author: scottaddie
description: Přečtěte si, jak používat ASP.NET základní api v knihovně tříd.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977194"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="3dc38-103">Použití ASP.NET základních api v knihovně tříd</span><span class="sxs-lookup"><span data-stu-id="3dc38-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="3dc38-104">Podle [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="3dc38-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3dc38-105">Tento dokument obsahuje pokyny pro použití ASP.NET základní api v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="3dc38-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="3dc38-106">Všechny ostatní pokyny ke knihovně najdete [v tématu Pokyny k knihovně s otevřeným zdrojovým kódem](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="3dc38-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="3dc38-107">Určení, které verze ASP.NET Core podporují</span><span class="sxs-lookup"><span data-stu-id="3dc38-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="3dc38-108">ASP.NET Core dodržuje [zásady podpory jádra .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="3dc38-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="3dc38-109">Při určování, které verze ASP.NET Core pro podporu v knihovně, naleznete v zásadách podpory.</span><span class="sxs-lookup"><span data-stu-id="3dc38-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="3dc38-110">Knihovna by měla:</span><span class="sxs-lookup"><span data-stu-id="3dc38-110">A library should:</span></span>

* <span data-ttu-id="3dc38-111">Vynassnažit se podporovat všechny ASP.NET základní verze klasifikované jako *dlouhodobá podpora* (LTS).</span><span class="sxs-lookup"><span data-stu-id="3dc38-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="3dc38-112">Necítí povinnost podporovat ASP.NET core verze klasifikované jako *Konec životnosti* (EOL).</span><span class="sxs-lookup"><span data-stu-id="3dc38-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="3dc38-113">Jako náhled verze ASP.NET Core jsou k dispozici, narušující změny jsou zveřejněny v úložišti [GitHub aspnet/Announcements.](https://github.com/aspnet/Announcements/issues)</span><span class="sxs-lookup"><span data-stu-id="3dc38-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="3dc38-114">Testování kompatibility knihoven lze provádět při vývoji funkcí architektury.</span><span class="sxs-lookup"><span data-stu-id="3dc38-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="3dc38-115">Použití sdíleného rámce ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dc38-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="3dc38-116">S vydáním .NET Core 3.0 mnoho ASP.NET sestavení core již nejsou publikovány nuget jako balíčky.</span><span class="sxs-lookup"><span data-stu-id="3dc38-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="3dc38-117">Místo toho jsou sestavení zahrnuta do `Microsoft.AspNetCore.App` sdíleného rámce, který je nainstalován s instalačními programy .NET Core SDK a runtime.</span><span class="sxs-lookup"><span data-stu-id="3dc38-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="3dc38-118">Seznam balíčků, které již nejsou publikovány, naleznete v tématu [Odebrání zastaralých odkazů na balíčky](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="3dc38-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="3dc38-119">Od rozhraní .NET Core 3.0 `Microsoft.NET.Sdk.Web` projekty pomocí sady MSBuild SDK implicitně odkazují na sdílený rámec.</span><span class="sxs-lookup"><span data-stu-id="3dc38-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="3dc38-120">Projekty `Microsoft.NET.Sdk` používající sadu `Microsoft.NET.Sdk.Razor` SDK musí odkazovat ASP.NET core, aby bylo nutné používat rozhraní API ASP.NET jádra ve sdíleném rámci.</span><span class="sxs-lookup"><span data-stu-id="3dc38-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="3dc38-121">Chcete-li odkazovat ASP.NET `<FrameworkReference>` jádro, přidejte do souboru projektu následující prvek:</span><span class="sxs-lookup"><span data-stu-id="3dc38-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="3dc38-122">Odkazování ASP.NET jádra tímto způsobem je podporováno pouze pro projekty zaměřené na rozhraní .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3dc38-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="3dc38-123">Včetně rozšiřitelnosti Blazoru</span><span class="sxs-lookup"><span data-stu-id="3dc38-123">Include Blazor extensibility</span></span>

<span data-ttu-id="3dc38-124">Blazor podporuje webassembly (WASM) a server [hosting modely](xref:blazor/hosting-models).</span><span class="sxs-lookup"><span data-stu-id="3dc38-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="3dc38-125">Pokud neexistuje konkrétní důvod, proč ne, knihovna [komponent Razor](xref:blazor/components) by měla podporovat oba hostitelské modely.</span><span class="sxs-lookup"><span data-stu-id="3dc38-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="3dc38-126">Knihovna komponent Razor musí používat sadu [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="3dc38-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="3dc38-127">Podpora obou hostingových modelů</span><span class="sxs-lookup"><span data-stu-id="3dc38-127">Support both hosting models</span></span>

<span data-ttu-id="3dc38-128">Chcete-li podpořit spotřebu komponent Razor z projektů [Blazor Server](xref:blazor/hosting-models#blazor-server) i [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) použijte následující pokyny pro váš editor.</span><span class="sxs-lookup"><span data-stu-id="3dc38-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dc38-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dc38-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3dc38-130">Použijte šablonu projektu **Knihovna tříd Razor.**</span><span class="sxs-lookup"><span data-stu-id="3dc38-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="3dc38-131">Zaškrtávací políčko **Stránky podpory a zobrazení** šablony by mělo být zaškrtnuto.</span><span class="sxs-lookup"><span data-stu-id="3dc38-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dc38-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dc38-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3dc38-133">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3dc38-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3dc38-134">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3dc38-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3dc38-135">Použijte šablonu projektu **Knihovna tříd Razor.**</span><span class="sxs-lookup"><span data-stu-id="3dc38-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="3dc38-136">Projekt generovaný ze šablony provádí následující akce:</span><span class="sxs-lookup"><span data-stu-id="3dc38-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="3dc38-137">Cíle .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="3dc38-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="3dc38-138">Nastaví `RazorLangVersion` vlastnost `3.0`na .</span><span class="sxs-lookup"><span data-stu-id="3dc38-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="3dc38-139">`3.0`je výchozí hodnota pro rozhraní .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3dc38-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="3dc38-140">Přidá následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="3dc38-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="3dc38-141">Microsoft.AspNetCore.Components</span><span class="sxs-lookup"><span data-stu-id="3dc38-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="3dc38-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="3dc38-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="3dc38-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dc38-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="3dc38-144">Podpora konkrétního modelu hostování</span><span class="sxs-lookup"><span data-stu-id="3dc38-144">Support a specific hosting model</span></span>

<span data-ttu-id="3dc38-145">Je mnohem méně časté podporovat jeden model hostingu Blazor.</span><span class="sxs-lookup"><span data-stu-id="3dc38-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="3dc38-146">Jako příklad pro podporu spotřeby komponent Razor z projektů [Blazor Server](xref:blazor/hosting-models#blazor-server) pouze:</span><span class="sxs-lookup"><span data-stu-id="3dc38-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="3dc38-147">Cíl .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3dc38-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="3dc38-148">Přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3dc38-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="3dc38-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dc38-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="3dc38-150">Další informace o knihovnách obsahujících komponenty Razor naleznete [v tématu ASP.NET knihovny tříd komponent Core Razor](xref:blazor/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="3dc38-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="3dc38-151">Zahrnout rozšiřitelnost MVC</span><span class="sxs-lookup"><span data-stu-id="3dc38-151">Include MVC extensibility</span></span>

<span data-ttu-id="3dc38-152">Tato část popisuje doporučení pro knihovny, které zahrnují:</span><span class="sxs-lookup"><span data-stu-id="3dc38-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="3dc38-153">Zobrazení břitva nebo Razor Stránky</span><span class="sxs-lookup"><span data-stu-id="3dc38-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="3dc38-154">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="3dc38-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="3dc38-155">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="3dc38-155">View components</span></span>](#view-components)

<span data-ttu-id="3dc38-156">Tato část nepopisuje vícenásobné cílení pro podporu více verzí MVC.</span><span class="sxs-lookup"><span data-stu-id="3dc38-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="3dc38-157">Pokyny k podpoře více verzí ASP.NET Core naleznete v tématu [Podpora více verzí ASP.NET core](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="3dc38-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="3dc38-158">Zobrazení břitva nebo Razor Stránky</span><span class="sxs-lookup"><span data-stu-id="3dc38-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="3dc38-159">Projekt, který obsahuje [zobrazení Razor](xref:mvc/views/overview) nebo [Razor Pages,](xref:razor-pages/index) musí používat sadu [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="3dc38-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="3dc38-160">Pokud se projekt zaměřuje na rozhraní .NET Core 3.x, vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="3dc38-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="3dc38-161">Vlastnost `AddRazorSupportForMvc` MSBuild nastavená na `true`.</span><span class="sxs-lookup"><span data-stu-id="3dc38-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="3dc38-162">Prvek `<FrameworkReference>` pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3dc38-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="3dc38-163">Šablona projektu **Knihovna tříd y razor** splňuje předchozí požadavky pro projekty zaměřené na rozhraní .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3dc38-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="3dc38-164">Pro editor použijte následující pokyny.</span><span class="sxs-lookup"><span data-stu-id="3dc38-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dc38-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dc38-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3dc38-166">Použijte šablonu projektu **Knihovna tříd Razor.**</span><span class="sxs-lookup"><span data-stu-id="3dc38-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="3dc38-167">Je třeba zaškrtnout **políčko Stránky podpory a zobrazení** šablony.</span><span class="sxs-lookup"><span data-stu-id="3dc38-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dc38-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dc38-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3dc38-169">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3dc38-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3dc38-170">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3dc38-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3dc38-171">V tuto chvíli není podpora šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="3dc38-171">No project template support at this time.</span></span>

---

<span data-ttu-id="3dc38-172">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dc38-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="3dc38-173">Pokud projekt místo toho cílí na standard .NET, je vyžadován odkaz na balíček [Microsoft.AspNetCore.Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc)</span><span class="sxs-lookup"><span data-stu-id="3dc38-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="3dc38-174">Balíček `Microsoft.AspNetCore.Mvc` se přesunul do sdíleného rámce v ASP.NET jádrem 3.0, a proto již není zveřejňován.</span><span class="sxs-lookup"><span data-stu-id="3dc38-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="3dc38-175">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dc38-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="3dc38-176">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="3dc38-176">Tag Helpers</span></span>

<span data-ttu-id="3dc38-177">Projekt, který obsahuje [pomocné spoje značek,](xref:mvc/views/tag-helpers/intro) by měl používat sadu `Microsoft.NET.Sdk` SDK.</span><span class="sxs-lookup"><span data-stu-id="3dc38-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="3dc38-178">Pokud cílení .NET Core 3.x, přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3dc38-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="3dc38-179">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dc38-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="3dc38-180">Pokud cílení .NET Standard (pro podporu verzí starších než ASP.NET Core 3.x), přidejte odkaz na balíček [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="3dc38-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="3dc38-181">Balíček byl `Microsoft.AspNetCore.Mvc.Razor` přesunut do sdíleného rámce, a proto již není zveřejňován.</span><span class="sxs-lookup"><span data-stu-id="3dc38-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="3dc38-182">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dc38-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="3dc38-183">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="3dc38-183">View components</span></span>

<span data-ttu-id="3dc38-184">Projekt, který obsahuje [komponenty](xref:mvc/views/view-components) `Microsoft.NET.Sdk` Zobrazení by měl používat sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="3dc38-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="3dc38-185">Pokud cílení .NET Core 3.x, přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3dc38-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="3dc38-186">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dc38-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="3dc38-187">Pokud cílení .NET Standard (pro podporu verzí starších než ASP.NET Core 3.x), přidejte odkaz na balíček [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="3dc38-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="3dc38-188">Balíček byl `Microsoft.AspNetCore.Mvc.ViewFeatures` přesunut do sdíleného rámce, a proto již není zveřejňován.</span><span class="sxs-lookup"><span data-stu-id="3dc38-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="3dc38-189">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dc38-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="3dc38-190">Podpora více verzí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dc38-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="3dc38-191">K vytvoření knihovny, která podporuje více variant ASP.NET Core, je vyžadováno více násobné cílení.</span><span class="sxs-lookup"><span data-stu-id="3dc38-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="3dc38-192">Zvažte scénář, ve kterém musí knihovna pomocníků značek podporovat následující ASP.NET varianty jádra:</span><span class="sxs-lookup"><span data-stu-id="3dc38-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="3dc38-193">ASP.NET zaměření jádra 2.1 na rozhraní .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="3dc38-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="3dc38-194">ASP.NET Cílení na jádro 2.x .NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3dc38-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="3dc38-195">ASP.NET Cílení na jádro 3.x .NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="3dc38-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="3dc38-196">Následující soubor projektu podporuje tyto `TargetFrameworks` varianty prostřednictvím vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3dc38-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="3dc38-197">S předchozím souborem projektu:</span><span class="sxs-lookup"><span data-stu-id="3dc38-197">With the preceding project file:</span></span>

* <span data-ttu-id="3dc38-198">Balíček `Markdig` je přidán pro všechny spotřebitele.</span><span class="sxs-lookup"><span data-stu-id="3dc38-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="3dc38-199">Odkaz na [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) je přidán pro spotřebitele cílení .NET Framework 4.6.1 nebo novější nebo .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="3dc38-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="3dc38-200">Verze 2.1.0 balíčku pracuje s ASP.NET Core 2.2 z důvodu zpětné kompatibility.</span><span class="sxs-lookup"><span data-stu-id="3dc38-200">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="3dc38-201">Sdílený rámec je odkazován pro spotřebitele cílení .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3dc38-201">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="3dc38-202">Balíček `Microsoft.AspNetCore.Mvc.Razor` je součástí sdíleného rámce.</span><span class="sxs-lookup"><span data-stu-id="3dc38-202">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="3dc38-203">Alternativně může být cílená norma .NET 2.0 namísto cílení na rozhraní .NET Core 2.1 a .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="3dc38-203">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="3dc38-204">S předchozím souborem projektu existují následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="3dc38-204">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="3dc38-205">Vzhledem k tomu, že knihovna obsahuje pouze pomocné spoje značek, je jednodušší cílit na konkrétní platformy, na kterých ASP.NET Core běží: .NET Core a .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="3dc38-205">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="3dc38-206">Pomocné spoje značek nelze použít jinými cílovými rámci kompatibilními se standardem .NET Standard 2.0, jako je Unity, UPW a Xamarin.</span><span class="sxs-lookup"><span data-stu-id="3dc38-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="3dc38-207">Použití rozhraní .NET Standard 2.0 z rozhraní .NET Framework má některé problémy, které byly vyřešeny v rozhraní .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="3dc38-207">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="3dc38-208">Můžete zlepšit prostředí pro spotřebitele pomocí rozhraní .NET Framework 4.6.1 až 4.7.1 cílením na rozhraní .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="3dc38-208">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="3dc38-209">Pokud vaše knihovna potřebuje volat rozhraní API specifická pro platformu, zacilte na konkrétní implementace rozhraní .NET namísto standardu .NET.</span><span class="sxs-lookup"><span data-stu-id="3dc38-209">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="3dc38-210">Další informace naleznete [v tématu Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="3dc38-210">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="3dc38-211">Použití rozhraní API, které se nezměnilo</span><span class="sxs-lookup"><span data-stu-id="3dc38-211">Use an API that hasn't changed</span></span>

<span data-ttu-id="3dc38-212">Představte si scénář, ve kterém upgradujete middlewarovou knihovnu z .NET Core 2.2 na 3.0.</span><span class="sxs-lookup"><span data-stu-id="3dc38-212">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="3dc38-213">Mezi ASP.NET Core 2.2 a 3.0 se nezměnila ASP.NET middlewarová api, která se používají v knihovně.</span><span class="sxs-lookup"><span data-stu-id="3dc38-213">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="3dc38-214">Chcete-li pokračovat v podpoře knihovny middlewaru v rozhraní .NET Core 3.0, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="3dc38-214">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="3dc38-215">Postupujte podle [standardních pokynů pro knihovnu](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="3dc38-215">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="3dc38-216">Přidejte odkaz na balíček pro každý balíček NuGet každého rozhraní API, pokud odpovídající sestavení neexistuje ve sdíleném rámci.</span><span class="sxs-lookup"><span data-stu-id="3dc38-216">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="3dc38-217">Použití rozhraní API, které se změnilo</span><span class="sxs-lookup"><span data-stu-id="3dc38-217">Use an API that changed</span></span>

<span data-ttu-id="3dc38-218">Představte si scénář, ve kterém upgradujete knihovnu z .NET Core 2.2 na .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3dc38-218">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="3dc38-219">Rozhraní ASP.NET Core API používané v knihovně má [zásadní změnu](/dotnet/core/compatibility/breaking-changes) v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3dc38-219">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="3dc38-220">Zvažte, zda lze knihovnu přepsat tak, aby nepoužívala poškozené rozhraní API ve všech verzích.</span><span class="sxs-lookup"><span data-stu-id="3dc38-220">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="3dc38-221">Pokud můžete přepsat knihovnu, učiňte tak a pokračujte v cílení na dřívější cílovou architekturu (například .NET Standard 2.0 nebo .NET Framework 4.6.1) s odkazy na balíčky.</span><span class="sxs-lookup"><span data-stu-id="3dc38-221">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="3dc38-222">Pokud knihovnu nemůžete přepsat, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="3dc38-222">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="3dc38-223">Přidejte cíl pro rozhraní .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3dc38-223">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="3dc38-224">Přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3dc38-224">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="3dc38-225">Použijte [direktivu preprocesoru #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) s příslušným symbolem cílového rozhraní k podmíněné kompilaci kódu.</span><span class="sxs-lookup"><span data-stu-id="3dc38-225">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="3dc38-226">Například synchronní čtení a zápisy na požadavek HTTP a datové proudy odpovědí jsou ve výchozím nastavení zakázány od ASP.NET Jádrem 3.0.</span><span class="sxs-lookup"><span data-stu-id="3dc38-226">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="3dc38-227">ASP.NET Core 2.2 podporuje synchronní chování ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="3dc38-227">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="3dc38-228">Zvažte middleware knihovnu, ve které synchronní čtení a zápisy by měly být povoleny, kde dochází vstupně-in.</span><span class="sxs-lookup"><span data-stu-id="3dc38-228">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="3dc38-229">Knihovna by měla uzavřete kód povolit synchronní funkce v příslušné direktivě preprocesoru.</span><span class="sxs-lookup"><span data-stu-id="3dc38-229">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="3dc38-230">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dc38-230">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="3dc38-231">Použití rozhraní API zavedeného v 3.0</span><span class="sxs-lookup"><span data-stu-id="3dc38-231">Use an API introduced in 3.0</span></span>

<span data-ttu-id="3dc38-232">Představte si, že chcete použít rozhraní API ASP.NET Core, které bylo zavedeno v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3dc38-232">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="3dc38-233">Zvažte následující otázky:</span><span class="sxs-lookup"><span data-stu-id="3dc38-233">Consider the following questions:</span></span>

1. <span data-ttu-id="3dc38-234">Je knihovna funkčně vyžadovat nové rozhraní API?</span><span class="sxs-lookup"><span data-stu-id="3dc38-234">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="3dc38-235">Může knihovna implementovat tuto funkci jiným způsobem?</span><span class="sxs-lookup"><span data-stu-id="3dc38-235">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="3dc38-236">Pokud knihovna funkčně vyžaduje rozhraní API a neexistuje žádný způsob, jak implementovat nižší úroveň:</span><span class="sxs-lookup"><span data-stu-id="3dc38-236">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="3dc38-237">Cíl .NET Core 3.x pouze.</span><span class="sxs-lookup"><span data-stu-id="3dc38-237">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="3dc38-238">Přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3dc38-238">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="3dc38-239">Pokud knihovna může implementovat funkci jiným způsobem:</span><span class="sxs-lookup"><span data-stu-id="3dc38-239">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="3dc38-240">Přidejte rozhraní .NET Core 3.x jako cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="3dc38-240">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="3dc38-241">Přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3dc38-241">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="3dc38-242">Použijte [direktivu preprocesoru #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) s příslušným symbolem cílového rozhraní k podmíněné kompilaci kódu.</span><span class="sxs-lookup"><span data-stu-id="3dc38-242">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="3dc38-243">Například následující pomocník značek používá <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> rozhraní zavedené v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3dc38-243">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="3dc38-244">Spotřebitelé cílení .NET Core 3.0 spustit `NETCOREAPP3_0` cestu kódu definované symbolem cílové ho rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3dc38-244">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="3dc38-245">Typ parametru konstruktoru pomocného <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> objektu značky se změní na pro spotřebitele rozhraní .NET Core 2.1 a .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="3dc38-245">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="3dc38-246">Tato změna byla nutná, protože `IHostingEnvironment` ASP.NET Core `IWebHostEnvironment` 3.0 označeny jako zastaralé a doporučené jako náhrada.</span><span class="sxs-lookup"><span data-stu-id="3dc38-246">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="3dc38-247">Následující víceúčelový soubor projektu podporuje tento scénář pomocníka pro značky:</span><span class="sxs-lookup"><span data-stu-id="3dc38-247">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="3dc38-248">Použití rozhraní API odebrané ze sdíleného rozhraní</span><span class="sxs-lookup"><span data-stu-id="3dc38-248">Use an API removed from the shared framework</span></span>

<span data-ttu-id="3dc38-249">Chcete-li použít ASP.NET sestavení Core, které bylo odebráno ze sdíleného rozhraní, přidejte příslušný odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="3dc38-249">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="3dc38-250">Seznam balíčků odebraných ze sdíleného rozhraní v ASP.NET jádrem 3.0 naleznete v tématu [Odebrání zastaralých odkazů na balíčky](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="3dc38-250">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="3dc38-251">Chcete-li například přidat klienta webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="3dc38-251">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="3dc38-252">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3dc38-252">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="3dc38-253">Podpora implementace rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="3dc38-253">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="3dc38-254">Zásady podpory rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="3dc38-254">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
