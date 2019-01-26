---
title: Kompilace souboru Razor a předkompilace v ASP.NET Core
author: rick-anderson
description: Přečtěte si o výhodách předkompilace Razor soubory a jak provádět Razor předkompilace souboru v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 2720708f8e58fdc55b82bfb56665005170e79934
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889753"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="47b96-103">Kompilace souboru Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47b96-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="47b96-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="47b96-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="47b96-105">Soubor Razor je zkompilován za běhu, když uživatel vyvolá přidružené zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="47b96-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="47b96-106">Publikování souborů Razor čas sestavení není podporováno.</span><span class="sxs-lookup"><span data-stu-id="47b96-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="47b96-107">Volitelně mohou být zkompilovány soubory Razor na čas publikování a nasazen s aplikací&mdash;nástrojem pro předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="47b96-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="47b96-108">Souboru Razor je zkompilován za běhu, když uživatel vyvolá přidružené zobrazení stránky Razor nebo MVC.</span><span class="sxs-lookup"><span data-stu-id="47b96-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="47b96-109">Publikování souborů Razor čas sestavení není podporováno.</span><span class="sxs-lookup"><span data-stu-id="47b96-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="47b96-110">Volitelně mohou být zkompilovány soubory Razor na čas publikování a nasazen s aplikací&mdash;nástrojem pro předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="47b96-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="47b96-111">Souboru Razor je zkompilován za běhu, když uživatel vyvolá přidružené zobrazení stránky Razor nebo MVC.</span><span class="sxs-lookup"><span data-stu-id="47b96-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="47b96-112">Razor soubory kompilované za obě sestavení a publikování pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="47b96-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

## <a name="precompilation-considerations"></a><span data-ttu-id="47b96-113">Předkompilace důležité informace</span><span class="sxs-lookup"><span data-stu-id="47b96-113">Precompilation considerations</span></span>

<span data-ttu-id="47b96-114">Tady jsou vedlejší účinky předkompilace souborech Razor:</span><span class="sxs-lookup"><span data-stu-id="47b96-114">The following are side effects of precompiling Razor files:</span></span>

* <span data-ttu-id="47b96-115">Menší publikované sady</span><span class="sxs-lookup"><span data-stu-id="47b96-115">A smaller published bundle</span></span>
* <span data-ttu-id="47b96-116">Rychlejší spuštění</span><span class="sxs-lookup"><span data-stu-id="47b96-116">A faster startup time</span></span>
* <span data-ttu-id="47b96-117">Nelze upravovat soubory Razor&mdash;chybí související obsah ze sady publikované.</span><span class="sxs-lookup"><span data-stu-id="47b96-117">You can't edit Razor files&mdash;the associated content is absent from the published bundle.</span></span>

## <a name="deploy-precompiled-files"></a><span data-ttu-id="47b96-118">Předkompilované soubory nasazení</span><span class="sxs-lookup"><span data-stu-id="47b96-118">Deploy precompiled files</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="47b96-119">Kompilace sestavení a publikování běhu Razor souborů je povolené ve výchozím nastavení sada Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="47b96-119">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="47b96-120">Úprava souborů Razor, až se aktualizují je podporována v okamžiku sestavení.</span><span class="sxs-lookup"><span data-stu-id="47b96-120">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="47b96-121">Ve výchozím nastavení pouze kompilované *Views.dll* a ne *.cshtml* jsou soubory nasazeny s vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="47b96-121">By default, only the compiled *Views.dll* and no *.cshtml* files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47b96-122">V ASP.NET Core 3.0 se odebere předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="47b96-122">The precompilation tool will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="47b96-123">Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="47b96-123">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="47b96-124">Sada Razor SDK nabídka platí jenom v případě, že žádné vlastnosti specifické pro předkompilaci jsou nastaveny v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="47b96-124">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="47b96-125">Například nastavení *.csproj* souboru `MvcRazorCompileOnPublish` vlastnost `true` zakáže Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="47b96-125">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="47b96-126">Pokud váš projekt cílí na .NET Framework, nainstalujte [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="47b96-126">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="47b96-127">Pokud váš projekt cílí na .NET Core, nejsou nutné žádné změny.</span><span class="sxs-lookup"><span data-stu-id="47b96-127">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="47b96-128">Šablony projektů ASP.NET Core 2.x implicitně nastavena `MvcRazorCompileOnPublish` vlastnost `true` ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="47b96-128">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="47b96-129">V důsledku toho tento prvek můžete ho bezpečně odebrat z *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="47b96-129">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47b96-130">V ASP.NET Core 3.0 se odebere předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="47b96-130">The precompilation tool will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="47b96-131">Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="47b96-131">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="47b96-132">Předkompilace Razor soubor není k dispozici při provádění [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) v ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="47b96-132">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="47b96-133">Nastavte `MvcRazorCompileOnPublish` vlastnost `true`a nainstalujte [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="47b96-133">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="47b96-134">Následující *.csproj* ukázka zvýrazní tato nastavení:</span><span class="sxs-lookup"><span data-stu-id="47b96-134">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="47b96-135">Příprava aplikace pro [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd) s [příkazu rozhraní příkazového řádku .NET Core pro publikování](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="47b96-135">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="47b96-136">Můžete třeba spustíte následující příkaz v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="47b96-136">For example, execute the following command at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="47b96-137">A *< project_name >. PrecompiledViews.dll* soubor obsahující kompilovaných souborech Razor, je vytvořen při předkompilaci proběhne úspěšně.</span><span class="sxs-lookup"><span data-stu-id="47b96-137">A *<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="47b96-138">Například následující snímek obrazovky znázorňuje obsah *Index.cshtml* v rámci *WebApplication1.PrecompiledViews.dll*:</span><span class="sxs-lookup"><span data-stu-id="47b96-138">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![Zobrazení syntaxe Razor uvnitř knihovny DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="recompile-razor-files-on-change"></a><span data-ttu-id="47b96-140">Znovu zkompilovat soubory Razor při změně</span><span class="sxs-lookup"><span data-stu-id="47b96-140">Recompile Razor files on change</span></span>

