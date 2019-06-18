---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: fa69e4840377e0c1c8291c7ba9305a27bd3e6b82
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196366"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="054c5-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="054c5-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="054c5-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="054c5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="054c5-105">Přehled</span><span class="sxs-lookup"><span data-stu-id="054c5-105">Overview</span></span>

<span data-ttu-id="054c5-106">[!INCLUDE[](~/includes/2.1-SDK.md)] Zahrnuje `Microsoft.NET.Sdk.Razor` sady MSBuild SDK (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="054c5-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="054c5-107">Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="054c5-107">The Razor SDK:</span></span>

* <span data-ttu-id="054c5-108">Standardizuje prostředí týkající se vytváření, balení a publikování projektů, které obsahují [Razor](xref:mvc/views/razor) souborů pro projekty ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="054c5-108">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="054c5-109">Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="054c5-110">Sada Razor SDK zahrnuje `<Content>` element s `Include` atribut nastaven na `**\*.cshtml` model podpory zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="054c5-110">The Razor SDK includes a `<Content>` element with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="054c5-111">Porovnávání souborů k publikování.</span><span class="sxs-lookup"><span data-stu-id="054c5-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="054c5-112">Sada Razor SDK zahrnuje `<Content>` prvky s `Include` nastavte atributy na `**\*.cshtml` a `**\*.razor` vzorů podpory zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="054c5-112">The Razor SDK includes `<Content>` elements with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="054c5-113">Porovnávání souborů k publikování.</span><span class="sxs-lookup"><span data-stu-id="054c5-113">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="054c5-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="054c5-114">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="054c5-115">Použít syntaxi Razor SDK</span><span class="sxs-lookup"><span data-stu-id="054c5-115">Use the Razor SDK</span></span>

<span data-ttu-id="054c5-116">Většina webových aplikací není nutné explicitně odkazují na sadu SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-116">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

<span data-ttu-id="054c5-117">Použití sady SDK Razor k vytvoření knihovny tříd obsahující zobrazení syntaxe Razor nebo stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="054c5-117">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="054c5-118">Použití `Microsoft.NET.Sdk.Razor` místo `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="054c5-118">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="054c5-119">Obvykle, odkaz na balíček pro `Microsoft.AspNetCore.Mvc` se vyžaduje pro příjem Další závislosti, které jsou potřebné k sestavení a kompilace Razor Pages a zobrazeními Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-119">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="054c5-120">Minimálně byste vašeho projektu přidat odkazy na balíčky do:</span><span class="sxs-lookup"><span data-stu-id="054c5-120">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="054c5-121">`Microsoft.AspNetCore.Razor.Design` Balíček poskytuje Razor kompilace úlohy a cíle projektu.</span><span class="sxs-lookup"><span data-stu-id="054c5-121">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="054c5-122">Předchozí balíčky jsou součástí `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="054c5-122">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="054c5-123">Následující kód ukazuje soubor projektu, který používá sada Razor SDK k sestavení souborů Razor pro aplikace ASP.NET Core Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="054c5-123">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="054c5-124">`Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí balíčků [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="054c5-124">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="054c5-125">Ale bez verze `Microsoft.AspNetCore.App` odkaz na balíček poskytuje Microsoft.aspnetcore.all aplikaci, která neobsahuje nejnovější verzi `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="054c5-125">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="054c5-126">Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`) tak, aby byly zahrnuty nejnovější opravy čas sestavení pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-126">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="054c5-127">Další informace najdete v tématu [tento problém Githubu](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="054c5-127">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="054c5-128">Vlastnosti</span><span class="sxs-lookup"><span data-stu-id="054c5-128">Properties</span></span>

<span data-ttu-id="054c5-129">Následující vlastnosti řídit chování sady SDK pro syntaxi Razor jako součást sestavení projektu:</span><span class="sxs-lookup"><span data-stu-id="054c5-129">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="054c5-130">`RazorCompileOnBuild` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="054c5-130">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="054c5-131">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="054c5-131">Defaults to `true`.</span></span>
* <span data-ttu-id="054c5-132">`RazorCompileOnPublish` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="054c5-132">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="054c5-133">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="054c5-133">Defaults to `true`.</span></span>

<span data-ttu-id="054c5-134">Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="054c5-134">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

| <span data-ttu-id="054c5-135">Položky</span><span class="sxs-lookup"><span data-stu-id="054c5-135">Items</span></span> | <span data-ttu-id="054c5-136">Popis</span><span class="sxs-lookup"><span data-stu-id="054c5-136">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="054c5-137">Položka elementy ( *.cshtml* soubory), které jsou vstupy do cíle generování kódu.</span><span class="sxs-lookup"><span data-stu-id="054c5-137">Item elements (*.cshtml* files) that are inputs to code generation targets.</span></span> |
| `RazorCompile` | <span data-ttu-id="054c5-138">Položka elementy ( *.cs* soubory), které jsou vstupy do cíle kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-138">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="054c5-139">Použijte tento `ItemGroup` zadat další soubory se zkompiluje do sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-139">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="054c5-140">Položka prvků, které slouží ke kódu generovat atributy pro sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-140">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="054c5-141">Příklad:</span><span class="sxs-lookup"><span data-stu-id="054c5-141">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="054c5-142">Položky elementy přidané jako vložené prostředky do generovaného sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-142">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="054c5-143">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="054c5-143">Property</span></span> | <span data-ttu-id="054c5-144">Popis</span><span class="sxs-lookup"><span data-stu-id="054c5-144">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="054c5-145">Název souboru (bez přípony) sestavení vytvořené metodou Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-145">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="054c5-146">Výstupní adresář Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-146">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="054c5-147">Slouží k určení sady nástrojů použitý k vytvoření sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-147">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="054c5-148">Platné hodnoty jsou `Implicit`, `RazorSDK`, a `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="054c5-148">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="054c5-149">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="054c5-149">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="054c5-150">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="054c5-150">Default is `true`.</span></span> <span data-ttu-id="054c5-151">Když `true`, zahrnuje *web.config*, *.json*, a *.cshtml* soubory jako obsah v projektu.</span><span class="sxs-lookup"><span data-stu-id="054c5-151">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="054c5-152">Pokud je k dispozici prostřednictvím `Microsoft.NET.Sdk.Web`, soubory pod *wwwroot* a konfigurační soubory jsou zahrnuté také.</span><span class="sxs-lookup"><span data-stu-id="054c5-152">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="054c5-153">Když `true`, zahrnuje *.cshtml* souborů z `Content` položky v `RazorGenerate` položky.</span><span class="sxs-lookup"><span data-stu-id="054c5-153">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="054c5-154">Když `true`, generuje *.cs* soubor, který obsahuje atributy určené `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace.</span><span class="sxs-lookup"><span data-stu-id="054c5-154">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="054c5-155">Když `true`, přidá výchozí sadu atributů sestavení, které mají `RazorAssemblyAttribute`.</span><span class="sxs-lookup"><span data-stu-id="054c5-155">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="054c5-156">Když `true`, kopie `RazorGenerate` položky ( *.cshtml*) soubory do adresáře publikovat.</span><span class="sxs-lookup"><span data-stu-id="054c5-156">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="054c5-157">Obvykle nejsou publikované aplikace vyžadovat Razor soubory, pokud se účastní v sestavování v době sestavení nebo publikovat čas.</span><span class="sxs-lookup"><span data-stu-id="054c5-157">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="054c5-158">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="054c5-158">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="054c5-159">Když `true`, zkopírovat odkaz na sestavení položky do adresáře publikovat.</span><span class="sxs-lookup"><span data-stu-id="054c5-159">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="054c5-160">Obvykle nejsou publikované aplikace vyžadovat referenční sestavení, dojde v okamžiku sestavení nebo publikovat čas kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-160">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="054c5-161">Nastavte na `true` Pokud publikované aplikace vyžaduje kompilace modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="054c5-161">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="054c5-162">Například nastavte hodnotu na `true` Pokud aplikace změní *.cshtml* soubory za běhu nebo používá vložený zobrazení.</span><span class="sxs-lookup"><span data-stu-id="054c5-162">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="054c5-163">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="054c5-163">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="054c5-164">Když `true`, všechny položky obsahu Razor ( *.cshtml* soubory) jsou označeny k zahrnutí vygenerovaný balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="054c5-164">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="054c5-165">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="054c5-165">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="054c5-166">Když `true`, přidá RazorGenerate ( *.cshtml*) položky jako vložené soubory do generovaného sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-166">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="054c5-167">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="054c5-167">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="054c5-168">Když `true`, využívá proces serveru trvalé sestavení přesměrovat pracovní generování kódu.</span><span class="sxs-lookup"><span data-stu-id="054c5-168">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="054c5-169">Výchozí hodnota je hodnota `UseSharedCompilation`.</span><span class="sxs-lookup"><span data-stu-id="054c5-169">Defaults to the value of `UseSharedCompilation`.</span></span> |

<span data-ttu-id="054c5-170">Další informace o vlastnostech najdete v tématu [vlastnosti nástroje MSBuild](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="054c5-170">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="054c5-171">Cíle</span><span class="sxs-lookup"><span data-stu-id="054c5-171">Targets</span></span>

<span data-ttu-id="054c5-172">Sada Razor SDK definuje dva hlavní cíle:</span><span class="sxs-lookup"><span data-stu-id="054c5-172">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="054c5-173">`RazorGenerate` &ndash; Generuje kód *.cs* souborů z `RazorGenerate` položky elementy.</span><span class="sxs-lookup"><span data-stu-id="054c5-173">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="054c5-174">Použití `RazorGenerateDependsOn` vlastnosti a určit další cílí, který můžete spustit před nebo po tento cíl.</span><span class="sxs-lookup"><span data-stu-id="054c5-174">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="054c5-175">`RazorCompile` &ndash; Zkompiluje generované *.cs* soubory v sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="054c5-175">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="054c5-176">Použití `RazorCompileDependsOn` zadat další cíle, které můžete spustit před nebo po tento cíl.</span><span class="sxs-lookup"><span data-stu-id="054c5-176">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="054c5-177">Kompilace modulu runtime zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="054c5-177">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="054c5-178">Ve výchozím nastavení nebude sada Razor SDK publikovat referenční sestavení, které jsou nutné k provedení kompilace modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="054c5-178">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="054c5-179">Výsledkem je selhání kompilace při aplikační model využívá kompilace modulu runtime&mdash;například aplikace používá vložený zobrazení nebo změny zobrazení po publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="054c5-179">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="054c5-180">Nastavte `CopyRefAssembliesToPublishDirectory` k `true` můžete pokračovat v publikování referenční sestavení.</span><span class="sxs-lookup"><span data-stu-id="054c5-180">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="054c5-181">Pro webovou aplikaci, ujistěte se vaše aplikace cílí `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="054c5-181">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="054c5-182">Syntaxi Razor verze jazyka</span><span class="sxs-lookup"><span data-stu-id="054c5-182">Razor language version</span></span>

<span data-ttu-id="054c5-183">Při cílení `Microsoft.NET.Sdk.Web` Razor jazykovou verzi sady SDK, je odvozen z cílovou verzi rozhraní framework aplikace.</span><span class="sxs-lookup"><span data-stu-id="054c5-183">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the the app's target framework version.</span></span> <span data-ttu-id="054c5-184">Pro projekty cílené na `Microsoft.NET.Sdk.Razor` SDK nebo ve výjimečných případech, že aplikace vyžaduje jinou verzi jazyka Razor než odvozené hodnotu, se dají konfigurovat na verzi tak, že nastavíte `<RazorLangVersion>` vlastnost v souboru projektu vaší aplikace:</span><span class="sxs-lookup"><span data-stu-id="054c5-184">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

## <a name="additional-resources"></a><span data-ttu-id="054c5-185">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="054c5-185">Additional resources</span></span>

* [<span data-ttu-id="054c5-186">Dodatky k formátu csproj pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="054c5-186">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="054c5-187">Společné položky projektu nástroje MSBuild</span><span class="sxs-lookup"><span data-stu-id="054c5-187">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
