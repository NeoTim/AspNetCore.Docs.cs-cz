---
title: Kompilace souborů Razor v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak probíhá kompilace souborů Razor v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223956"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="c0d20-103">Kompilace souborů Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0d20-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="c0d20-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0d20-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c0d20-105">Razor soubory s *příponou .cshtml* jsou kompilovány na obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c0d20-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c0d20-106">Kompilace runtime může být volitelně povolena konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="c0d20-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="c0d20-107">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="c0d20-107">Razor compilation</span></span>

<span data-ttu-id="c0d20-108">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="c0d20-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="c0d20-109">Pokud je povolena, kompilace runtime doplňuje kompilaci v době sestavení, což umožňuje aktualizaci souborů Razor, pokud jsou upraveny.</span><span class="sxs-lookup"><span data-stu-id="c0d20-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="c0d20-110">Runtime kompilace</span><span class="sxs-lookup"><span data-stu-id="c0d20-110">Runtime compilation</span></span>

<span data-ttu-id="c0d20-111">Povolení kompilace runtime pro všechna prostředí a režimy konfigurace:</span><span class="sxs-lookup"><span data-stu-id="c0d20-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="c0d20-112">Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="c0d20-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="c0d20-113">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="c0d20-113">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="c0d20-114">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c0d20-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="c0d20-115">Podmíněně povolit kompilaci runtime</span><span class="sxs-lookup"><span data-stu-id="c0d20-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="c0d20-116">Runtime kompilace může být povolena tak, že je k dispozici pouze pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="c0d20-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="c0d20-117">Podmíněné povolení tímto způsobem zajišťuje, že publikovaný výstup:</span><span class="sxs-lookup"><span data-stu-id="c0d20-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="c0d20-118">Používá zkompilovaná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c0d20-118">Uses compiled views.</span></span>
* <span data-ttu-id="c0d20-119">Je menší velikost.</span><span class="sxs-lookup"><span data-stu-id="c0d20-119">Is smaller in size.</span></span>
* <span data-ttu-id="c0d20-120">Neumožňuje sledování souborů v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="c0d20-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="c0d20-121">Povolení kompilace runtime na základě prostředí a režimu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="c0d20-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="c0d20-122">Podmíněně odkazovat [na microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) `Configuration` balíček na základě aktivní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="c0d20-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="c0d20-123">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, `AddRazorRuntimeCompilation`aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="c0d20-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="c0d20-124">Podmíněně `AddRazorRuntimeCompilation` spustit tak, že běží pouze `ASPNETCORE_ENVIRONMENT` v režimu `Development`ladění, pokud je proměnná nastavena na :</span><span class="sxs-lookup"><span data-stu-id="c0d20-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c0d20-125">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c0d20-125">Additional resources</span></span>

* <span data-ttu-id="c0d20-126">[Vlastnosti RazorCompileOnBuild a RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="c0d20-126">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="c0d20-127">Podívejte se [na ukázku runtimekompilace na GitHubu](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pro ukázku, která ukazuje, že kompilace za běhu funguje napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="c0d20-127">See the [runtimecompilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c0d20-128">Soubor Razor je kompilován za běhu, když je vyvolána přidružená stránka razor nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="c0d20-128">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="c0d20-129">Razor soubory jsou kompilovány v obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c0d20-129">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="c0d20-130">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="c0d20-130">Razor compilation</span></span>

<span data-ttu-id="c0d20-131">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="c0d20-131">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="c0d20-132">Úpravy souborů Razor po jejich aktualizaci jsou podporovány v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="c0d20-132">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="c0d20-133">Ve výchozím nastavení jsou v aplikaci nasazeny pouze zkompilované soubory *Views.dll* a žádná sestavení *.cshtml* nebo odkazy potřebné ke kompilaci souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="c0d20-133">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c0d20-134">Nástroj pro předkompilaci byl zastarala a budou odebrány v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="c0d20-134">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="c0d20-135">Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c0d20-135">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="c0d20-136">Sada Razor SDK je účinná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="c0d20-136">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="c0d20-137">Například nastavení `MvcRazorCompileOnPublish` vlastnosti souboru *.csproj* na `true` zakázání sady Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="c0d20-137">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="c0d20-138">Runtime kompilace</span><span class="sxs-lookup"><span data-stu-id="c0d20-138">Runtime compilation</span></span>

<span data-ttu-id="c0d20-139">Kompilace v době sestavení je doplněna runtime kompilací souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="c0d20-139">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="c0d20-140">ASP.NET Core MVC překompiluje soubory Razor, když se změní obsah souboru *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="c0d20-140">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0d20-141">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c0d20-141">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