<span data-ttu-id="47b96-141"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> `AllowRecompilingViewsOnFileChange` Získává nebo nastavuje hodnotu, která určuje, zda jsou soubory Razor (zobrazení syntaxe Razor a Razor Pages) znovu zkompilovat a pokud soubory na disku.</span><span class="sxs-lookup"><span data-stu-id="47b96-141">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> `AllowRecompilingViewsOnFileChange` gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="47b96-142">Pokud je nastavena na `true`, [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nakonfigurovaný Watch pro změny v souborech Razor v <xref:Microsoft.Extensions.FileProviders.IFileProvider> instancí.</span><span class="sxs-lookup"><span data-stu-id="47b96-142">When set to `true`, [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) watches for changes to Razor files in configured <xref:Microsoft.Extensions.FileProviders.IFileProvider> instances.</span></span>

<span data-ttu-id="47b96-143">Výchozí hodnota je `true` pro:</span><span class="sxs-lookup"><span data-stu-id="47b96-143">The default value is `true` for:</span></span>

* <span data-ttu-id="47b96-144">Aplikace ASP.NET Core 2.1 nebo dřívější.</span><span class="sxs-lookup"><span data-stu-id="47b96-144">ASP.NET Core 2.1 or earlier apps.</span></span>
* <span data-ttu-id="47b96-145">Aplikace ASP.NET Core 2.2 nebo vyšší ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="47b96-145">ASP.NET Core 2.2 or later apps in the Development environment.</span></span>

<span data-ttu-id="47b96-146">`AllowRecompilingViewsOnFileChange` souvisí s přepínačem kompatibilitu a může poskytnout různé chování v závislosti na nakonfigurovaných kompatibility verzí pro aplikace.</span><span class="sxs-lookup"><span data-stu-id="47b96-146">`AllowRecompilingViewsOnFileChange` is associated with a compatibility switch and can provide different behavior depending on the configured compatibility version for the app.</span></span> <span data-ttu-id="47b96-147">Konfigurace aplikace tak, že nastavíte `AllowRecompilingViewsOnFileChange` má přednost před hodnoty odvozené od verze kompatibility aplikace.</span><span class="sxs-lookup"><span data-stu-id="47b96-147">Configuring the app by setting `AllowRecompilingViewsOnFileChange` takes precedence over the value implied by the app's compatibility version.</span></span>

<span data-ttu-id="47b96-148">Pokud verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> nebo starší, `AllowRecompilingViewsOnFileChange` je nastavena na `true` dokud explicitně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="47b96-148">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier, `AllowRecompilingViewsOnFileChange` is set to `true` unless explicitly configured.</span></span>

<span data-ttu-id="47b96-149">Pokud je verze kompatibility aplikace nastavená na `CompatibilityVersion.Version_2_2` nebo novější, `AllowRecompilingViewsOnFileChange` je nastavena na `false` není-li prostředí vývoj nebo explicitně nastavená hodnotu.</span><span class="sxs-lookup"><span data-stu-id="47b96-149">If the app's compatibility version is set to `CompatibilityVersion.Version_2_2` or later, `AllowRecompilingViewsOnFileChange` is set to `false` unless the environment is Development or the value is explicitly configured.</span></span>

<span data-ttu-id="47b96-150">Pokyny a příklady nastavení verze kompatibility aplikace najdete v tématu <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="47b96-150">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="47b96-151">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="47b96-151">Additional resources</span></span>

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
