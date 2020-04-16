---
title: Kompilace souborů Razor v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak probíhá kompilace souborů Razor v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440932"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="e0d6a-103">Kompilace souborů Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0d6a-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="e0d6a-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0d6a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="e0d6a-105">Razor soubory s *příponou .cshtml* jsou kompilovány na obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="e0d6a-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="e0d6a-106">Runtime kompilace může být volitelně povolena konfigurací projektu.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="e0d6a-107">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="e0d6a-107">Razor compilation</span></span>

<span data-ttu-id="e0d6a-108">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="e0d6a-109">Pokud je povolena, kompilace runtime doplňuje kompilaci v době sestavení, což umožňuje aktualizaci souborů Razor, pokud jsou upraveny.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="e0d6a-110">Povolit kompilaci za běhu při vytváření projektu</span><span class="sxs-lookup"><span data-stu-id="e0d6a-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="e0d6a-111">Šablony projektu Razor Pages a MVC obsahují možnost povolit kompilaci za běhu při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="e0d6a-112">Tato možnost je podporována v ASP.NET jádrem 3.1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e0d6a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0d6a-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e0d6a-114">V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci:**</span><span class="sxs-lookup"><span data-stu-id="e0d6a-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="e0d6a-115">Vyberte šablonu projektu **Webová aplikace** nebo **Webová aplikace (Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="e0d6a-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="e0d6a-116">Zaškrtněte políčko **Povolit kompilaci modulu runtime razor.**</span><span class="sxs-lookup"><span data-stu-id="e0d6a-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e0d6a-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="e0d6a-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e0d6a-118">Použijte `-rrc` možnost `--razor-runtime-compilation` nebo šablonu.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="e0d6a-119">Například následující příkaz vytvoří nový projekt Razor Pages s povolenou kompilací runtime:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="e0d6a-120">Povolení kompilace za běhu v existujícím projektu</span><span class="sxs-lookup"><span data-stu-id="e0d6a-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="e0d6a-121">Povolení kompilace za běhu pro všechna prostředí v existujícím projektu:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="e0d6a-122">Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="e0d6a-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="e0d6a-123">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="e0d6a-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="e0d6a-124">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="e0d6a-125">Podmíněné povolení kompilace za běhu v existujícím projektu</span><span class="sxs-lookup"><span data-stu-id="e0d6a-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="e0d6a-126">Runtime kompilace může být povolena tak, že je k dispozici pouze pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="e0d6a-127">Podmíněné povolení tímto způsobem zajišťuje, že publikovaný výstup:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="e0d6a-128">Používá zkompilovaná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-128">Uses compiled views.</span></span>
* <span data-ttu-id="e0d6a-129">Neumožňuje sledování souborů v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="e0d6a-130">Povolení kompilace za běhu pouze ve vývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="e0d6a-131">Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="e0d6a-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="e0d6a-132">Upravte oddíl `environmentVariables` profilu spuštění v *souboru launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="e0d6a-133">Ověřte, zda `ASPNETCORE_ENVIRONMENT` je nastavena na `"Development"`.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="e0d6a-134">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` Nastaveno `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`na .</span><span class="sxs-lookup"><span data-stu-id="e0d6a-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="e0d6a-135">V následujícím příkladu je ve vývojovém prostředí `IIS Express` pro `RazorPagesApp` profily a spouštěcí profily povolena kompilace runtime:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="e0d6a-136">Ve `Startup` třídě projektu nejsou potřeba žádné změny kódu.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="e0d6a-137">Za běhu ASP.NET Core hledá [atribut HostingStartup na](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`úrovni sestavení v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="e0d6a-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="e0d6a-138">Atribut `HostingStartup` určuje spouštěcí kód aplikace, který má být spuštěn.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="e0d6a-139">Tento spouštěcí kód umožňuje kompilaci runtime.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="e0d6a-140">Povolení kompilace za běhu pro knihovnu tříd Razor</span><span class="sxs-lookup"><span data-stu-id="e0d6a-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="e0d6a-141">Zvažte scénář, ve kterém projekt Razor Pages odkazuje na [knihovnu třídy Razor (RCL)](xref:razor-pages/ui-class) s názvem *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="e0d6a-142">RCL obsahuje soubor *_Layout.cshtml,* který všechny projekty MVC a Razor Pages vašeho týmu spotřebovávají.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="e0d6a-143">Chcete povolit kompilaci modulu runtime pro soubor *_Layout.cshtml* v tomto rcl.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="e0d6a-144">Proveďte následující změny v projektu Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="e0d6a-145">Povolit kompilaci runtime s pokyny na [Podmíněně povolit kompilaci runtime v existujícím projektu](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="e0d6a-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="e0d6a-146">Konfigurace možností kompilace `Startup.ConfigureServices`runtime v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="e0d6a-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="e0d6a-147">V předchozím kódu je vytvořena absolutní cesta k *MyClassLib* RCL.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="e0d6a-148">Rozhraní [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) se používá k vyhledání adresářů a souborů na této absolutní cestě.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="e0d6a-149">Nakonec je `PhysicalFileProvider` instance přidána do kolekce zprostředkovatelů souborů, která umožňuje přístup k souborům *.cshtml* rcl.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0d6a-150">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e0d6a-150">Additional resources</span></span>

* <span data-ttu-id="e0d6a-151">[Vlastnosti RazorCompileOnBuild a RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="e0d6a-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="e0d6a-152">Razor soubory s *příponou .cshtml* jsou kompilovány na obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="e0d6a-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="e0d6a-153">Kompilace runtime může být volitelně povolena konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="e0d6a-154">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="e0d6a-154">Razor compilation</span></span>

<span data-ttu-id="e0d6a-155">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="e0d6a-156">Pokud je povolena, kompilace runtime doplňuje kompilaci v době sestavení, což umožňuje aktualizaci souborů Razor, pokud jsou upraveny.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="e0d6a-157">Runtime kompilace</span><span class="sxs-lookup"><span data-stu-id="e0d6a-157">Runtime compilation</span></span>

<span data-ttu-id="e0d6a-158">Povolení kompilace runtime pro všechna prostředí a režimy konfigurace:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="e0d6a-159">Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="e0d6a-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="e0d6a-160">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="e0d6a-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="e0d6a-161">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="e0d6a-162">Podmíněně povolit kompilaci runtime</span><span class="sxs-lookup"><span data-stu-id="e0d6a-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="e0d6a-163">Runtime kompilace může být povolena tak, že je k dispozici pouze pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="e0d6a-164">Podmíněné povolení tímto způsobem zajišťuje, že publikovaný výstup:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="e0d6a-165">Používá zkompilovaná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-165">Uses compiled views.</span></span>
* <span data-ttu-id="e0d6a-166">Je menší velikost.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-166">Is smaller in size.</span></span>
* <span data-ttu-id="e0d6a-167">Neumožňuje sledování souborů v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="e0d6a-168">Povolení kompilace runtime na základě prostředí a režimu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="e0d6a-169">Podmíněně odkazovat [na microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) `Configuration` balíček na základě aktivní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="e0d6a-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="e0d6a-170">Aktualizujte `Startup.ConfigureServices` metodu projektu tak, `AddRazorRuntimeCompilation`aby zahrnovala volání aplikace .</span><span class="sxs-lookup"><span data-stu-id="e0d6a-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="e0d6a-171">Podmíněně `AddRazorRuntimeCompilation` spustit tak, že běží pouze `ASPNETCORE_ENVIRONMENT` v režimu `Development`ladění, pokud je proměnná nastavena na :</span><span class="sxs-lookup"><span data-stu-id="e0d6a-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="e0d6a-172">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e0d6a-172">Additional resources</span></span>

* <span data-ttu-id="e0d6a-173">[Vlastnosti RazorCompileOnBuild a RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="e0d6a-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="e0d6a-174">Podívejte se [na ukázku kompilace runtime na GitHubu](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pro ukázku, která ukazuje, že kompilace runtime funguje napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e0d6a-175">Soubor Razor je kompilován za běhu, když je vyvolána přidružená stránka razor nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="e0d6a-176">Razor soubory jsou kompilovány v obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="e0d6a-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="e0d6a-177">Kompilace holicího strojku</span><span class="sxs-lookup"><span data-stu-id="e0d6a-177">Razor compilation</span></span>

<span data-ttu-id="e0d6a-178">Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="e0d6a-179">Úpravy souborů Razor po jejich aktualizaci jsou podporovány v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="e0d6a-180">Ve výchozím nastavení jsou v aplikaci nasazeny pouze zkompilované soubory *Views.dll* a žádná sestavení *.cshtml* nebo odkazy potřebné ke kompilaci souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e0d6a-181">Nástroj pro předkompilaci byl zastarala a budou odebrány v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="e0d6a-182">Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="e0d6a-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="e0d6a-183">Sada Razor SDK je účinná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předkompilaci.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="e0d6a-184">Například nastavení `MvcRazorCompileOnPublish` vlastnosti souboru *.csproj* na `true` zakázání sady Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="e0d6a-185">Runtime kompilace</span><span class="sxs-lookup"><span data-stu-id="e0d6a-185">Runtime compilation</span></span>

<span data-ttu-id="e0d6a-186">Kompilace v době sestavení je doplněna runtime kompilací souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="e0d6a-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="e0d6a-187">ASP.NET Core MVC překompiluje soubory Razor, když se změní obsah souboru *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="e0d6a-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0d6a-188">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e0d6a-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
