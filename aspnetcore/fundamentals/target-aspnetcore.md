---
title: Použití rozhraní API ASP.NET Core v knihovně tříd
author: scottaddie
description: Naučte se používat rozhraní API ASP.NET Core v knihovně tříd.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 72096fc2f03033dfe8325b5129e074913a2fbd1f
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463886"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="5519a-103">Použití rozhraní API ASP.NET Core v knihovně tříd</span><span class="sxs-lookup"><span data-stu-id="5519a-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="5519a-104">[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5519a-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5519a-105">Tento dokument poskytuje pokyny pro použití rozhraní API ASP.NET Core v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="5519a-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="5519a-106">Všechny ostatní doprovodné materiály ke knihovně naleznete v tématu [Open-Source doprovodné](/dotnet/standard/library-guidance/)materiály.</span><span class="sxs-lookup"><span data-stu-id="5519a-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="5519a-107">Určete, které verze ASP.NET Core se mají podporovat.</span><span class="sxs-lookup"><span data-stu-id="5519a-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="5519a-108">ASP.NET Core dodržuje [zásady podpory .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="5519a-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="5519a-109">Při určování, které verze ASP.NET Core se mají podporovat v knihovně, se podívejte na zásady podpory.</span><span class="sxs-lookup"><span data-stu-id="5519a-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="5519a-110">Knihovna by měla:</span><span class="sxs-lookup"><span data-stu-id="5519a-110">A library should:</span></span>

* <span data-ttu-id="5519a-111">Udělejte úsilí, aby podporovalo všechny ASP.NET Core verze klasifikované jako *Dlouhodobá podpora* (LTS).</span><span class="sxs-lookup"><span data-stu-id="5519a-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="5519a-112">Nejedná se o povinnost podporovat ASP.NET Core verze klasifikované jako *konec života* (konce řádku).</span><span class="sxs-lookup"><span data-stu-id="5519a-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="5519a-113">V rámci verze Preview ASP.NET Core k dispozici jsou zásadní změny publikované v úložišti GitHub [ASPNET/oznámení](https://github.com/aspnet/Announcements/issues) .</span><span class="sxs-lookup"><span data-stu-id="5519a-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="5519a-114">Testování kompatibility knihoven se dá provádět jako vývoj funkcí rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="5519a-115">Použití sdíleného rozhraní ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5519a-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="5519a-116">S vydáním .NET Core 3,0 se už mnoho ASP.NET Corech sestavení nepublikují do NuGet jako balíčky.</span><span class="sxs-lookup"><span data-stu-id="5519a-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="5519a-117">Místo toho jsou sestavení zahrnuta ve `Microsoft.AspNetCore.App` sdíleném rozhraním, které je nainstalováno s instalačními programy .NET Core SDK a modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="5519a-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="5519a-118">Seznam balíčků, které už nejsou publikované, najdete v tématu [odebrání zastaralých odkazů na balíčky](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="5519a-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="5519a-119">Od .NET Core 3,0 projekty, které používají `Microsoft.NET.Sdk.Web` MSBuild SDK, implicitně odkazují na sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="5519a-120">Projekty používající `Microsoft.NET.Sdk` nebo `Microsoft.NET.Sdk.Razor` SDK musí odkazovat ASP.NET Core na použití rozhraní API ASP.NET Core ve sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="5519a-121">Chcete-li odkazovat ASP.NET Core, přidejte následující `<FrameworkReference>` prvek do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="5519a-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="5519a-122">Odkazování ASP.NET Core tímto způsobem je podporováno pouze pro projekty cílené na .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="5519a-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-opno-locblazor-extensibility"></a><span data-ttu-id="5519a-123">Zahrnout rozšíření Blazor</span><span class="sxs-lookup"><span data-stu-id="5519a-123">Include Blazor extensibility</span></span>

Blazor<span data-ttu-id="5519a-124"> podporuje WebAssembly (WASM) a [hostitelské modely](xref:blazor/hosting-models)serveru.</span><span class="sxs-lookup"><span data-stu-id="5519a-124"> supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="5519a-125">Pokud neexistuje konkrétní důvod, knihovna [komponent Razor](xref:blazor/components) by měla podporovat oba modely hostování.</span><span class="sxs-lookup"><span data-stu-id="5519a-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="5519a-126">Knihovna komponent Razor musí používat [sadu Microsoft. NET. SDK. Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="5519a-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="5519a-127">Podpora obou modelů hostování</span><span class="sxs-lookup"><span data-stu-id="5519a-127">Support both hosting models</span></span>

<span data-ttu-id="5519a-128">Pro podporu spotřeby komponent Razor z [Blazorho serveru](xref:blazor/hosting-models#blazor-server) i [Blazor projektů WASM](xref:blazor/hosting-models#blazor-webassembly) použijte následující pokyny pro Editor.</span><span class="sxs-lookup"><span data-stu-id="5519a-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5519a-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5519a-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5519a-130">Použijte šablonu projektu **knihovny tříd Razor** .</span><span class="sxs-lookup"><span data-stu-id="5519a-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="5519a-131">Zaškrtávací políčko pro **stránky podpory a zobrazení** šablony je třeba zrušit výběr.</span><span class="sxs-lookup"><span data-stu-id="5519a-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5519a-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5519a-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5519a-133">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5519a-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5519a-134">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5519a-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5519a-135">Použijte šablonu projektu **knihovny tříd Razor** .</span><span class="sxs-lookup"><span data-stu-id="5519a-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="5519a-136">Projekt vygenerovaný ze šablony provádí následující akce:</span><span class="sxs-lookup"><span data-stu-id="5519a-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="5519a-137">Cíle .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="5519a-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="5519a-138">Nastaví vlastnost `RazorLangVersion` na hodnotu `3.0`.</span><span class="sxs-lookup"><span data-stu-id="5519a-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="5519a-139">`3.0` je výchozí hodnota pro .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="5519a-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="5519a-140">Přidá následující odkazy na balíčky:</span><span class="sxs-lookup"><span data-stu-id="5519a-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="5519a-141">Microsoft. AspNetCore. Components</span><span class="sxs-lookup"><span data-stu-id="5519a-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="5519a-142">Microsoft. AspNetCore. Components. Web</span><span class="sxs-lookup"><span data-stu-id="5519a-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="5519a-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5519a-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="5519a-144">Podpora konkrétního modelu hostování</span><span class="sxs-lookup"><span data-stu-id="5519a-144">Support a specific hosting model</span></span>

<span data-ttu-id="5519a-145">Je mnohem méně běžné podporovat model hostování jednoho Blazor.</span><span class="sxs-lookup"><span data-stu-id="5519a-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="5519a-146">Například pro podporu spotřeby komponent Razor z [Blazorch projektů serveru](xref:blazor/hosting-models#blazor-server) :</span><span class="sxs-lookup"><span data-stu-id="5519a-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="5519a-147">Cílová platforma .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="5519a-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="5519a-148">Přidejte `<FrameworkReference>` element pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="5519a-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5519a-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="5519a-150">Další informace o knihovnách obsahujících komponenty Razor naleznete v tématu [ASP.NET Core knihovny tříd komponent Razor](xref:blazor/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="5519a-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="5519a-151">Zahrnutí rozšiřitelnosti MVC</span><span class="sxs-lookup"><span data-stu-id="5519a-151">Include MVC extensibility</span></span>

<span data-ttu-id="5519a-152">V této části najdete popis doporučení pro knihovny, které zahrnují:</span><span class="sxs-lookup"><span data-stu-id="5519a-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="5519a-153">Zobrazení a Razor Pages Razor</span><span class="sxs-lookup"><span data-stu-id="5519a-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="5519a-154">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="5519a-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="5519a-155">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="5519a-155">View components</span></span>](#view-components)

<span data-ttu-id="5519a-156">Tato část se nezabývá cílením na podporu více verzí MVC.</span><span class="sxs-lookup"><span data-stu-id="5519a-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="5519a-157">Pokyny k podpoře více verzí ASP.NET Core najdete v tématu [Podpora více verzí ASP.NET Core](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="5519a-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="5519a-158">Zobrazení a Razor Pages Razor</span><span class="sxs-lookup"><span data-stu-id="5519a-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="5519a-159">Projekt, který obsahuje [zobrazení](xref:mvc/views/overview) nebo [Razor Pages](xref:razor-pages/index) Razor, musí používat [sadu Microsoft. NET. SDK. Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="5519a-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="5519a-160">Pokud je projekt cílen na rozhraní .NET Core 3. x, vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="5519a-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="5519a-161">Vlastnost `AddRazorSupportForMvc` MSBuild nastavená na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="5519a-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="5519a-162">Element `<FrameworkReference>` pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="5519a-163">Šablona projektu **knihovny tříd Razor** splňuje předchozí požadavky pro projekty cílené na .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="5519a-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="5519a-164">Pro editor použijte následující pokyny.</span><span class="sxs-lookup"><span data-stu-id="5519a-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5519a-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5519a-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5519a-166">Použijte šablonu projektu **knihovny tříd Razor** .</span><span class="sxs-lookup"><span data-stu-id="5519a-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="5519a-167">Je třeba vybrat zaškrtávací políčko **stránky podpory a zobrazení** šablony.</span><span class="sxs-lookup"><span data-stu-id="5519a-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5519a-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5519a-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5519a-169">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5519a-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5519a-170">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5519a-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5519a-171">V tuto chvíli není podporována žádná podpora šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="5519a-171">No project template support at this time.</span></span>

---

<span data-ttu-id="5519a-172">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5519a-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="5519a-173">Pokud se místo toho projekt cílí .NET Standard, je vyžadován odkaz na balíček [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) .</span><span class="sxs-lookup"><span data-stu-id="5519a-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="5519a-174">Balíček `Microsoft.AspNetCore.Mvc` přesunut do sdíleného rozhraní v ASP.NET Core 3,0, a proto již není publikován.</span><span class="sxs-lookup"><span data-stu-id="5519a-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="5519a-175">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5519a-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="5519a-176">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="5519a-176">Tag Helpers</span></span>

<span data-ttu-id="5519a-177">Projekt, který obsahuje [pomocníky značek](xref:mvc/views/tag-helpers/intro) , by měl používat sadu SDK `Microsoft.NET.Sdk`.</span><span class="sxs-lookup"><span data-stu-id="5519a-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="5519a-178">Pokud cílíte na rozhraní .NET Core 3. x, přidejte `<FrameworkReference>` element pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="5519a-179">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5519a-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="5519a-180">Pokud cílíte .NET Standard (pro podporu verzí starších než ASP.NET Core 3. x), přidejte odkaz na balíček do [Microsoft. AspNetCore. Mvc. Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="5519a-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="5519a-181">Balíček `Microsoft.AspNetCore.Mvc.Razor` přesunut do sdíleného rozhraní a proto již není publikován.</span><span class="sxs-lookup"><span data-stu-id="5519a-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="5519a-182">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5519a-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="5519a-183">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="5519a-183">View components</span></span>

<span data-ttu-id="5519a-184">Projekt, který obsahuje [komponenty zobrazení](xref:mvc/views/view-components) , by měl používat sadu SDK `Microsoft.NET.Sdk`.</span><span class="sxs-lookup"><span data-stu-id="5519a-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="5519a-185">Pokud cílíte na rozhraní .NET Core 3. x, přidejte `<FrameworkReference>` element pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="5519a-186">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5519a-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="5519a-187">Pokud cílíte .NET Standard (pro podporu verzí starších než ASP.NET Core 3. x), přidejte odkaz na balíček do [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="5519a-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="5519a-188">Balíček `Microsoft.AspNetCore.Mvc.ViewFeatures` přesunut do sdíleného rozhraní a proto již není publikován.</span><span class="sxs-lookup"><span data-stu-id="5519a-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="5519a-189">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5519a-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="5519a-190">Podpora více verzí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5519a-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="5519a-191">Aby bylo možné vytvořit knihovnu, která podporuje více variant ASP.NET Core, vyžaduje se cílení na více verzí.</span><span class="sxs-lookup"><span data-stu-id="5519a-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="5519a-192">Vezměte v úvahu scénář, ve kterém musí knihovna pomocníků značek podporovat následující ASP.NET Core varianty:</span><span class="sxs-lookup"><span data-stu-id="5519a-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="5519a-193">ASP.NET Core 2,1 cílení na .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="5519a-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="5519a-194">ASP.NET Core 2. x cílící na .NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="5519a-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="5519a-195">ASP.NET Core 3. x cílící na .NET Core 3. x</span><span class="sxs-lookup"><span data-stu-id="5519a-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="5519a-196">Následující soubor projektu podporuje tyto varianty prostřednictvím vlastnosti `TargetFrameworks`:</span><span class="sxs-lookup"><span data-stu-id="5519a-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="5519a-197">S předchozím souborem projektu:</span><span class="sxs-lookup"><span data-stu-id="5519a-197">With the preceding project file:</span></span>

* <span data-ttu-id="5519a-198">Balíček `Markdig` se přidá pro všechny uživatele.</span><span class="sxs-lookup"><span data-stu-id="5519a-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="5519a-199">Odkaz na [Microsoft. AspNetCore. Mvc. Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) je přidán pro uživatele cílící na .NET Framework 4.6.1 nebo novější nebo .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="5519a-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="5519a-200">2\.1.0 verze balíčku spolupracuje s ASP.NET Core 2,2 z důvodu zpětné kompatibility.</span><span class="sxs-lookup"><span data-stu-id="5519a-200">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="5519a-201">Na sdílené rozhraní se odkazuje pro uživatele, kteří cílí na .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="5519a-201">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="5519a-202">Balíček `Microsoft.AspNetCore.Mvc.Razor` je součástí sdílené architektury.</span><span class="sxs-lookup"><span data-stu-id="5519a-202">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="5519a-203">Alternativně můžete .NET Standard 2,0 cílit místo cílení na rozhraní .NET Core 2,1 i .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="5519a-203">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="5519a-204">V předchozím souboru projektu jsou k dispozici následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="5519a-204">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="5519a-205">Vzhledem k tomu, že knihovna obsahuje pouze pomocníky značek, je snazší zaměřit se na konkrétní platformy, na kterých ASP.NET Core běžet: .NET Core a .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5519a-205">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="5519a-206">Pomocníky značek nemůžou používat jiné cílové architektury kompatibilní s .NET Standard 2,0, jako je Unity, UWP a Xamarin.</span><span class="sxs-lookup"><span data-stu-id="5519a-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="5519a-207">Použití .NET Standard 2,0 z .NET Framework obsahuje některé problémy, které byly vyřešeny v .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="5519a-207">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="5519a-208">Pro uživatele můžete vylepšit prostředí pomocí .NET Framework 4.6.1 prostřednictvím 4.7.1, a to tak, že zacílíte .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="5519a-208">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="5519a-209">Pokud vaše knihovna potřebuje volat rozhraní API specifická pro platformu, místo .NET Standard pro konkrétní cílové implementace .NET.</span><span class="sxs-lookup"><span data-stu-id="5519a-209">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="5519a-210">Další informace najdete v tématu [cílení na více](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)platforem.</span><span class="sxs-lookup"><span data-stu-id="5519a-210">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="5519a-211">Použití rozhraní API, které se nezměnilo</span><span class="sxs-lookup"><span data-stu-id="5519a-211">Use an API that hasn't changed</span></span>

<span data-ttu-id="5519a-212">Představte si situaci, ve které upgradujete knihovnu middlewaru z .NET Core 2,2 na 3,0.</span><span class="sxs-lookup"><span data-stu-id="5519a-212">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="5519a-213">Rozhraní API middleware ASP.NET Core používaná v knihovně se mezi ASP.NET Core 2,2 a 3,0 nezměnila.</span><span class="sxs-lookup"><span data-stu-id="5519a-213">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="5519a-214">Chcete-li pokračovat v podpoře knihovny middlewaru v rozhraní .NET Core 3,0, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="5519a-214">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="5519a-215">Řiďte se [standardními pokyny ke knihovně](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="5519a-215">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="5519a-216">Přidejte odkaz na balíček pro každý balíček NuGet rozhraní API, pokud odpovídající sestavení neexistuje ve sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-216">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="5519a-217">Použití rozhraní API, které se změnilo</span><span class="sxs-lookup"><span data-stu-id="5519a-217">Use an API that changed</span></span>

<span data-ttu-id="5519a-218">Představte si situaci, ve které upgradujete knihovnu ze sady .NET Core 2,2 na .NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="5519a-218">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="5519a-219">ASP.NET Core rozhraní API, které se používá v knihovně, má zásadní [změnu](/dotnet/core/compatibility/breaking-changes) v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="5519a-219">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="5519a-220">Zvažte, zda lze knihovnu přepsat, aby nepoužívala poškozené rozhraní API ve všech verzích.</span><span class="sxs-lookup"><span data-stu-id="5519a-220">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="5519a-221">Pokud můžete knihovnu přepsat, udělejte to a pokračujte do cílení na dřívější cílové rozhraní (například .NET Standard 2,0 nebo .NET Framework 4.6.1) s odkazy na balíčky.</span><span class="sxs-lookup"><span data-stu-id="5519a-221">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="5519a-222">Pokud knihovnu nelze přepsat, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="5519a-222">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="5519a-223">Přidejte cíl pro .NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="5519a-223">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="5519a-224">Přidejte `<FrameworkReference>` element pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-224">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="5519a-225">Pomocí [direktivy preprocesoru #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) s příslušným cílovým symbolem rozhraní můžete podmíněně kompilovat kód.</span><span class="sxs-lookup"><span data-stu-id="5519a-225">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="5519a-226">Například synchronní čtení a zápisy v případě požadavků HTTP a datových proudů odpovědí jsou ve výchozím nastavení zakázány ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="5519a-226">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="5519a-227">ASP.NET Core 2,2 ve výchozím nastavení podporuje synchronní chování.</span><span class="sxs-lookup"><span data-stu-id="5519a-227">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="5519a-228">Vezměte v úvahu knihovnu middlewaru, ve které by měla být povolena synchronní čtení a zápisy v případě výskytu vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="5519a-228">Consider a middleware library in which synchronous reads and writes should be enabled where IO is occurring.</span></span> <span data-ttu-id="5519a-229">Knihovna by měla uzavřít kód, aby povolovala synchronní funkce v příslušné direktivě preprocesoru.</span><span class="sxs-lookup"><span data-stu-id="5519a-229">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="5519a-230">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5519a-230">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="5519a-231">Použití rozhraní API představeného v 3,0</span><span class="sxs-lookup"><span data-stu-id="5519a-231">Use an API introduced in 3.0</span></span>

<span data-ttu-id="5519a-232">Představte si, že chcete použít ASP.NET Core rozhraní API, které bylo zavedeno v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="5519a-232">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="5519a-233">Zvažte následující otázky:</span><span class="sxs-lookup"><span data-stu-id="5519a-233">Consider the following questions:</span></span>

1. <span data-ttu-id="5519a-234">Vyžaduje Knihovna funkce nové rozhraní API?</span><span class="sxs-lookup"><span data-stu-id="5519a-234">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="5519a-235">Může knihovna tuto funkci implementovat jiným způsobem?</span><span class="sxs-lookup"><span data-stu-id="5519a-235">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="5519a-236">Pokud knihovna funkci vyžaduje rozhraní API a neexistuje žádný způsob, jak ji implementovat na nižší úroveň:</span><span class="sxs-lookup"><span data-stu-id="5519a-236">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="5519a-237">Pouze cílový .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="5519a-237">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="5519a-238">Přidejte `<FrameworkReference>` element pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-238">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="5519a-239">Pokud knihovna může implementovat funkci jiným způsobem:</span><span class="sxs-lookup"><span data-stu-id="5519a-239">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="5519a-240">Přidejte .NET Core 3. x jako cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="5519a-240">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="5519a-241">Přidejte `<FrameworkReference>` element pro sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5519a-241">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="5519a-242">Pomocí [direktivy preprocesoru #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) s příslušným cílovým symbolem rozhraní můžete podmíněně kompilovat kód.</span><span class="sxs-lookup"><span data-stu-id="5519a-242">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="5519a-243">Například následující pomocná značka používá rozhraní <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> představené v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="5519a-243">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="5519a-244">Spotřebitelé cílící na .NET Core 3,0 spustí cestu kódu definovanou symbolem `NETCOREAPP3_0` Target Framework.</span><span class="sxs-lookup"><span data-stu-id="5519a-244">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="5519a-245">Typ parametru konstruktoru pomocné rutiny značky změny <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> pro .NET Core 2,1 a .NET Framework 4.6.1 spotřebitelé.</span><span class="sxs-lookup"><span data-stu-id="5519a-245">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="5519a-246">Tato změna byla nezbytná, protože ASP.NET Core 3,0 označil `IHostingEnvironment` jako zastaralý a doporučuje se `IWebHostEnvironment` jako náhrada.</span><span class="sxs-lookup"><span data-stu-id="5519a-246">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

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

<span data-ttu-id="5519a-247">Tento scénář pomocníka značek podporuje následující soubor projektu s více cíli:</span><span class="sxs-lookup"><span data-stu-id="5519a-247">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="5519a-248">Použití rozhraní API odebraného ze sdíleného rozhraní</span><span class="sxs-lookup"><span data-stu-id="5519a-248">Use an API removed from the shared framework</span></span>

<span data-ttu-id="5519a-249">Chcete-li použít ASP.NET Core sestavení, které bylo odebráno ze sdíleného rozhraní, přidejte příslušný odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="5519a-249">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="5519a-250">Seznam balíčků odebraných ze sdíleného rozhraní v ASP.NET Core 3,0 najdete v tématu [odebrání zastaralých odkazů na balíčky](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="5519a-250">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="5519a-251">Například pro přidání klienta webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="5519a-251">For example, to add the web API client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="5519a-252">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="5519a-252">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="5519a-253">Podpora implementace rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="5519a-253">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="5519a-254">Zásady podpory rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="5519a-254">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)