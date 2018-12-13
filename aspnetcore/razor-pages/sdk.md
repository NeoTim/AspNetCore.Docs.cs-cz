---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/25/2018
uid: razor-pages/sdk
ms.openlocfilehash: 2df7dc4234207d3dbac8a4ff47751adc8fc6a192
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284436"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="725fe-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="725fe-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="725fe-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="725fe-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="725fe-105">[!INCLUDE[](~/includes/2.1-SDK.md)] Zahrnuje `Microsoft.NET.Sdk.Razor` sady MSBuild SDK (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="725fe-105">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="725fe-106">Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="725fe-106">The Razor SDK:</span></span>

* <span data-ttu-id="725fe-107">Standardizuje prostředí týkající se vytváření, balení a publikování projektů, které obsahují [Razor](xref:mvc/views/razor) souborů pro projekty ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="725fe-107">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="725fe-108">Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-108">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="725fe-109">Požadavky</span><span class="sxs-lookup"><span data-stu-id="725fe-109">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="using-the-razor-sdk"></a><span data-ttu-id="725fe-110">Pomocí syntaxe Razor SDK</span><span class="sxs-lookup"><span data-stu-id="725fe-110">Using the Razor SDK</span></span>

<span data-ttu-id="725fe-111">Většina webových aplikací není nutné explicitně odkazují na sadu SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-111">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

<span data-ttu-id="725fe-112">Použití sady SDK Razor k vytvoření knihovny tříd obsahující zobrazení syntaxe Razor nebo stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="725fe-112">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="725fe-113">Použití `Microsoft.NET.Sdk.Razor` místo `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="725fe-113">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    ...
  </Project>
  ```

* <span data-ttu-id="725fe-114">Obvykle, odkaz na balíček pro `Microsoft.AspNetCore.Mvc` se vyžaduje pro příjem Další závislosti, které jsou potřebné k sestavení a kompilace Razor Pages a zobrazeními Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-114">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="725fe-115">Minimálně byste vašeho projektu přidat odkazy na balíčky do:</span><span class="sxs-lookup"><span data-stu-id="725fe-115">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
    
  <span data-ttu-id="725fe-116">`Microsoft.AspNetCore.Razor.Design` Balíček poskytuje Razor kompilace úlohy a cíle projektu.</span><span class="sxs-lookup"><span data-stu-id="725fe-116">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="725fe-117">Předchozí balíčky jsou součástí `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="725fe-117">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="725fe-118">Následující kód ukazuje soubor projektu, který používá sada Razor SDK k sestavení souborů Razor pro aplikace ASP.NET Core Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="725fe-118">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="725fe-119">`Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí balíčků [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="725fe-119">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="725fe-120">Ale bez verze `Microsoft.AspNetCore.App` odkaz na balíček poskytuje Microsoft.aspnetcore.all aplikaci, která neobsahuje nejnovější verzi `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="725fe-120">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="725fe-121">Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`) tak, aby byly zahrnuty nejnovější opravy čas sestavení pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-121">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="725fe-122">Další informace najdete v tématu [tento problém Githubu](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="725fe-122">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="725fe-123">Vlastnosti</span><span class="sxs-lookup"><span data-stu-id="725fe-123">Properties</span></span>

<span data-ttu-id="725fe-124">Následující vlastnosti řídit chování sady SDK pro syntaxi Razor jako součást sestavení projektu:</span><span class="sxs-lookup"><span data-stu-id="725fe-124">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="725fe-125">`RazorCompileOnBuild` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="725fe-125">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="725fe-126">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="725fe-126">Defaults to `true`.</span></span>
* <span data-ttu-id="725fe-127">`RazorCompileOnPublish` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="725fe-127">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="725fe-128">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="725fe-128">Defaults to `true`.</span></span>

<span data-ttu-id="725fe-129">Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="725fe-129">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

