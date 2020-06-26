---
title: Razorkompilace souborů v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak Razor probíhá kompilace souborů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: 71487ff2d5d7d7cf96835778f386e5f30fa32254
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405442"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Razor<span data-ttu-id="ace14-103">kompilace souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ace14-103"> file compilation in ASP.NET Core</span></span>

<span data-ttu-id="ace14-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ace14-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

Razor<span data-ttu-id="ace14-105">soubory s příponou *. cshtml* jsou kompilovány jak při sestavování, tak při publikování pomocí [ Razor sady SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="ace14-105"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="ace14-106">Kompilace za běhu může být volitelně povolena konfigurací projektu.</span><span class="sxs-lookup"><span data-stu-id="ace14-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="ace14-107">kompilace</span><span class="sxs-lookup"><span data-stu-id="ace14-107"> compilation</span></span>

<span data-ttu-id="ace14-108">RazorSada SDK ve výchozím nastavení povoluje kompilaci souborů pro čas sestavení a publikování Razor .</span><span class="sxs-lookup"><span data-stu-id="ace14-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="ace14-109">Pokud je povoleno, kompilace za běhu doplňuje kompilaci při sestavení a umožňuje Razor aktualizovat soubory, pokud jsou upravovány.</span><span class="sxs-lookup"><span data-stu-id="ace14-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="ace14-110">Povolit kompilaci za běhu při vytváření projektu</span><span class="sxs-lookup"><span data-stu-id="ace14-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="ace14-111">RazorŠablony projektů stránky a MVC obsahují možnost Povolit kompilaci za běhu při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="ace14-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="ace14-112">Tato možnost je podporovaná v ASP.NET Core 3,1 a novějších.</span><span class="sxs-lookup"><span data-stu-id="ace14-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ace14-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ace14-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ace14-114">V dialogovém okně **vytvořit novou ASP.NET Core webovou aplikaci** :</span><span class="sxs-lookup"><span data-stu-id="ace14-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="ace14-115">Vyberte šablonu projektu webová **aplikace** nebo **Webová aplikace (model-zobrazení-kontroler)** .</span><span class="sxs-lookup"><span data-stu-id="ace14-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="ace14-116">Zaškrtněte políčko **Povolit Razor kompilaci za běhu** .</span><span class="sxs-lookup"><span data-stu-id="ace14-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ace14-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ace14-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ace14-118">Použijte `-rrc` `--razor-runtime-compilation` možnost šablony nebo.</span><span class="sxs-lookup"><span data-stu-id="ace14-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="ace14-119">Například následující příkaz vytvoří nový Razor projekt stránky s povolenou kompilací Runtime:</span><span class="sxs-lookup"><span data-stu-id="ace14-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="ace14-120">Povolit kompilaci za běhu v existujícím projektu</span><span class="sxs-lookup"><span data-stu-id="ace14-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="ace14-121">Povolení kompilace za běhu pro všechna prostředí v existujícím projektu:</span><span class="sxs-lookup"><span data-stu-id="ace14-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="ace14-122">Nainstalujte [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Balíček NuGet RuntimeCompilation</span><span class="sxs-lookup"><span data-stu-id="ace14-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="ace14-123">Aktualizujte metodu projektu `Startup.ConfigureServices` tak, aby zahrnovala volání <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="ace14-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="ace14-124">Například:</span><span class="sxs-lookup"><span data-stu-id="ace14-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="ace14-125">Podmíněně povolit kompilaci za běhu v existujícím projektu</span><span class="sxs-lookup"><span data-stu-id="ace14-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="ace14-126">Je možné povolit kompilaci za běhu, aby byla dostupná pouze pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="ace14-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="ace14-127">Podmíněné povolení tímto způsobem zajistí, že publikovaný výstup:</span><span class="sxs-lookup"><span data-stu-id="ace14-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="ace14-128">Používá kompilovaná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ace14-128">Uses compiled views.</span></span>
* <span data-ttu-id="ace14-129">Nepovoluje sledovací procesy souborů v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="ace14-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="ace14-130">Chcete-li povolit kompilaci za běhu pouze ve vývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="ace14-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="ace14-131">Nainstalujte [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Balíček NuGet RuntimeCompilation</span><span class="sxs-lookup"><span data-stu-id="ace14-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="ace14-132">Upravte část spouštěcí profil `environmentVariables` v *launchSettings.jsdne*:</span><span class="sxs-lookup"><span data-stu-id="ace14-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="ace14-133">Ověřte `ASPNETCORE_ENVIRONMENT` , že je nastavené na `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="ace14-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="ace14-134">Nastavte `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` na `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` .</span><span class="sxs-lookup"><span data-stu-id="ace14-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="ace14-135">V následujícím příkladu je kompilace modulu runtime ve vývojovém prostředí povolena pro `IIS Express` `RazorPagesApp` profily spuštění a:</span><span class="sxs-lookup"><span data-stu-id="ace14-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="ace14-136">Ve třídě projektu nejsou potřeba žádné změny kódu `Startup` .</span><span class="sxs-lookup"><span data-stu-id="ace14-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="ace14-137">V době běhu ASP.NET Core vyhledá [atribut HostingStartup na úrovni sestavení](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) v `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="ace14-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="ace14-138">`HostingStartup`Atribut určuje spouštěcí kód aplikace, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="ace14-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="ace14-139">Tento spouštěcí kód umožňuje kompilaci za běhu.</span><span class="sxs-lookup"><span data-stu-id="ace14-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="ace14-140">Povolit kompilaci za běhu pro Razor knihovnu tříd</span><span class="sxs-lookup"><span data-stu-id="ace14-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="ace14-141">Vezměte v úvahu scénář, ve kterém se Razor projekt stránky odkazuje na [ Razor knihovnu tříd (RCL)](xref:razor-pages/ui-class) s názvem *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="ace14-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="ace14-142">RCL obsahuje soubor *_Layout. cshtml* , který využívá všechny projekty MVC a Razor Pages vašeho týmu.</span><span class="sxs-lookup"><span data-stu-id="ace14-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="ace14-143">Chcete povolit kompilaci za běhu pro soubor *_Layout. cshtml* v tomto RCL.</span><span class="sxs-lookup"><span data-stu-id="ace14-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="ace14-144">Proveďte následující změny v Razor projektu stránky:</span><span class="sxs-lookup"><span data-stu-id="ace14-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="ace14-145">Povolit kompilaci za běhu pomocí instrukcí v [podmíněně povolit kompilaci za běhu v existujícím projektu](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="ace14-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="ace14-146">Nakonfigurujte možnosti kompilace za běhu v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ace14-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="ace14-147">V předchozím kódu je vytvořena absolutní cesta k *MyClassLib* RCL.</span><span class="sxs-lookup"><span data-stu-id="ace14-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="ace14-148">[Rozhraní PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) se používá k nalezení adresářů a souborů v této absolutní cestě.</span><span class="sxs-lookup"><span data-stu-id="ace14-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="ace14-149">Nakonec `PhysicalFileProvider` je instance přidána do kolekce zprostředkovatelů souborů, která umožňuje přístup k souborům *. cshtml* RCL.</span><span class="sxs-lookup"><span data-stu-id="ace14-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ace14-150">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ace14-150">Additional resources</span></span>

* <span data-ttu-id="ace14-151">Vlastnosti [RazorCompileOnBuild a RazorCompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="ace14-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor<span data-ttu-id="ace14-152">soubory s příponou *. cshtml* jsou kompilovány jak při sestavování, tak při publikování pomocí [ Razor sady SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="ace14-152"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="ace14-153">Kompilace za běhu může být volitelně povolena konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="ace14-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="ace14-154">kompilace</span><span class="sxs-lookup"><span data-stu-id="ace14-154"> compilation</span></span>

<span data-ttu-id="ace14-155">RazorSada SDK ve výchozím nastavení povoluje kompilaci souborů pro čas sestavení a publikování Razor .</span><span class="sxs-lookup"><span data-stu-id="ace14-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="ace14-156">Pokud je povoleno, kompilace za běhu doplňuje kompilaci při sestavení a umožňuje Razor aktualizovat soubory, pokud jsou upravovány.</span><span class="sxs-lookup"><span data-stu-id="ace14-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="ace14-157">Kompilace za běhu</span><span class="sxs-lookup"><span data-stu-id="ace14-157">Runtime compilation</span></span>

<span data-ttu-id="ace14-158">Povolení kompilace za běhu pro všechna prostředí a režimy konfigurace:</span><span class="sxs-lookup"><span data-stu-id="ace14-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="ace14-159">Nainstalujte [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Balíček NuGet RuntimeCompilation</span><span class="sxs-lookup"><span data-stu-id="ace14-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="ace14-160">Aktualizujte metodu projektu `Startup.ConfigureServices` tak, aby zahrnovala volání <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="ace14-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="ace14-161">Například:</span><span class="sxs-lookup"><span data-stu-id="ace14-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="ace14-162">Podmíněně povolit kompilaci za běhu</span><span class="sxs-lookup"><span data-stu-id="ace14-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="ace14-163">Je možné povolit kompilaci za běhu, aby byla dostupná pouze pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="ace14-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="ace14-164">Podmíněné povolení tímto způsobem zajistí, že publikovaný výstup:</span><span class="sxs-lookup"><span data-stu-id="ace14-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="ace14-165">Používá kompilovaná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ace14-165">Uses compiled views.</span></span>
* <span data-ttu-id="ace14-166">Má menší velikost.</span><span class="sxs-lookup"><span data-stu-id="ace14-166">Is smaller in size.</span></span>
* <span data-ttu-id="ace14-167">Nepovoluje sledovací procesy souborů v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="ace14-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="ace14-168">Povolení kompilace za běhu na základě prostředí a konfiguračního režimu:</span><span class="sxs-lookup"><span data-stu-id="ace14-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="ace14-169">Podmíněně odkázat na [Microsoft. AspNetCore. Mvc. Razor .. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) balíček na základě aktivní `Configuration` hodnoty:</span><span class="sxs-lookup"><span data-stu-id="ace14-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="ace14-170">Aktualizujte metodu projektu `Startup.ConfigureServices` tak, aby zahrnovala volání `AddRazorRuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="ace14-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="ace14-171">Podmíněně `AddRazorRuntimeCompilation` se spustí tak, aby běžela pouze v režimu ladění, pokud `ASPNETCORE_ENVIRONMENT` je proměnná nastavena na `Development` :</span><span class="sxs-lookup"><span data-stu-id="ace14-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="ace14-172">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ace14-172">Additional resources</span></span>

* <span data-ttu-id="ace14-173">Vlastnosti [RazorCompileOnBuild a RazorCompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="ace14-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="ace14-174">Ukázku, která ukazuje, jak pracovat s kompilací runtime napříč projekty, najdete v [ukázce kompilace za běhu na GitHubu](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) .</span><span class="sxs-lookup"><span data-stu-id="ace14-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ace14-175">RazorSoubor je zkompilován za běhu, když Razor je vyvolána přidružená stránka nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="ace14-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> Razor<span data-ttu-id="ace14-176">soubory jsou kompilovány jak při sestavování, tak při publikování pomocí [ Razor sady SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="ace14-176"> files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="ace14-177">kompilace</span><span class="sxs-lookup"><span data-stu-id="ace14-177"> compilation</span></span>

<span data-ttu-id="ace14-178">RazorSada SDK ve výchozím nastavení povoluje kompilaci souborů sestavení a publikování Razor .</span><span class="sxs-lookup"><span data-stu-id="ace14-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="ace14-179">Úpravy Razor souborů po jejich aktualizaci jsou podporovány v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="ace14-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="ace14-180">Ve výchozím nastavení jsou ve vaší aplikaci nasazeny pouze zkompilované *Views.dll* a žádné soubory *. cshtml* nebo odkazy na sestavení nutná ke kompilaci Razor souborů.</span><span class="sxs-lookup"><span data-stu-id="ace14-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ace14-181">Předkompilace nástroje je zastaralá a bude odebrána v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="ace14-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="ace14-182">Doporučujeme migrovat na [ Razor sadu SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="ace14-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="ace14-183">RazorSada SDK je platná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předem zkompilování.</span><span class="sxs-lookup"><span data-stu-id="ace14-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="ace14-184">Například nastavením vlastnosti souboru *. csproj* `MvcRazorCompileOnPublish` `true` zakážete Razor sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="ace14-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="ace14-185">Kompilace za běhu</span><span class="sxs-lookup"><span data-stu-id="ace14-185">Runtime compilation</span></span>

<span data-ttu-id="ace14-186">Kompilace v době sestavení je doplněna kompilací souborů za běhu Razor .</span><span class="sxs-lookup"><span data-stu-id="ace14-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="ace14-187">ASP.NET Core MVC bude Razor po změně obsahu souboru *. cshtml* znovu kompilovat soubory.</span><span class="sxs-lookup"><span data-stu-id="ace14-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ace14-188">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ace14-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
