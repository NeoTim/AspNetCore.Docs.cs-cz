---
title: Kompilace souborů Razor v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak probíhá kompilace souborů Razor v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994609"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="92709-103">Kompilace souborů Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92709-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="92709-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92709-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="92709-105">Razor soubory s *příponou .cshtml* jsou kompilovány na obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="92709-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="92709-106">Kompilace runtime může být volitelně povolena konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="92709-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="92709-107">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="92709-107">Razor compilation</span></span>

<span data-ttu-id="92709-108">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="92709-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="92709-109">Pokud je povolena, kompilace runtime doplňuje kompilaci v době sestavení, což umožňuje aktualizaci souborů Razor, pokud jsou upraveny.</span><span class="sxs-lookup"><span data-stu-id="92709-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="92709-110">Runtime kompilace</span><span class="sxs-lookup"><span data-stu-id="92709-110">Runtime compilation</span></span>

<span data-ttu-id="92709-111">Povolení kompilace runtime pro všechna prostředí a režimy konfigurace:</span><span class="sxs-lookup"><span data-stu-id="92709-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="92709-112">Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="92709-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="92709-113">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, `AddRazorRuntimeCompilation`aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="92709-113">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="92709-114">Příklad:</span><span class="sxs-lookup"><span data-stu-id="92709-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="92709-115">Podmíněně povolit kompilaci runtime</span><span class="sxs-lookup"><span data-stu-id="92709-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="92709-116">Runtime kompilace může být povolena tak, že je k dispozici pouze pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="92709-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="92709-117">Podmíněné povolení tímto způsobem zajišťuje, že publikovaný výstup:</span><span class="sxs-lookup"><span data-stu-id="92709-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="92709-118">Používá zkompilovaná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92709-118">Uses compiled views.</span></span>
* <span data-ttu-id="92709-119">Je menší velikost.</span><span class="sxs-lookup"><span data-stu-id="92709-119">Is smaller in size.</span></span>
* <span data-ttu-id="92709-120">Neumožňuje sledování souborů v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="92709-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="92709-121">Povolení kompilace runtime na základě prostředí a režimu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="92709-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="92709-122">Podmíněně odkazovat [na microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) `Configuration` balíček na základě aktivní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="92709-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="92709-123">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, `AddRazorRuntimeCompilation`aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="92709-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="92709-124">Podmíněně `AddRazorRuntimeCompilation` spustit tak, že běží pouze `ASPNETCORE_ENVIRONMENT` v režimu `Development`ladění, pokud je proměnná nastavena na :</span><span class="sxs-lookup"><span data-stu-id="92709-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="92709-125">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="92709-125">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="92709-126">Soubor Razor je kompilován za běhu, když je vyvolána přidružená stránka razor nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="92709-126">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="92709-127">Razor soubory jsou kompilovány v obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="92709-127">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="92709-128">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="92709-128">Razor compilation</span></span>

<span data-ttu-id="92709-129">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="92709-129">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="92709-130">Úpravy souborů Razor po jejich aktualizaci jsou podporovány v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="92709-130">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="92709-131">Ve výchozím nastavení jsou v aplikaci nasazeny pouze zkompilované soubory *Views.dll* a žádná sestavení *.cshtml* nebo odkazy potřebné ke kompilaci souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="92709-131">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="92709-132">Nástroj pro předkompilaci byl zastarala a budou odebrány v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="92709-132">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="92709-133">Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="92709-133">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="92709-134">Sada Razor SDK je účinná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="92709-134">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="92709-135">Například nastavení `MvcRazorCompileOnPublish` vlastnosti souboru *.csproj* na `true` zakázání sady Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="92709-135">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="92709-136">Runtime kompilace</span><span class="sxs-lookup"><span data-stu-id="92709-136">Runtime compilation</span></span>

<span data-ttu-id="92709-137">Kompilace v době sestavení je doplněna runtime kompilací souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="92709-137">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="92709-138">ASP.NET Core MVC překompiluje soubory Razor, když se změní obsah souboru *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="92709-138">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92709-139">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="92709-139">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end