---
title: Kompilace souboru Razor v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak kompilace souborech Razor vyvolá se v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 11195f00e922f6817a0fa0988fad9d8082dea30a
ms.sourcegitcommit: a467828b5e4eaae291d961ffe2279a571900de23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58142322"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="7cb48-103">Kompilace souboru Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cb48-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="7cb48-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7cb48-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="7cb48-105">Soubor Razor je zkompilován za běhu, když uživatel vyvolá přidružené zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="7cb48-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="7cb48-106">Publikování souborů Razor čas sestavení není podporováno.</span><span class="sxs-lookup"><span data-stu-id="7cb48-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="7cb48-107">Volitelně mohou být zkompilovány soubory Razor na čas publikování a nasazen s aplikací&mdash;nástrojem pro předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="7cb48-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="7cb48-108">Souboru Razor je zkompilován za běhu, když uživatel vyvolá přidružené zobrazení stránky Razor nebo MVC.</span><span class="sxs-lookup"><span data-stu-id="7cb48-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="7cb48-109">Publikování souborů Razor čas sestavení není podporováno.</span><span class="sxs-lookup"><span data-stu-id="7cb48-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="7cb48-110">Volitelně mohou být zkompilovány soubory Razor na čas publikování a nasazen s aplikací&mdash;nástrojem pro předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="7cb48-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="7cb48-111">Souboru Razor je zkompilován za běhu, když uživatel vyvolá přidružené zobrazení stránky Razor nebo MVC.</span><span class="sxs-lookup"><span data-stu-id="7cb48-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="7cb48-112">Razor soubory kompilované za obě sestavení a publikování pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="7cb48-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7cb48-113">Razor soubory kompilované za obě sestavení a publikování pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="7cb48-113">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="7cb48-114">Kompilace modulu runtime může volitelně stará konfigurace vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="7cb48-114">Runtime compilation may be optionally enabled by configuring your application.</span></span>

::: moniker-end

## <a name="razor-compilation"></a><span data-ttu-id="7cb48-115">Kompilace Razor</span><span class="sxs-lookup"><span data-stu-id="7cb48-115">Razor compilation</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="7cb48-116">Kompilace sestavení a publikování běhu Razor souborů je povolené ve výchozím nastavení sada Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="7cb48-116">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="7cb48-117">Při povolené, kompilace modulu runtime, bude doplněk sestavení umožňující souborech Razor aktualizovat v případě, že jsou editied kompilace.</span><span class="sxs-lookup"><span data-stu-id="7cb48-117">When enabled, runtime compilation, will complement build time compilation allowing Razor files to be updated if they are editied.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="7cb48-118">Kompilace sestavení a publikování běhu Razor souborů je povolené ve výchozím nastavení sada Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="7cb48-118">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="7cb48-119">Úprava souborů Razor, až se aktualizují je podporována v okamžiku sestavení.</span><span class="sxs-lookup"><span data-stu-id="7cb48-119">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="7cb48-120">Ve výchozím nastavení pouze kompilované *Views.dll* a ne *.cshtml* soubory nebo odkazy na sestavení požadovaných pro kompilaci Razor soubory jsou nasazeny s vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="7cb48-120">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7cb48-121">Předkompilaci je zastaralá a v ASP.NET Core 3.0 se odebere.</span><span class="sxs-lookup"><span data-stu-id="7cb48-121">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="7cb48-122">Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="7cb48-122">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="7cb48-123">Sada Razor SDK nabídka platí jenom v případě, že žádné vlastnosti specifické pro předkompilaci jsou nastaveny v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="7cb48-123">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="7cb48-124">Například nastavení *.csproj* souboru `MvcRazorCompileOnPublish` vlastnost `true` zakáže Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="7cb48-124">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="7cb48-125">Pokud váš projekt cílí na .NET Framework, nainstalujte [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="7cb48-125">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="7cb48-126">Pokud váš projekt cílí na .NET Core, nejsou nutné žádné změny.</span><span class="sxs-lookup"><span data-stu-id="7cb48-126">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="7cb48-127">Šablony projektů ASP.NET Core 2.x implicitně nastavena `MvcRazorCompileOnPublish` vlastnost `true` ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="7cb48-127">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="7cb48-128">V důsledku toho tento prvek můžete ho bezpečně odebrat z *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="7cb48-128">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7cb48-129">Předkompilaci je zastaralá a v ASP.NET Core 3.0 se odebere.</span><span class="sxs-lookup"><span data-stu-id="7cb48-129">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="7cb48-130">Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="7cb48-130">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="7cb48-131">Předkompilace Razor soubor není k dispozici při provádění [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) v ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="7cb48-131">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="7cb48-132">Nastavte `MvcRazorCompileOnPublish` vlastnost `true`a nainstalujte [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="7cb48-132">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="7cb48-133">Následující *.csproj* ukázka zvýrazní tato nastavení:</span><span class="sxs-lookup"><span data-stu-id="7cb48-133">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="7cb48-134">Příprava aplikace pro [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd) s [příkazu rozhraní příkazového řádku .NET Core pro publikování](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="7cb48-134">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="7cb48-135">Můžete třeba spustíte následující příkaz v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="7cb48-135">For example, execute the following command at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="7cb48-136">A  *\<project_name >. PrecompiledViews.dll* soubor obsahující kompilovaných souborech Razor, je vytvořen při předkompilaci proběhne úspěšně.</span><span class="sxs-lookup"><span data-stu-id="7cb48-136">A *\<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="7cb48-137">Například následující snímek obrazovky znázorňuje obsah *Index.cshtml* v rámci *WebApplication1.PrecompiledViews.dll*:</span><span class="sxs-lookup"><span data-stu-id="7cb48-137">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![Zobrazení syntaxe Razor uvnitř knihovny DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a><span data-ttu-id="7cb48-139">Kompilace modulu runtime</span><span class="sxs-lookup"><span data-stu-id="7cb48-139">Runtime compilation</span></span>

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="7cb48-140">Kompilace sestavení doplňují kompilace modulu runtime souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="7cb48-140">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="7cb48-141">ASP.NET Core MVC Razor zkompiluje soubory při obsah *.cshtml* změna souboru.</span><span class="sxs-lookup"><span data-stu-id="7cb48-141">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7cb48-142">Kompilace sestavení doplňují kompilace modulu runtime souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="7cb48-142">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="7cb48-143"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> Získává nebo nastavuje hodnotu, která určuje, zda jsou soubory Razor (zobrazení syntaxe Razor a Razor Pages) znovu zkompilovat a pokud soubory na disku.</span><span class="sxs-lookup"><span data-stu-id="7cb48-143">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="7cb48-144">Výchozí hodnota je `true` pro:</span><span class="sxs-lookup"><span data-stu-id="7cb48-144">The default value is `true` for:</span></span>

