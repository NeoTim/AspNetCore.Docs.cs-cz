---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 872d90662494735dc0e4caa01c46fcdcc2606bc6
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809130"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="2e212-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="2e212-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="2e212-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2e212-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="2e212-105">Přehled</span><span class="sxs-lookup"><span data-stu-id="2e212-105">Overview</span></span>

<span data-ttu-id="2e212-106">[!INCLUDE[](~/includes/2.1-SDK.md)] Zahrnuje `Microsoft.NET.Sdk.Razor` sady MSBuild SDK (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="2e212-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="2e212-107">Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="2e212-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="2e212-108">Je vyžadován k sestavení, sbalení a publikování projektů obsahujících soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projekty založené na MVC nebo [Blazor](xref:blazor/index) .</span><span class="sxs-lookup"><span data-stu-id="2e212-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="2e212-109">Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor ( *. cshtml* nebo *. Razor*).</span><span class="sxs-lookup"><span data-stu-id="2e212-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="2e212-110">Sada Razor SDK obsahuje `Content` položky s `Include` atributy nastavené na `**\*.cshtml` a `**\*.razor` vzory expanze.</span><span class="sxs-lookup"><span data-stu-id="2e212-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="2e212-111">Jsou publikovány vyhovující soubory.</span><span class="sxs-lookup"><span data-stu-id="2e212-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="2e212-112">Standardizuje prostředí týkající se vytváření, balení a publikování projektů, které obsahují [Razor](xref:mvc/views/razor) souborů pro projekty ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="2e212-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="2e212-113">Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="2e212-114">Sada Razor SDK obsahuje `Content` položku s atributem `Include` nastaveným na `**\*.cshtml` vzorek expanze.</span><span class="sxs-lookup"><span data-stu-id="2e212-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="2e212-115">Jsou publikovány vyhovující soubory.</span><span class="sxs-lookup"><span data-stu-id="2e212-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="2e212-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2e212-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="2e212-117">Použití sady Razor SDK</span><span class="sxs-lookup"><span data-stu-id="2e212-117">Use the Razor SDK</span></span>

<span data-ttu-id="2e212-118">Většina webových aplikací není nutné explicitně odkazují na sadu SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2e212-119">Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení nebo Razor Pages Razor, doporučujeme začít se šablonou projektu knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="2e212-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="2e212-120">RCL, který se používá k sestavování souborů Blazor ( *. Razor*), vyžaduje minimální odkaz na balíček [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) .</span><span class="sxs-lookup"><span data-stu-id="2e212-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="2e212-121">RCL, který se používá k vytvoření zobrazení nebo stránek (souborů *. cshtml* ) Razor, vyžaduje minimálně cílení na `netcoreapp3.0` nebo novější a má `FrameworkReference` do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v jeho souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="2e212-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2e212-122">Použití sady SDK Razor k vytvoření knihovny tříd obsahující zobrazení syntaxe Razor nebo stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="2e212-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="2e212-123">Použití `Microsoft.NET.Sdk.Razor` místo `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="2e212-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="2e212-124">Obvykle, odkaz na balíček pro `Microsoft.AspNetCore.Mvc` se vyžaduje pro příjem Další závislosti, které jsou potřebné k sestavení a kompilace Razor Pages a zobrazeními Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="2e212-125">Minimálně byste vašeho projektu přidat odkazy na balíčky do:</span><span class="sxs-lookup"><span data-stu-id="2e212-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  <span data-ttu-id="2e212-126">`Microsoft.AspNetCore.Razor.Design` Balíček poskytuje Razor kompilace úlohy a cíle projektu.</span><span class="sxs-lookup"><span data-stu-id="2e212-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="2e212-127">Předchozí balíčky jsou součástí `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="2e212-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="2e212-128">Následující kód ukazuje soubor projektu, který používá sada Razor SDK k sestavení souborů Razor pro aplikace ASP.NET Core Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="2e212-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="2e212-129">`Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí balíčků [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2e212-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="2e212-130">Ale bez verze `Microsoft.AspNetCore.App` odkaz na balíček poskytuje Microsoft.aspnetcore.all aplikaci, která neobsahuje nejnovější verzi `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="2e212-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="2e212-131">Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`) tak, aby byly zahrnuty nejnovější opravy čas sestavení pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="2e212-132">Další informace najdete v tématu [tento problém Githubu](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="2e212-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="2e212-133">Vlastnosti</span><span class="sxs-lookup"><span data-stu-id="2e212-133">Properties</span></span>

<span data-ttu-id="2e212-134">Následující vlastnosti řídit chování sady SDK pro syntaxi Razor jako součást sestavení projektu:</span><span class="sxs-lookup"><span data-stu-id="2e212-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="2e212-135">`RazorCompileOnBuild` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="2e212-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="2e212-136">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="2e212-136">Defaults to `true`.</span></span>
* <span data-ttu-id="2e212-137">`RazorCompileOnPublish` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="2e212-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="2e212-138">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="2e212-138">Defaults to `true`.</span></span>

<span data-ttu-id="2e212-139">Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="2e212-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="2e212-140">Počínaje ASP.NET Core 3,0 se zobrazení MVC nebo Razor Pages nezpracovávají ve výchozím nastavení, pokud jsou vlastnosti `RazorCompileOnBuild` nebo `RazorCompileOnPublish` MSBuild v souboru projektu zakázané.</span><span class="sxs-lookup"><span data-stu-id="2e212-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="2e212-141">Aplikace musí přidat explicitní odkaz na balíček [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) , pokud aplikace spoléhá na kompilaci za běhu za účelem zpracování souborů *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2e212-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="2e212-142">Položky</span><span class="sxs-lookup"><span data-stu-id="2e212-142">Items</span></span> | <span data-ttu-id="2e212-143">Popis</span><span class="sxs-lookup"><span data-stu-id="2e212-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="2e212-144">Prvky položky (soubory *. cshtml* ), které jsou vstupy pro generování kódu.</span><span class="sxs-lookup"><span data-stu-id="2e212-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="2e212-145">Prvky položky (soubory *. Razor* ), které jsou vstupy pro generování kódu komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="2e212-146">Prvky položky (soubory *. cs* ), které jsou vstupy pro cíle kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="2e212-147">Tento `ItemGroup` slouží k určení dalších souborů, které mají být zkompilovány do sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="2e212-148">Položka prvků, které slouží ke kódu generovat atributy pro sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="2e212-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2e212-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="2e212-150">Položky elementy přidané jako vložené prostředky do generovaného sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="2e212-151">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="2e212-151">Property</span></span> | <span data-ttu-id="2e212-152">Popis</span><span class="sxs-lookup"><span data-stu-id="2e212-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="2e212-153">Název souboru (bez přípony) sestavení vytvořené metodou Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-153">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="2e212-154">Výstupní adresář Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="2e212-155">Slouží k určení sady nástrojů použitý k vytvoření sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="2e212-156">Platné hodnoty jsou `Implicit`, `RazorSDK`, a `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="2e212-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="2e212-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="2e212-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="2e212-158">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="2e212-158">Default is `true`.</span></span> <span data-ttu-id="2e212-159">Při `true`zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu.</span><span class="sxs-lookup"><span data-stu-id="2e212-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="2e212-160">Pokud je k dispozici prostřednictvím `Microsoft.NET.Sdk.Web`, soubory pod *wwwroot* a konfigurační soubory jsou zahrnuté také.</span><span class="sxs-lookup"><span data-stu-id="2e212-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="2e212-161">Když `true`, zahrnuje *.cshtml* souborů z `Content` položky v `RazorGenerate` položky.</span><span class="sxs-lookup"><span data-stu-id="2e212-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="2e212-162">Když `true`, generuje *.cs* soubor, který obsahuje atributy určené `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace.</span><span class="sxs-lookup"><span data-stu-id="2e212-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="2e212-163">Když `true`, přidá výchozí sadu atributů sestavení, které mají `RazorAssemblyAttribute`.</span><span class="sxs-lookup"><span data-stu-id="2e212-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="2e212-164">Když `true`, kopie `RazorGenerate` položky ( *.cshtml*) soubory do adresáře publikovat.</span><span class="sxs-lookup"><span data-stu-id="2e212-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="2e212-165">Obvykle nejsou publikované aplikace vyžadovat Razor soubory, pokud se účastní v sestavování v době sestavení nebo publikovat čas.</span><span class="sxs-lookup"><span data-stu-id="2e212-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="2e212-166">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="2e212-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="2e212-167">Když `true`, zkopírovat odkaz na sestavení položky do adresáře publikovat.</span><span class="sxs-lookup"><span data-stu-id="2e212-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="2e212-168">Obvykle nejsou publikované aplikace vyžadovat referenční sestavení, dojde v okamžiku sestavení nebo publikovat čas kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="2e212-169">Nastavte na `true` Pokud publikované aplikace vyžaduje kompilace modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="2e212-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="2e212-170">Například nastavte hodnotu na `true` Pokud aplikace změní *.cshtml* soubory za běhu nebo používá vložený zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2e212-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="2e212-171">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="2e212-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="2e212-172">Když `true`, všechny položky obsahu Razor ( *.cshtml* soubory) jsou označeny k zahrnutí vygenerovaný balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="2e212-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="2e212-173">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="2e212-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="2e212-174">Když `true`, přidá RazorGenerate ( *.cshtml*) položky jako vložené soubory do generovaného sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="2e212-175">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="2e212-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="2e212-176">Když `true`, využívá proces serveru trvalé sestavení přesměrovat pracovní generování kódu.</span><span class="sxs-lookup"><span data-stu-id="2e212-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="2e212-177">Výchozí hodnota je hodnota `UseSharedCompilation`.</span><span class="sxs-lookup"><span data-stu-id="2e212-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="2e212-178">Při `true`generuje sada SDK Další atributy, pomocí kterých MVC za běhu provede zjišťování částí aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e212-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="2e212-179">Vzor expanze názvů pro prvky položky, které mají být vyloučeny ze skupiny položek `Content` v projektech cílících na web nebo sadu Razor SDK</span><span class="sxs-lookup"><span data-stu-id="2e212-179">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="2e212-180">Při `true`se soubory *. config* a *. JSON* nekopírují do výstupního adresáře sestavení.</span><span class="sxs-lookup"><span data-stu-id="2e212-180">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="2e212-181">Když `true`, nakonfiguruje sadu Razor SDK tak, aby přidala podporu pro konfiguraci MVC, která je požadována při sestavování aplikací obsahujících zobrazení MVC nebo Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2e212-181">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="2e212-182">Tato vlastnost je implicitně nastavená pro projekty .NET Core 3,0 nebo novější, které cílí na webovou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="2e212-182">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="2e212-183">Verze jazyka Razor, na kterou se má cílit</span><span class="sxs-lookup"><span data-stu-id="2e212-183">The version of the Razor Language to target.</span></span> |

<span data-ttu-id="2e212-184">Další informace o vlastnostech naleznete v tématu [vlastnosti MSBuild](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="2e212-184">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="2e212-185">Cíle</span><span class="sxs-lookup"><span data-stu-id="2e212-185">Targets</span></span>

<span data-ttu-id="2e212-186">Sada Razor SDK definuje dva hlavní cíle:</span><span class="sxs-lookup"><span data-stu-id="2e212-186">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="2e212-187">`RazorGenerate` &ndash; Generuje kód *.cs* souborů z `RazorGenerate` položky elementy.</span><span class="sxs-lookup"><span data-stu-id="2e212-187">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="2e212-188">Vlastnost `RazorGenerateDependsOn` použijte k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.</span><span class="sxs-lookup"><span data-stu-id="2e212-188">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="2e212-189">`RazorCompile` &ndash; Zkompiluje generované *.cs* soubory v sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="2e212-189">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="2e212-190">Pomocí `RazorCompileDependsOn` můžete určit další cíle, které se dají spustit před nebo po tomto cíli.</span><span class="sxs-lookup"><span data-stu-id="2e212-190">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="2e212-191">Kód &ndash; `RazorComponentGenerate` generuje soubory *. cs* pro prvky `RazorComponent` položky.</span><span class="sxs-lookup"><span data-stu-id="2e212-191">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="2e212-192">Vlastnost `RazorComponentGenerateDependsOn` použijte k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.</span><span class="sxs-lookup"><span data-stu-id="2e212-192">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="2e212-193">Kompilace modulu runtime zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="2e212-193">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="2e212-194">Ve výchozím nastavení nebude sada Razor SDK publikovat referenční sestavení, které jsou nutné k provedení kompilace modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="2e212-194">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="2e212-195">Výsledkem je selhání kompilace při aplikační model využívá kompilace modulu runtime&mdash;například aplikace používá vložený zobrazení nebo změny zobrazení po publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e212-195">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="2e212-196">Nastavte `CopyRefAssembliesToPublishDirectory` k `true` můžete pokračovat v publikování referenční sestavení.</span><span class="sxs-lookup"><span data-stu-id="2e212-196">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="2e212-197">Pro webovou aplikaci, ujistěte se vaše aplikace cílí `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="2e212-197">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="2e212-198">Verze jazyka Razor</span><span class="sxs-lookup"><span data-stu-id="2e212-198">Razor language version</span></span>

<span data-ttu-id="2e212-199">Při cílení na sadu `Microsoft.NET.Sdk.Web` SDK je jazyková verze Razor odvozená z verze rozhraní Target Framework aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e212-199">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="2e212-200">Pro projekty, které cílí na sadu `Microsoft.NET.Sdk.Razor` SDK, nebo ve výjimečném případě, že aplikace vyžaduje jinou verzi jazyka Razor než odvozenou hodnotu, lze verzi nakonfigurovat nastavením vlastnosti `<RazorLangVersion>` v souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="2e212-200">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="2e212-201">Jazyková verze Razor je úzce integrovaná s verzí modulu runtime, pro který byl sestaven.</span><span class="sxs-lookup"><span data-stu-id="2e212-201">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="2e212-202">Cílení na jazykovou verzi, která není navržena pro modul runtime, není podporována a nejspíš generuje chyby sestavení.</span><span class="sxs-lookup"><span data-stu-id="2e212-202">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e212-203">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="2e212-203">Additional resources</span></span>

* [<span data-ttu-id="2e212-204">Přidání do formátu csproj pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="2e212-204">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="2e212-205">Společné položky projektu nástroje MSBuild</span><span class="sxs-lookup"><span data-stu-id="2e212-205">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
