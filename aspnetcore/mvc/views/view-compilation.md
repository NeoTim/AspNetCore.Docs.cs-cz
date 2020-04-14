---
title: Kompilace souborů Razor v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak probíhá kompilace souborů Razor v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277263"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="f1556-103">Kompilace souborů Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1556-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="f1556-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f1556-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="f1556-105">Razor soubory s *příponou .cshtml* jsou kompilovány na obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="f1556-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="f1556-106">Runtime kompilace může být volitelně povolena konfigurací projektu.</span><span class="sxs-lookup"><span data-stu-id="f1556-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="f1556-107">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="f1556-107">Razor compilation</span></span>

<span data-ttu-id="f1556-108">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="f1556-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="f1556-109">Pokud je povolena, kompilace runtime doplňuje kompilaci v době sestavení, což umožňuje aktualizaci souborů Razor, pokud jsou upraveny.</span><span class="sxs-lookup"><span data-stu-id="f1556-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="f1556-110">Povolit kompilaci za běhu při vytváření projektu</span><span class="sxs-lookup"><span data-stu-id="f1556-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="f1556-111">Šablony projektu Razor Pages a MVC obsahují možnost povolit kompilaci za běhu při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="f1556-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="f1556-112">Tato možnost je podporována v ASP.NET jádrem 3.1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="f1556-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1556-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1556-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f1556-114">V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci:**</span><span class="sxs-lookup"><span data-stu-id="f1556-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="f1556-115">Vyberte šablonu projektu **Webová aplikace** nebo **Webová aplikace (Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="f1556-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="f1556-116">Zaškrtněte políčko **Povolit kompilaci modulu runtime razor.**</span><span class="sxs-lookup"><span data-stu-id="f1556-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f1556-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="f1556-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f1556-118">Použijte `-rrc` možnost `--razor-runtime-compilation` nebo šablonu.</span><span class="sxs-lookup"><span data-stu-id="f1556-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="f1556-119">Například následující příkaz vytvoří nový projekt Razor Pages s povolenou kompilací runtime:</span><span class="sxs-lookup"><span data-stu-id="f1556-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="f1556-120">Povolení kompilace za běhu v existujícím projektu</span><span class="sxs-lookup"><span data-stu-id="f1556-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="f1556-121">Povolení kompilace za běhu pro všechna prostředí v existujícím projektu:</span><span class="sxs-lookup"><span data-stu-id="f1556-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="f1556-122">Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="f1556-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="f1556-123">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="f1556-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="f1556-124">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f1556-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="f1556-125">Podmíněné povolení kompilace za běhu v existujícím projektu</span><span class="sxs-lookup"><span data-stu-id="f1556-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="f1556-126">Runtime kompilace může být povolena tak, že je k dispozici pouze pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="f1556-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="f1556-127">Podmíněné povolení tímto způsobem zajišťuje, že publikovaný výstup:</span><span class="sxs-lookup"><span data-stu-id="f1556-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="f1556-128">Používá zkompilovaná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f1556-128">Uses compiled views.</span></span>
* <span data-ttu-id="f1556-129">Neumožňuje sledování souborů v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="f1556-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="f1556-130">Povolení kompilace za běhu pouze ve vývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="f1556-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="f1556-131">Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="f1556-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="f1556-132">Upravte oddíl `environmentVariables` profilu spuštění v *souboru launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f1556-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="f1556-133">Ověřte, zda `ASPNETCORE_ENVIRONMENT` je nastavena na `"Development"`.</span><span class="sxs-lookup"><span data-stu-id="f1556-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="f1556-134">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` Nastaveno `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`na .</span><span class="sxs-lookup"><span data-stu-id="f1556-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="f1556-135">V následujícím příkladu je ve vývojovém prostředí `IIS Express` pro `RazorPagesApp` profily a spouštěcí profily povolena kompilace runtime:</span><span class="sxs-lookup"><span data-stu-id="f1556-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="f1556-136">Ve `Startup` třídě projektu nejsou potřeba žádné změny kódu.</span><span class="sxs-lookup"><span data-stu-id="f1556-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="f1556-137">Za běhu ASP.NET Core hledá [atribut HostingStartup na](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`úrovni sestavení v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="f1556-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="f1556-138">Atribut `HostingStartup` určuje spouštěcí kód aplikace, který má být spuštěn.</span><span class="sxs-lookup"><span data-stu-id="f1556-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="f1556-139">Tento spouštěcí kód umožňuje kompilaci runtime.</span><span class="sxs-lookup"><span data-stu-id="f1556-139">That startup code enables runtime compilation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1556-140">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f1556-140">Additional resources</span></span>

