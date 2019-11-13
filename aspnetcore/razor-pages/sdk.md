---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Přečtěte si, jak Razor Pages v ASP.NET Core usnadňuje a produktivnější vytváření kódu v různých scénářích než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2fbdf95d02d7918236981c7fee8ebcbedf5c55e1
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963264"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="58a8b-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="58a8b-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="58a8b-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="58a8b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="58a8b-105">Přehled</span><span class="sxs-lookup"><span data-stu-id="58a8b-105">Overview</span></span>

<span data-ttu-id="58a8b-106">[!INCLUDE[](~/includes/2.1-SDK.md)] obsahuje sadu `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="58a8b-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="58a8b-107">Sada Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="58a8b-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="58a8b-108">Je vyžadován k sestavení, sbalení a publikování projektů obsahujících soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projekty založené na MVC nebo [Blazor](xref:blazor/index) .</span><span class="sxs-lookup"><span data-stu-id="58a8b-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="58a8b-109">Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor ( *. cshtml* nebo *. Razor*).</span><span class="sxs-lookup"><span data-stu-id="58a8b-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="58a8b-110">Sada Razor SDK obsahuje položky `Content` s atributy `Include` nastavenými na vzory expanze názvů `**\*.cshtml` a `**\*.razor`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="58a8b-111">Jsou publikovány vyhovující soubory.</span><span class="sxs-lookup"><span data-stu-id="58a8b-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="58a8b-112">Standardizace prostředí kolem vytváření, balení a publikování projektů, které obsahují soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projektů založených na MVC.</span><span class="sxs-lookup"><span data-stu-id="58a8b-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="58a8b-113">Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="58a8b-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="58a8b-114">Sada Razor SDK obsahuje položku `Content` s atributem `Include` nastaveným na vzor expanze názvů `**\*.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="58a8b-115">Jsou publikovány vyhovující soubory.</span><span class="sxs-lookup"><span data-stu-id="58a8b-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="58a8b-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="58a8b-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="58a8b-117">Použití sady Razor SDK</span><span class="sxs-lookup"><span data-stu-id="58a8b-117">Use the Razor SDK</span></span>

<span data-ttu-id="58a8b-118">Pro většinu webových aplikací není nutné explicitně odkazovat na sadu Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="58a8b-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58a8b-119">Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení nebo Razor Pages Razor, doporučujeme začít se šablonou projektu knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="58a8b-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="58a8b-120">RCL, který se používá k sestavování souborů Blazor ( *. Razor*), vyžaduje minimální odkaz na balíček [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) .</span><span class="sxs-lookup"><span data-stu-id="58a8b-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="58a8b-121">RCL, který se používá k vytvoření zobrazení nebo stránek (souborů *. cshtml* ) Razor, vyžaduje minimálně cílení na `netcoreapp3.0` nebo novější a má `FrameworkReference` do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v jeho souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="58a8b-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58a8b-122">Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení Razor nebo Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="58a8b-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="58a8b-123">Místo `Microsoft.NET.Sdk` použijte `Microsoft.NET.Sdk.Razor`:</span><span class="sxs-lookup"><span data-stu-id="58a8b-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="58a8b-124">K získání dalších závislostí, které jsou požadovány pro sestavování a kompilování Razor Pages zobrazení a Razor, je obvykle vyžadován odkaz na balíček `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="58a8b-125">Projekt by měl mít minimálně přidat odkazy na balíčky:</span><span class="sxs-lookup"><span data-stu-id="58a8b-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="58a8b-126">Balíček `Microsoft.AspNetCore.Razor.Design` poskytuje úlohy kompilace Razor a cíle pro projekt.</span><span class="sxs-lookup"><span data-stu-id="58a8b-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="58a8b-127">Předchozí balíčky jsou zahrnuté do `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="58a8b-128">Následující kód ukazuje soubor projektu, který používá sadu Razor SDK k sestavení souborů Razor pro aplikaci ASP.NET Core Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="58a8b-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="58a8b-129">Balíčky `Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="58a8b-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="58a8b-130">Reference k balíčku `Microsoft.AspNetCore.App` bez verze ale poskytuje Metapackage aplikaci, která neobsahuje nejnovější verzi `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="58a8b-131">Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`), aby byly zahrnuty nejnovější opravy v době sestavení pro Razor.</span><span class="sxs-lookup"><span data-stu-id="58a8b-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="58a8b-132">Další informace najdete v [tomto problému GitHubu](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="58a8b-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="58a8b-133">Vlastnosti</span><span class="sxs-lookup"><span data-stu-id="58a8b-133">Properties</span></span>

