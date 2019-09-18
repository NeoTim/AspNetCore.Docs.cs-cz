---
title: Kompilace souboru Razor v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak probíhá kompilace souborů Razor v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/20/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 0aa632bce32ef44f65d92639284c64c1d00e952e
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080816"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="225ae-103">Kompilace souboru Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="225ae-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="225ae-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="225ae-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="225ae-105">Soubor Razor je kompilován za běhu, když je vyvolána přidružená zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="225ae-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="225ae-106">Publikování souboru Razor v čase sestavení není podporováno.</span><span class="sxs-lookup"><span data-stu-id="225ae-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="225ae-107">Soubory Razor mohou být volitelně kompilovány v době publikování a nasazeny s&mdash;aplikací pomocí nástroje předkompilace.</span><span class="sxs-lookup"><span data-stu-id="225ae-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="225ae-108">Soubor Razor se zkompiluje za běhu, když se vyvolá přidružená stránka Razor nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="225ae-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="225ae-109">Publikování souboru Razor v čase sestavení není podporováno.</span><span class="sxs-lookup"><span data-stu-id="225ae-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="225ae-110">Soubory Razor mohou být volitelně kompilovány v době publikování a nasazeny s&mdash;aplikací pomocí nástroje předkompilace.</span><span class="sxs-lookup"><span data-stu-id="225ae-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="225ae-111">Soubor Razor se zkompiluje za běhu, když se vyvolá přidružená stránka Razor nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="225ae-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="225ae-112">Soubory Razor jsou kompilovány jak při sestavování, tak při publikování pomocí [sady Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="225ae-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="225ae-113">Soubory Razor jsou kompilovány jak při sestavování, tak při publikování pomocí [sady Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="225ae-113">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="225ae-114">Kompilace za běhu může být volitelně povolena konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="225ae-114">Runtime compilation may be optionally enabled by configuring your application.</span></span>

::: moniker-end

## <a name="razor-compilation"></a><span data-ttu-id="225ae-115">Kompilace Razor</span><span class="sxs-lookup"><span data-stu-id="225ae-115">Razor compilation</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="225ae-116">Kompilace souborů Razor založená na sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="225ae-116">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="225ae-117">Pokud je povoleno, kompilace za běhu doplňuje kompilaci času sestavení a umožňuje aktualizaci souborů Razor, pokud jsou upravovány.</span><span class="sxs-lookup"><span data-stu-id="225ae-117">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="225ae-118">Kompilace souborů Razor založená na sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="225ae-118">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="225ae-119">Úpravy souborů Razor po jejich aktualizaci jsou podporovány v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="225ae-119">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="225ae-120">Ve výchozím nastavení se do vaší aplikace nasazují pouze kompilovaná *zobrazení. dll* a žádné soubory *. cshtml* nebo odkazy na sestavení, která jsou nutná pro zkompilování souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="225ae-120">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="225ae-121">Předkompilace nástroje je zastaralá a bude odebrána v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="225ae-121">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="225ae-122">Doporučujeme, abyste provedli migraci na [sadu Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="225ae-122">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="225ae-123">Sada Razor SDK je platná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="225ae-123">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="225ae-124">Například nastavením `MvcRazorCompileOnPublish` vlastnosti `true` souboru *. csproj* zakážete sadu Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="225ae-124">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="225ae-125">Pokud je projekt cílen .NET Framework, nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) :</span><span class="sxs-lookup"><span data-stu-id="225ae-125">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="225ae-126">Pokud váš projekt cílí na .NET Core, nemusíte dělat žádné změny.</span><span class="sxs-lookup"><span data-stu-id="225ae-126">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="225ae-127">Šablony projektu ASP.NET Core 2. x implicitně nastavují `MvcRazorCompileOnPublish` vlastnost na `true` hodnotu standardně.</span><span class="sxs-lookup"><span data-stu-id="225ae-127">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="225ae-128">V důsledku toho lze tento prvek bezpečně odebrat ze souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="225ae-128">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="225ae-129">Předkompilace nástroje je zastaralá a bude odebrána v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="225ae-129">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="225ae-130">Doporučujeme, abyste provedli migraci na [sadu Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="225ae-130">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="225ae-131">Předkompilace souboru Razor není k dispozici při provádění [samostatně zahrnutého nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) v ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="225ae-131">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="225ae-132">Nastavte vlastnost na `true`a nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. ViewCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) `MvcRazorCompileOnPublish`</span><span class="sxs-lookup"><span data-stu-id="225ae-132">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="225ae-133">Následující ukázka *. csproj* zvýrazní tato nastavení:</span><span class="sxs-lookup"><span data-stu-id="225ae-133">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="225ae-134">Připravte aplikaci pro [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd) pomocí [příkazu .NET Core CLI publikovat](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="225ae-134">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="225ae-135">Například spusťte následující příkaz v kořenu projektu:</span><span class="sxs-lookup"><span data-stu-id="225ae-135">For example, execute the following command at the project root:</span></span>

```dotnetcli
dotnet publish -c Release
```

<span data-ttu-id="225ae-136">PROJECT_NAME  *>\<. Soubor PrecompiledViews. dll* obsahující zkompilované soubory Razor je vytvořen po úspěšném dokončení předkompilace.</span><span class="sxs-lookup"><span data-stu-id="225ae-136">A *\<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="225ae-137">Například následující snímek obrazovky znázorňuje obsah souboru *index. cshtml* v rámci *WebApplication1. PrecompiledViews. dll*:</span><span class="sxs-lookup"><span data-stu-id="225ae-137">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![Zobrazení Razor v knihovně DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a><span data-ttu-id="225ae-139">Kompilace za běhu</span><span class="sxs-lookup"><span data-stu-id="225ae-139">Runtime compilation</span></span>

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="225ae-140">Kompilace v době sestavení je doplněna kompilací souborů Razor za běhu.</span><span class="sxs-lookup"><span data-stu-id="225ae-140">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="225ae-141">ASP.NET Core MVC bude znovu kompilovat soubory Razor, když se změní obsah souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="225ae-141">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="225ae-142">Kompilace v době sestavení je doplněna kompilací souborů Razor za běhu.</span><span class="sxs-lookup"><span data-stu-id="225ae-142">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="225ae-143"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> Získávánebonastavujehodnotu,kteráurčuje,jestlisesouboryRazor(zobrazeníRazoraRazorPages)majíznovuzkompilovataaktualizovat,pokudse<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> soubory na disku mění.</span><span class="sxs-lookup"><span data-stu-id="225ae-143">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="225ae-144">Výchozí hodnota je `true` :</span><span class="sxs-lookup"><span data-stu-id="225ae-144">The default value is `true` for:</span></span>

* <span data-ttu-id="225ae-145">Pokud je verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> nebo starší</span><span class="sxs-lookup"><span data-stu-id="225ae-145">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier</span></span>
* <span data-ttu-id="225ae-146">Pokud je verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> nebo novější a aplikace se nachází ve vývojovém prostředí. <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*></span><span class="sxs-lookup"><span data-stu-id="225ae-146">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> or later and the app is in the Development environment <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>.</span></span> <span data-ttu-id="225ae-147">Jinými slovy, soubory Razor nebudou znovu zkompilovány v nevývojovém prostředí, pokud <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> není explicitně nastaveno.</span><span class="sxs-lookup"><span data-stu-id="225ae-147">In other words, Razor files would not recompile in non-Development environment unless <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is explicitly set.</span></span>

<span data-ttu-id="225ae-148">Pokyny a příklady nastavení verze kompatibility aplikace najdete v tématu <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="225ae-148">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="225ae-149">Kompilace modulu runtime je povolena pomocí `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` balíčku.</span><span class="sxs-lookup"><span data-stu-id="225ae-149">Runtime compilation is enabled using the `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package.</span></span> <span data-ttu-id="225ae-150">Aby bylo možné povolit kompilaci za běhu, aplikace musí:</span><span class="sxs-lookup"><span data-stu-id="225ae-150">To enable runtime compilation, apps must:</span></span>

* <span data-ttu-id="225ae-151">Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) .</span><span class="sxs-lookup"><span data-stu-id="225ae-151">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
* <span data-ttu-id="225ae-152">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, aby zahrnovala `AddRazorRuntimeCompilation`volání:</span><span class="sxs-lookup"><span data-stu-id="225ae-152">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddRazorRuntimeCompilation();
  ```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="225ae-153">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="225ae-153">Additional resources</span></span>

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