* <span data-ttu-id="f1556-141">[Vlastnosti RazorCompileOnBuild a RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="f1556-141">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="f1556-142">Podívejte se [na ukázku kompilace runtime na GitHubu](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pro ukázku, která ukazuje, že kompilace runtime funguje napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="f1556-142">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="f1556-143">Razor soubory s *příponou .cshtml* jsou kompilovány na obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="f1556-143">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="f1556-144">Kompilace runtime může být volitelně povolena konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="f1556-144">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="f1556-145">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="f1556-145">Razor compilation</span></span>

<span data-ttu-id="f1556-146">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="f1556-146">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="f1556-147">Pokud je povolena, kompilace runtime doplňuje kompilaci v době sestavení, což umožňuje aktualizaci souborů Razor, pokud jsou upraveny.</span><span class="sxs-lookup"><span data-stu-id="f1556-147">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="f1556-148">Runtime kompilace</span><span class="sxs-lookup"><span data-stu-id="f1556-148">Runtime compilation</span></span>

<span data-ttu-id="f1556-149">Povolení kompilace runtime pro všechna prostředí a režimy konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f1556-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="f1556-150">Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="f1556-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="f1556-151">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="f1556-151">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="f1556-152">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f1556-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="f1556-153">Podmíněně povolit kompilaci runtime</span><span class="sxs-lookup"><span data-stu-id="f1556-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="f1556-154">Runtime kompilace může být povolena tak, že je k dispozici pouze pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="f1556-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="f1556-155">Podmíněné povolení tímto způsobem zajišťuje, že publikovaný výstup:</span><span class="sxs-lookup"><span data-stu-id="f1556-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="f1556-156">Používá zkompilovaná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f1556-156">Uses compiled views.</span></span>
* <span data-ttu-id="f1556-157">Je menší velikost.</span><span class="sxs-lookup"><span data-stu-id="f1556-157">Is smaller in size.</span></span>
* <span data-ttu-id="f1556-158">Neumožňuje sledování souborů v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="f1556-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="f1556-159">Povolení kompilace runtime na základě prostředí a režimu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f1556-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="f1556-160">Podmíněně odkazovat [na microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) `Configuration` balíček na základě aktivní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="f1556-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="f1556-161">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, `AddRazorRuntimeCompilation`aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="f1556-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="f1556-162">Podmíněně `AddRazorRuntimeCompilation` spustit tak, že běží pouze `ASPNETCORE_ENVIRONMENT` v režimu `Development`ladění, pokud je proměnná nastavena na :</span><span class="sxs-lookup"><span data-stu-id="f1556-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="f1556-163">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f1556-163">Additional resources</span></span>

* <span data-ttu-id="f1556-164">[Vlastnosti RazorCompileOnBuild a RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="f1556-164">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="f1556-165">Podívejte se [na ukázku kompilace runtime na GitHubu](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pro ukázku, která ukazuje, že kompilace runtime funguje napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="f1556-165">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f1556-166">Soubor Razor je kompilován za běhu, když je vyvolána přidružená stránka razor nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="f1556-166">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="f1556-167">Razor soubory jsou kompilovány v obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="f1556-167">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="f1556-168">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="f1556-168">Razor compilation</span></span>

<span data-ttu-id="f1556-169">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="f1556-169">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="f1556-170">Úpravy souborů Razor po jejich aktualizaci jsou podporovány v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="f1556-170">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="f1556-171">Ve výchozím nastavení jsou v aplikaci nasazeny pouze zkompilované soubory *Views.dll* a žádná sestavení *.cshtml* nebo odkazy potřebné ke kompilaci souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="f1556-171">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f1556-172">Nástroj pro předkompilaci byl zastarala a budou odebrány v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="f1556-172">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="f1556-173">Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="f1556-173">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="f1556-174">Sada Razor SDK je účinná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="f1556-174">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="f1556-175">Například nastavení `MvcRazorCompileOnPublish` vlastnosti souboru *.csproj* na `true` zakázání sady Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="f1556-175">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="f1556-176">Runtime kompilace</span><span class="sxs-lookup"><span data-stu-id="f1556-176">Runtime compilation</span></span>

<span data-ttu-id="f1556-177">Kompilace v době sestavení je doplněna runtime kompilací souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="f1556-177">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="f1556-178">ASP.NET Core MVC překompiluje soubory Razor, když se změní obsah souboru *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="f1556-178">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1556-179">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f1556-179">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