<span data-ttu-id="58a8b-134">Následující vlastnosti řídí chování sady SDK Razor v rámci sestavení projektu:</span><span class="sxs-lookup"><span data-stu-id="58a8b-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="58a8b-135">`RazorCompileOnBuild` &ndash;, pokud `true` kompiluje a generuje sestavení Razor jako součást sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="58a8b-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="58a8b-136">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-136">Defaults to `true`.</span></span>
* <span data-ttu-id="58a8b-137">`RazorCompileOnPublish` &ndash;, pokud `true`, kompiluje a generuje sestavení Razor jako součást publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="58a8b-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="58a8b-138">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-138">Defaults to `true`.</span></span>

<span data-ttu-id="58a8b-139">Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů do sady Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="58a8b-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="58a8b-140">Počínaje ASP.NET Core 3,0 nejsou zobrazení MVC nebo Razor Pages obsluhovány ve výchozím nastavení, pokud jsou vlastnosti `RazorCompileOnBuild` nebo `RazorCompileOnPublish` MSBuild v souboru projektu zakázané.</span><span class="sxs-lookup"><span data-stu-id="58a8b-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="58a8b-141">Aplikace musí přidat explicitní odkaz na balíček [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) , pokud aplikace spoléhá na kompilaci za běhu za účelem zpracování souborů *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58a8b-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="58a8b-142">Položky</span><span class="sxs-lookup"><span data-stu-id="58a8b-142">Items</span></span> | <span data-ttu-id="58a8b-143">Popis</span><span class="sxs-lookup"><span data-stu-id="58a8b-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="58a8b-144">Prvky položky (soubory *. cshtml* ), které jsou vstupy pro generování kódu.</span><span class="sxs-lookup"><span data-stu-id="58a8b-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="58a8b-145">Prvky položky (soubory *. Razor* ), které jsou vstupy pro generování kódu komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="58a8b-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="58a8b-146">Prvky položky (soubory *. cs* ), které jsou vstupy pro cíle kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="58a8b-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="58a8b-147">Pomocí tohoto `ItemGroup` určete další soubory, které mají být zkompilovány do sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="58a8b-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="58a8b-148">Prvky položky použité pro kód generují atributy pro sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="58a8b-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="58a8b-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="58a8b-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="58a8b-150">Prvky položky přidané jako vložené prostředky do vygenerovaného sestavení Razor</span><span class="sxs-lookup"><span data-stu-id="58a8b-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="58a8b-151">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="58a8b-151">Property</span></span> | <span data-ttu-id="58a8b-152">Popis</span><span class="sxs-lookup"><span data-stu-id="58a8b-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="58a8b-153">Název souboru (bez přípony) sestavení vytvořeného syntaxí Razor</span><span class="sxs-lookup"><span data-stu-id="58a8b-153">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="58a8b-154">Výstupní adresář Razor</span><span class="sxs-lookup"><span data-stu-id="58a8b-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="58a8b-155">Slouží k určení sady nástrojů používané k sestavení sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="58a8b-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="58a8b-156">Platné hodnoty jsou `Implicit`, `RazorSDK` a `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="58a8b-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="58a8b-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="58a8b-158">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-158">Default is `true`.</span></span> <span data-ttu-id="58a8b-159">Při `true` zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu.</span><span class="sxs-lookup"><span data-stu-id="58a8b-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="58a8b-160">V případě, že jsou odkazovány prostřednictvím `Microsoft.NET.Sdk.Web`, jsou zahrnuty také soubory pod soubory *wwwroot* a config.</span><span class="sxs-lookup"><span data-stu-id="58a8b-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="58a8b-161">Při `true` zahrnuje soubory *. cshtml* z položek `Content` v položkách `RazorGenerate`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="58a8b-162">Když `true`, vygeneruje soubor *. cs* obsahující atributy určené `RazorAssemblyAttribute` a zahrne soubor do výstupu kompilace.</span><span class="sxs-lookup"><span data-stu-id="58a8b-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="58a8b-163">Při `true` přidá výchozí sadu atributů sestavení do `RazorAssemblyAttribute`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="58a8b-164">Při `true` zkopíruje soubory `RazorGenerate` ( *. cshtml*) do adresáře pro publikování.</span><span class="sxs-lookup"><span data-stu-id="58a8b-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="58a8b-165">Soubory Razor nejsou typicky nutné pro publikovanou aplikaci, pokud se účastní kompilace při sestavení nebo době publikování.</span><span class="sxs-lookup"><span data-stu-id="58a8b-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="58a8b-166">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="58a8b-167">Při `true` zkopírujte položky referenčního sestavení do adresáře pro publikování.</span><span class="sxs-lookup"><span data-stu-id="58a8b-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="58a8b-168">V případě, že dojde k kompilaci Razor v době sestavení nebo při publikování, se obvykle nevyžadují referenční sestavení pro publikovanou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="58a8b-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="58a8b-169">Nastavte na `true`, pokud vaše publikovaná aplikace vyžaduje kompilaci za běhu.</span><span class="sxs-lookup"><span data-stu-id="58a8b-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="58a8b-170">Nastavte například hodnotu `true`, pokud aplikace upravuje soubory *. cshtml* za běhu nebo používá vložená zobrazení.</span><span class="sxs-lookup"><span data-stu-id="58a8b-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="58a8b-171">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="58a8b-172">Při `true` jsou všechny položky obsahu Razor (soubory *. cshtml* ) označeny pro zahrnutí do generovaného balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="58a8b-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="58a8b-173">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="58a8b-174">Při `true` přidá položky RazorGenerate ( *. cshtml*) jako vložené soubory do vygenerovaného sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="58a8b-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="58a8b-175">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="58a8b-176">Při `true` používá trvalý proces sestavení serveru pro přesměrování práce generování kódu.</span><span class="sxs-lookup"><span data-stu-id="58a8b-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="58a8b-177">Výchozí hodnota je `UseSharedCompilation`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="58a8b-178">Při `true` vygeneruje sada SDK Další atributy, které MVC používá za běhu, aby bylo možné provést zjišťování částí aplikace.</span><span class="sxs-lookup"><span data-stu-id="58a8b-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |

<span data-ttu-id="58a8b-179">Další informace o vlastnostech naleznete v tématu [vlastnosti MSBuild](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="58a8b-179">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="58a8b-180">Cíle</span><span class="sxs-lookup"><span data-stu-id="58a8b-180">Targets</span></span>

<span data-ttu-id="58a8b-181">Sada Razor SDK definuje dva primární cíle:</span><span class="sxs-lookup"><span data-stu-id="58a8b-181">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="58a8b-182">`RazorGenerate` kód &ndash; generuje soubory *. cs* z prvků položky `RazorGenerate`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-182">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="58a8b-183">Pomocí vlastnosti `RazorGenerateDependsOn` určete další cíle, které lze spustit před nebo po tomto cíli.</span><span class="sxs-lookup"><span data-stu-id="58a8b-183">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="58a8b-184">`RazorCompile` &ndash; zkompiluje generované soubory *. cs* do sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="58a8b-184">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="58a8b-185">Pomocí `RazorCompileDependsOn` můžete určit další cíle, které se dají spustit před nebo po tomto cíli.</span><span class="sxs-lookup"><span data-stu-id="58a8b-185">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="58a8b-186">`RazorComponentGenerate` kód &ndash; generuje soubory *. cs* pro prvky položky `RazorComponent`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-186">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="58a8b-187">Pomocí vlastnosti `RazorComponentGenerateDependsOn` určete další cíle, které lze spustit před nebo po tomto cíli.</span><span class="sxs-lookup"><span data-stu-id="58a8b-187">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="58a8b-188">Běhová kompilace zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="58a8b-188">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="58a8b-189">Ve výchozím nastavení sada Razor SDK nepublikuje referenční sestavení, která jsou nutná k provedení kompilace za běhu.</span><span class="sxs-lookup"><span data-stu-id="58a8b-189">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="58a8b-190">Výsledkem je selhání kompilace, když aplikační model spoléhá na kompilaci modulu runtime&mdash;například aplikace používá vložená zobrazení nebo změny zobrazení po publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="58a8b-190">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="58a8b-191">Pokud chcete pokračovat v publikování referenčních sestavení, nastavte `CopyRefAssembliesToPublishDirectory` na `true`.</span><span class="sxs-lookup"><span data-stu-id="58a8b-191">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="58a8b-192">V případě webové aplikace zajistěte, aby vaše aplikace byla cílena na sadu `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="58a8b-192">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="58a8b-193">Verze jazyka Razor</span><span class="sxs-lookup"><span data-stu-id="58a8b-193">Razor language version</span></span>