| <span data-ttu-id="725fe-130">Položky</span><span class="sxs-lookup"><span data-stu-id="725fe-130">Items</span></span> | <span data-ttu-id="725fe-131">Popis</span><span class="sxs-lookup"><span data-stu-id="725fe-131">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="725fe-132">Položka elementy (*.cshtml* soubory), které jsou vstupy do cíle generování kódu.</span><span class="sxs-lookup"><span data-stu-id="725fe-132">Item elements (*.cshtml* files) that are inputs to code generation targets.</span></span> |
| `RazorCompile` | <span data-ttu-id="725fe-133">Položka elementy (*.cs* soubory), které jsou vstupy do cíle kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-133">Item elements (*.cs* files) that are inputs to  Razor compilation targets.</span></span> <span data-ttu-id="725fe-134">Pomocí této ItemGroup můžete určit další soubory se zkompiluje do sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-134">Use this ItemGroup to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="725fe-135">Položka prvků, které slouží ke kódu generovat atributy pro sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-135">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="725fe-136">Příklad:</span><span class="sxs-lookup"><span data-stu-id="725fe-136">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="725fe-137">Položky elementy přidané jako vložené prostředky do generovaného sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-137">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="725fe-138">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="725fe-138">Property</span></span> | <span data-ttu-id="725fe-139">Popis</span><span class="sxs-lookup"><span data-stu-id="725fe-139">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="725fe-140">Název souboru (bez přípony) sestavení vytvořené metodou Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-140">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="725fe-141">Výstupní adresář Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-141">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="725fe-142">Slouží k určení sady nástrojů použitý k vytvoření sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-142">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="725fe-143">Platné hodnoty jsou `Implicit`, `RazorSDK`, a `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="725fe-143">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| `EnableDefaultContentItems` | <span data-ttu-id="725fe-144">Když `true`, obsahuje určité typy souborů, jako například *.cshtml* soubory jako obsah v projektu.</span><span class="sxs-lookup"><span data-stu-id="725fe-144">When `true`, includes certain file types, such as *.cshtml* files, as content in the project.</span></span> <span data-ttu-id="725fe-145">Pokud je k dispozici prostřednictvím `Microsoft.NET.Sdk.Web`, soubory pod *wwwroot* a konfigurační soubory jsou zahrnuté také.</span><span class="sxs-lookup"><span data-stu-id="725fe-145">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="725fe-146">Když `true`, zahrnuje *.cshtml* souborů z `Content` položky v `RazorGenerate` položky.</span><span class="sxs-lookup"><span data-stu-id="725fe-146">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="725fe-147">Když `true`, generuje *.cs* soubor, který obsahuje atributy určené `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace.</span><span class="sxs-lookup"><span data-stu-id="725fe-147">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="725fe-148">Když `true`, přidá výchozí sadu atributů sestavení, které mají `RazorAssemblyAttribute`.</span><span class="sxs-lookup"><span data-stu-id="725fe-148">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="725fe-149">Když `true`, kopie `RazorGenerate` položky (*.cshtml*) soubory do adresáře publikovat.</span><span class="sxs-lookup"><span data-stu-id="725fe-149">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="725fe-150">Obvykle nejsou publikované aplikace vyžadovat Razor soubory, pokud se účastní v sestavování v době sestavení nebo publikovat čas.</span><span class="sxs-lookup"><span data-stu-id="725fe-150">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="725fe-151">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="725fe-151">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="725fe-152">Když `true`, zkopírovat odkaz na sestavení položky do adresáře publikovat.</span><span class="sxs-lookup"><span data-stu-id="725fe-152">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="725fe-153">Obvykle nejsou publikované aplikace vyžadovat referenční sestavení, dojde v okamžiku sestavení nebo publikovat čas kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-153">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="725fe-154">Nastavte na `true` Pokud publikované aplikace vyžaduje kompilace modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="725fe-154">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="725fe-155">Například nastavte hodnotu na `true` Pokud aplikace změní *.cshtml* soubory za běhu nebo používá vložený zobrazení.</span><span class="sxs-lookup"><span data-stu-id="725fe-155">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="725fe-156">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="725fe-156">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="725fe-157">Když `true`, všechny položky obsahu Razor (*.cshtml* soubory) jsou označeny k zahrnutí vygenerovaný balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="725fe-157">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="725fe-158">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="725fe-158">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="725fe-159">Když `true`, přidá RazorGenerate (*.cshtml*) položky jako vložené soubory do generovaného sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-159">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="725fe-160">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="725fe-160">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="725fe-161">Když `true`, využívá proces serveru trvalé sestavení přesměrovat pracovní generování kódu.</span><span class="sxs-lookup"><span data-stu-id="725fe-161">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="725fe-162">Výchozí hodnota je hodnota `UseSharedCompilation`.</span><span class="sxs-lookup"><span data-stu-id="725fe-162">Defaults to the value of `UseSharedCompilation`.</span></span> |

### <a name="targets"></a><span data-ttu-id="725fe-163">Cíle</span><span class="sxs-lookup"><span data-stu-id="725fe-163">Targets</span></span>

<span data-ttu-id="725fe-164">Sada Razor SDK definuje dva hlavní cíle:</span><span class="sxs-lookup"><span data-stu-id="725fe-164">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="725fe-165">`RazorGenerate` &ndash; Generuje kód *.cs* souborů z `RazorGenerate` položky elementy.</span><span class="sxs-lookup"><span data-stu-id="725fe-165">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="725fe-166">Použití `RazorGenerateDependsOn` vlastnosti a určit další cílí, který můžete spustit před nebo po tento cíl.</span><span class="sxs-lookup"><span data-stu-id="725fe-166">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="725fe-167">`RazorCompile` &ndash; Zkompiluje generované *.cs* soubory v sestavení Razor.</span><span class="sxs-lookup"><span data-stu-id="725fe-167">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="725fe-168">Použití `RazorCompileDependsOn` zadat další cíle, které můžete spustit před nebo po tento cíl.</span><span class="sxs-lookup"><span data-stu-id="725fe-168">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="725fe-169">Kompilace modulu runtime zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="725fe-169">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="725fe-170">Ve výchozím nastavení nebude sada Razor SDK publikovat referenční sestavení, které jsou nutné k provedení kompilace modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="725fe-170">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="725fe-171">Výsledkem je selhání kompilace při aplikační model využívá kompilace modulu runtime&mdash;například aplikace používá vložený zobrazení nebo změny zobrazení po publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="725fe-171">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="725fe-172">Nastavte `CopyRefAssembliesToPublishDirectory` k `true` můžete pokračovat v publikování referenční sestavení.</span><span class="sxs-lookup"><span data-stu-id="725fe-172">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="725fe-173">Pro webovou aplikaci, ujistěte se vaše aplikace cílí `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="725fe-173">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>