* <span data-ttu-id="7cb48-145">Pokud je verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> nebo dřívější</span><span class="sxs-lookup"><span data-stu-id="7cb48-145">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier</span></span>
* <span data-ttu-id="7cb48-146">Pokud je verze kompatibility aplikace pro nastavení do sady <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> nebo novější a je aplikace ve vývojovém prostředí <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>.</span><span class="sxs-lookup"><span data-stu-id="7cb48-146">If the app's compatibility version is set to set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> or later and the app is in the Development environment <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>.</span></span> <span data-ttu-id="7cb48-147">Jinými slovy, souborech Razor nebude znovu zkompilovat v mimo vývojové prostředí není-li <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> explicitně nastavena.</span><span class="sxs-lookup"><span data-stu-id="7cb48-147">In other words, Razor files would not recompile in non-Development environment unless <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is explicitly set.</span></span>

<span data-ttu-id="7cb48-148">Pokyny a příklady nastavení verze kompatibility aplikace najdete v tématu <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="7cb48-148">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7cb48-149">Kompilace modulu runtime se aktivuje pomocí `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` balíčku.</span><span class="sxs-lookup"><span data-stu-id="7cb48-149">Runtime compilation is enabled using the `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package.</span></span> <span data-ttu-id="7cb48-150">Pokud chcete povolit kompilace modulu runtime, musí aplikace:</span><span class="sxs-lookup"><span data-stu-id="7cb48-150">To enable runtime compilation, apps must:</span></span>

* <span data-ttu-id="7cb48-151">Nainstalujte [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="7cb48-151">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
* <span data-ttu-id="7cb48-152">Aktualizace aplikace `ConfigureServices` zahrnout volání `AddMvcRazorRuntimeCompilation`:</span><span class="sxs-lookup"><span data-stu-id="7cb48-152">Update the application's `ConfigureServices` to include a call to `AddMvcRazorRuntimeCompilation`:</span></span>

  ```csharp
  services
      .AddMvc()
      .AddRazorRuntimeCompilation()
  ```

<span data-ttu-id="7cb48-153">Pro kompilaci modulu runtime pro práci při nasazení, musí aplikace upravit kromě jejich soubory projektu, chcete-li nastavit `PreserveCompilationReferences` k `true`.</span><span class="sxs-lookup"><span data-stu-id="7cb48-153">For runtime compilation to work when deployed, apps must additionally modify their project files to set the `PreserveCompilationReferences` to `true`.</span></span>

[!code-xml[](view-compilation/sample/RuntimeCompilation.csproj?highlight=3)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7cb48-154">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7cb48-154">Additional resources</span></span>

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