<span data-ttu-id="58a8b-194">Při cílení na sadu SDK `Microsoft.NET.Sdk.Web` je jazyková verze Razor odvozená z verze cílového rozhraní Framework aplikace.</span><span class="sxs-lookup"><span data-stu-id="58a8b-194">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="58a8b-195">Pro projekty, které cílí na sadu `Microsoft.NET.Sdk.Razor` SDK nebo ve vzácných případech, kdy aplikace vyžaduje jinou verzi jazyka Razor než odvozenou hodnotu, lze verzi nakonfigurovat nastavením vlastnosti `<RazorLangVersion>` v souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="58a8b-195">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="58a8b-196">Jazyková verze Razor je úzce integrovaná s verzí modulu runtime, pro který byl sestaven.</span><span class="sxs-lookup"><span data-stu-id="58a8b-196">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="58a8b-197">Cílení na jazykovou verzi, která není navržena pro modul runtime, není podporována a nejspíš generuje chyby sestavení.</span><span class="sxs-lookup"><span data-stu-id="58a8b-197">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58a8b-198">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="58a8b-198">Additional resources</span></span>

* [<span data-ttu-id="58a8b-199">Přidání do formátu csproj pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="58a8b-199">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="58a8b-200">Společné položky projektu nástroje MSBuild</span><span class="sxs-lookup"><span data-stu-id="58a8b-200">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
