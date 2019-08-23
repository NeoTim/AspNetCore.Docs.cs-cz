---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: 1dc001c7c5fe320629835e06fe6db7fadabff94d
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985398"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="d80c0-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="d80c0-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="d80c0-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d80c0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="d80c0-105">Přehled</span><span class="sxs-lookup"><span data-stu-id="d80c0-105">Overview</span></span>

<span data-ttu-id="d80c0-106">[!INCLUDE[](~/includes/2.1-SDK.md)] Zahrnuje `Microsoft.NET.Sdk.Razor` sady MSBuild SDK (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="d80c0-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="d80c0-107">Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="d80c0-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
* <span data-ttu-id="d80c0-108">Standardizuje prostředí týkající se vytváření, balení a publikování projektů, které obsahují [Razor](xref:mvc/views/razor) souborů pro projekty ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d80c0-108">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="d80c0-109">Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <span data-ttu-id="d80c0-110">je vyžadován k sestavování, balení a publikování projektů obsahujících soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projektů založených na MVC nebo projektů [Blazor](xref:blazor/index)</span><span class="sxs-lookup"><span data-stu-id="d80c0-110">is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects, or [Blazor](xref:blazor/index) projects</span></span>
* <span data-ttu-id="d80c0-111">Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor (. cshtml nebo. Razor).</span><span class="sxs-lookup"><span data-stu-id="d80c0-111">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (.cshtml or .razor) files.</span></span>
::: moniker-end


::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="d80c0-112">Sada Razor SDK obsahuje `<Content>` prvek `Include` s atributem nastaveným na `**\*.cshtml` vzor expanze.</span><span class="sxs-lookup"><span data-stu-id="d80c0-112">The Razor SDK includes a `<Content>` element with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="d80c0-113">Jsou publikovány vyhovující soubory.</span><span class="sxs-lookup"><span data-stu-id="d80c0-113">Matching files are published.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d80c0-114">Sada Razor SDK obsahuje `<Content>` prvky s `Include` atributy nastavenými na `**\*.cshtml` vzory `**\*.razor` pro expanzi názvů a.</span><span class="sxs-lookup"><span data-stu-id="d80c0-114">The Razor SDK includes `<Content>` elements with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="d80c0-115">Jsou publikovány vyhovující soubory.</span><span class="sxs-lookup"><span data-stu-id="d80c0-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="d80c0-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d80c0-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="d80c0-117">Použití sady Razor SDK</span><span class="sxs-lookup"><span data-stu-id="d80c0-117">Use the Razor SDK</span></span>

<span data-ttu-id="d80c0-118">Většina webových aplikací není nutné explicitně odkazují na sadu SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
<span data-ttu-id="d80c0-119">Použití sady SDK Razor k vytvoření knihovny tříd obsahující zobrazení syntaxe Razor nebo stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="d80c0-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="d80c0-120">Použití `Microsoft.NET.Sdk.Razor` místo `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="d80c0-120">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="d80c0-121">Obvykle, odkaz na balíček pro `Microsoft.AspNetCore.Mvc` se vyžaduje pro příjem Další závislosti, které jsou potřebné k sestavení a kompilace Razor Pages a zobrazeními Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-121">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="d80c0-122">Minimálně byste vašeho projektu přidat odkazy na balíčky do:</span><span class="sxs-lookup"><span data-stu-id="d80c0-122">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="d80c0-123">`Microsoft.AspNetCore.Razor.Design` Balíček poskytuje Razor kompilace úlohy a cíle projektu.</span><span class="sxs-lookup"><span data-stu-id="d80c0-123">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="d80c0-124">Předchozí balíčky jsou součástí `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-124">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="d80c0-125">Následující kód ukazuje soubor projektu, který používá sada Razor SDK k sestavení souborů Razor pro aplikace ASP.NET Core Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="d80c0-125">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="d80c0-126">Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení Razor nebo Razor Pages, doporučujeme začít se šablonou projektu knihovny tříd Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-126">To use the Razor SDK to build class libraries containing Razor views or Razor Pages we recommend starting with the Razor Class Library project template.</span></span> <span data-ttu-id="d80c0-127">Knihovna tříd Razor, která se používá k sestavování souborů Blazor (. Razor), bude minimálně vyžadovat odkaz na `Microsoft.AspNetCore.Components` balíček.</span><span class="sxs-lookup"><span data-stu-id="d80c0-127">A Razor class library that is used to build Blazor (.razor) files will at minimum require a reference to the `Microsoft.AspNetCore.Components` package.</span></span> <span data-ttu-id="d80c0-128">Knihovna tříd Razor, která se používá k sestavení zobrazení nebo stránek (soubory. cshtml) Razor, bude vyžadovat cílení `netcoreapp3.0` nebo novější a `FrameworkReference` má na `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-128">A Razor class library that is used to build Razor views or pages (.cshtml files), will require targeting `netcoreapp3.0` or newer and have a `FrameworkReference` to `Microsoft.AspNetCore.App`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="d80c0-129">`Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí balíčků [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d80c0-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="d80c0-130">Ale bez verze `Microsoft.AspNetCore.App` odkaz na balíček poskytuje Microsoft.aspnetcore.all aplikaci, která neobsahuje nejnovější verzi `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="d80c0-131">Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`) tak, aby byly zahrnuty nejnovější opravy čas sestavení pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="d80c0-132">Další informace najdete v tématu [tento problém Githubu](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="d80c0-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="d80c0-133">Vlastnosti</span><span class="sxs-lookup"><span data-stu-id="d80c0-133">Properties</span></span>

<span data-ttu-id="d80c0-134">Následující vlastnosti řídit chování sady SDK pro syntaxi Razor jako součást sestavení projektu:</span><span class="sxs-lookup"><span data-stu-id="d80c0-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="d80c0-135">`RazorCompileOnBuild` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="d80c0-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="d80c0-136">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-136">Defaults to `true`.</span></span>
* <span data-ttu-id="d80c0-137">`RazorCompileOnPublish` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="d80c0-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="d80c0-138">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-138">Defaults to `true`.</span></span>

<span data-ttu-id="d80c0-139">Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="d80c0-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"
> [!WARNING]
<span data-ttu-id="d80c0-140">Počínaje ASP.NET Core 3,0 nebudou zobrazení MVC nebo Razor Pages ve výchozím nastavení obsluhovány, pokud `RazorCompileOnBuild` je nebo `RazorCompileOnPublish` zakázán.</span><span class="sxs-lookup"><span data-stu-id="d80c0-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages will not be served by default if `RazorCompileOnBuild` or `RazorCompileOnPublish` is disabled.</span></span> <span data-ttu-id="d80c0-141">Aplikace musí přidat explicitní odkaz na `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` balíček pro přidání podpory pro kompilaci za běhu, pokud spoléhají na kompilaci za běhu za účelem zpracování souborů cshtml.</span><span class="sxs-lookup"><span data-stu-id="d80c0-141">Applications must add an explicit reference to `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package to add support for runtime compilation if they rely on runtime compilation to process cshtml files.</span></span>
::: moniker-end


| <span data-ttu-id="d80c0-142">Položky</span><span class="sxs-lookup"><span data-stu-id="d80c0-142">Items</span></span> | <span data-ttu-id="d80c0-143">Popis</span><span class="sxs-lookup"><span data-stu-id="d80c0-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="d80c0-144">Prvky položky (soubory *. cshtml* ), které jsou vstupy pro generování kódu.</span><span class="sxs-lookup"><span data-stu-id="d80c0-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="d80c0-145">Prvky položky (soubory *. Razor* ), které jsou vstupy pro generování kódu komponenty.</span><span class="sxs-lookup"><span data-stu-id="d80c0-145">Item elements (*.razor* files) that are inputs to Component code generation.</span></span>
| `RazorCompile` | <span data-ttu-id="d80c0-146">Prvky položky (soubory *. cs* ), které jsou vstupy pro cíle kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="d80c0-147">Toto `ItemGroup` použijte k určení dalších souborů, které mají být zkompilovány do sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="d80c0-148">Položka prvků, které slouží ke kódu generovat atributy pro sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="d80c0-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d80c0-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="d80c0-150">Položky elementy přidané jako vložené prostředky do generovaného sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="d80c0-151">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="d80c0-151">Property</span></span> | <span data-ttu-id="d80c0-152">Popis</span><span class="sxs-lookup"><span data-stu-id="d80c0-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="d80c0-153">Název souboru (bez přípony) sestavení vytvořené metodou Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-153">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="d80c0-154">Výstupní adresář Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="d80c0-155">Slouží k určení sady nástrojů použitý k vytvoření sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="d80c0-156">Platné hodnoty jsou `Implicit`, `RazorSDK`, a `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="d80c0-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="d80c0-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="d80c0-158">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-158">Default is `true`.</span></span> <span data-ttu-id="d80c0-159">V `true`případě zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu.</span><span class="sxs-lookup"><span data-stu-id="d80c0-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="d80c0-160">Pokud je k dispozici prostřednictvím `Microsoft.NET.Sdk.Web`, soubory pod *wwwroot* a konfigurační soubory jsou zahrnuté také.</span><span class="sxs-lookup"><span data-stu-id="d80c0-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="d80c0-161">Když `true`, zahrnuje *.cshtml* souborů z `Content` položky v `RazorGenerate` položky.</span><span class="sxs-lookup"><span data-stu-id="d80c0-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="d80c0-162">Když `true`, generuje *.cs* soubor, který obsahuje atributy určené `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace.</span><span class="sxs-lookup"><span data-stu-id="d80c0-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="d80c0-163">Když `true`, přidá výchozí sadu atributů sestavení, které mají `RazorAssemblyAttribute`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="d80c0-164">Když `true`, kopie `RazorGenerate` položky ( *.cshtml*) soubory do adresáře publikovat.</span><span class="sxs-lookup"><span data-stu-id="d80c0-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="d80c0-165">Obvykle nejsou publikované aplikace vyžadovat Razor soubory, pokud se účastní v sestavování v době sestavení nebo publikovat čas.</span><span class="sxs-lookup"><span data-stu-id="d80c0-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="d80c0-166">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="d80c0-167">Když `true`, zkopírovat odkaz na sestavení položky do adresáře publikovat.</span><span class="sxs-lookup"><span data-stu-id="d80c0-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="d80c0-168">Obvykle nejsou publikované aplikace vyžadovat referenční sestavení, dojde v okamžiku sestavení nebo publikovat čas kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="d80c0-169">Nastavte na `true` Pokud publikované aplikace vyžaduje kompilace modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="d80c0-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="d80c0-170">Například nastavte hodnotu na `true` Pokud aplikace změní *.cshtml* soubory za běhu nebo používá vložený zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d80c0-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="d80c0-171">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="d80c0-172">Když `true`, všechny položky obsahu Razor ( *.cshtml* soubory) jsou označeny k zahrnutí vygenerovaný balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="d80c0-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="d80c0-173">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="d80c0-174">Když `true`, přidá RazorGenerate ( *.cshtml*) položky jako vložené soubory do generovaného sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="d80c0-175">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="d80c0-176">Když `true`, využívá proces serveru trvalé sestavení přesměrovat pracovní generování kódu.</span><span class="sxs-lookup"><span data-stu-id="d80c0-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="d80c0-177">Výchozí hodnota je hodnota `UseSharedCompilation`.</span><span class="sxs-lookup"><span data-stu-id="d80c0-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="d80c0-178">Když `true`sada SDK generuje další atributy, pomocí kterých MVC za běhu provede zjišťování částí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d80c0-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |

<span data-ttu-id="d80c0-179">Další informace o vlastnostech naleznete v tématu [vlastnosti MSBuild](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="d80c0-179">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="d80c0-180">Cíle</span><span class="sxs-lookup"><span data-stu-id="d80c0-180">Targets</span></span>

<span data-ttu-id="d80c0-181">Sada Razor SDK definuje dva hlavní cíle:</span><span class="sxs-lookup"><span data-stu-id="d80c0-181">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="d80c0-182">`RazorGenerate` &ndash; Generuje kód *.cs* souborů z `RazorGenerate` položky elementy.</span><span class="sxs-lookup"><span data-stu-id="d80c0-182">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="d80c0-183">Použití `RazorGenerateDependsOn` vlastnosti a určit další cílí, který můžete spustit před nebo po tento cíl.</span><span class="sxs-lookup"><span data-stu-id="d80c0-183">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="d80c0-184">`RazorCompile` &ndash; Zkompiluje generované *.cs* soubory v sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="d80c0-184">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="d80c0-185">Použití `RazorCompileDependsOn` zadat další cíle, které můžete spustit před nebo po tento cíl.</span><span class="sxs-lookup"><span data-stu-id="d80c0-185">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="d80c0-186">`RazorComponentGenerate`Kód generuje soubory *. cs* pro `RazorComponent` prvky položky. &ndash;</span><span class="sxs-lookup"><span data-stu-id="d80c0-186">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="d80c0-187">Použití `RazorComponentGenerateDependsOn` vlastnosti a určit další cílí, který můžete spustit před nebo po tento cíl.</span><span class="sxs-lookup"><span data-stu-id="d80c0-187">Use `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="d80c0-188">Kompilace modulu runtime zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="d80c0-188">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="d80c0-189">Ve výchozím nastavení nebude sada Razor SDK publikovat referenční sestavení, které jsou nutné k provedení kompilace modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="d80c0-189">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="d80c0-190">Výsledkem je selhání kompilace při aplikační model využívá kompilace modulu runtime&mdash;například aplikace používá vložený zobrazení nebo změny zobrazení po publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="d80c0-190">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="d80c0-191">Nastavte `CopyRefAssembliesToPublishDirectory` k `true` můžete pokračovat v publikování referenční sestavení.</span><span class="sxs-lookup"><span data-stu-id="d80c0-191">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="d80c0-192">Pro webovou aplikaci, ujistěte se vaše aplikace cílí `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="d80c0-192">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="d80c0-193">Verze jazyka Razor</span><span class="sxs-lookup"><span data-stu-id="d80c0-193">Razor language version</span></span>

<span data-ttu-id="d80c0-194">Při cílení `Microsoft.NET.Sdk.Web` na sadu SDK je jazyková verze Razor odvozena z verze cílového rozhraní Framework aplikace.</span><span class="sxs-lookup"><span data-stu-id="d80c0-194">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the the app's target framework version.</span></span> <span data-ttu-id="d80c0-195">Pro projekty, které `Microsoft.NET.Sdk.Razor` cílí na sadu SDK nebo ve vzácných případech, že aplikace vyžaduje jinou verzi jazyka Razor než odvozená hodnota, lze verzi nakonfigurovat `<RazorLangVersion>` nastavením vlastnosti v souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="d80c0-195">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="d80c0-196">Jazyková verze Razor je úzce integrovaná s verzí modulu runtime, pro který byl sestaven.</span><span class="sxs-lookup"><span data-stu-id="d80c0-196">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="d80c0-197">Cílení na verzi jazyka, která není navržena pro modul runtime, je nepodporované a bude nejspíš způsobit chyby sestavení.</span><span class="sxs-lookup"><span data-stu-id="d80c0-197">Targeting a language version that is not designed for the runtime is unsupported, and will likely produce build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d80c0-198">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d80c0-198">Additional resources</span></span>

* [<span data-ttu-id="d80c0-199">Přidání do formátu csproj pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="d80c0-199">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="d80c0-200">Společné položky projektu nástroje MSBuild</span><span class="sxs-lookup"><span data-stu-id="d80c0-200">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
