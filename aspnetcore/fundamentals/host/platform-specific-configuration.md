---
title: Použití hostování spouštěcích sestavení v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vylepšit ASP.NET Core aplikaci z externího sestavení pomocí implementace IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 0636c62c4373533234ab252d64052b476b123bbf
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405091"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="ba8fe-103">Použití hostování spouštěcích sestavení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba8fe-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="ba8fe-104">Od [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba8fe-105"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="ba8fe-106">Externí knihovna může například použít hostitelskou implementaci při spuštění k poskytnutí dalších poskytovatelů konfigurace nebo služeb do aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="ba8fe-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ba8fe-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="ba8fe-108">HostingStartup – atribut</span><span class="sxs-lookup"><span data-stu-id="ba8fe-108">HostingStartup attribute</span></span>

<span data-ttu-id="ba8fe-109">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="ba8fe-110">Pro zadání sestavení nebo sestavení obsahujícího `Startup` třídu se automaticky vyhledá `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ba8fe-111">Seznam sestavení pro vyhledávání `HostingStartup` atributů je načten za běhu z konfigurace ve [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="ba8fe-112">Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="ba8fe-113">V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="ba8fe-114">Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ba8fe-115">`HostingStartup`Atribut je obvykle umístěn v souboru implementační třídy sestavení, který spouští `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="ba8fe-116">Najít načtená hostování spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="ba8fe-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="ba8fe-117">Chcete-li zjistit načtená hostování spouštěcích sestavení, povolte protokolování a ověřte protokoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="ba8fe-118">Chyby, ke kterým dochází při načítání sestavení, jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="ba8fe-119">Načtená hostující spouštěcí sestavení jsou protokolována na úrovni ladění a všechny chyby jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="ba8fe-120">Zakázat automatické načítání hostujících spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="ba8fe-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="ba8fe-121">Chcete-li zakázat automatické načítání hostujících spouštěcích sestavení, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="ba8fe-122">Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1` :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="ba8fe-123">Zabránit hostování nastavení konfigurace hostitele po spuštění:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="ba8fe-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="ba8fe-125">Chcete-li zabránit tomu, aby se konkrétní hostování spouštěcích sestavení načetlo, nastavte jednu z následujících hodnot na řetězec, který je hostitelem spouštěcích sestavení s oddělovači, aby při spuštění vyloučil:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="ba8fe-126">Hostování nastavení konfigurace vyloučení sestavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="ba8fe-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="ba8fe-128">Pokud je nastavené nastavení konfigurace hostitele i proměnná prostředí, řídí se chování nastavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="ba8fe-129">Zakázání hostování spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="ba8fe-130">Project</span><span class="sxs-lookup"><span data-stu-id="ba8fe-130">Project</span></span>

<span data-ttu-id="ba8fe-131">Vytvořte hostování po spuštění s některým z následujících typů projektů:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="ba8fe-132">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="ba8fe-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="ba8fe-133">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="ba8fe-134">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="ba8fe-134">Class library</span></span>

<span data-ttu-id="ba8fe-135">Rozšíření spouštění hostitele lze poskytnout v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="ba8fe-136">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ba8fe-137">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor stránku aplikace, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="ba8fe-138">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-138">The class library:</span></span>

* <span data-ttu-id="ba8fe-139">Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ba8fe-140">`ServiceKeyInjection`Přidá dvojici řetězců služby k konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="ba8fe-141">Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu pro spuštění hostingu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="ba8fe-142">`ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="ba8fe-143">*HostingStartupLibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ba8fe-144">Stránka indexu aplikace načte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostujícím sestavením pro spuštění knihovny tříd:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="ba8fe-145">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="ba8fe-146">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="ba8fe-147">Balíček má stejné charakteristiky jako knihovna tříd popsané výše.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="ba8fe-148">Balíček:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-148">The package:</span></span>

* <span data-ttu-id="ba8fe-149">Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ba8fe-150">`ServiceKeyInjection`Přidá do konfigurace aplikace dvojici řetězců služby.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="ba8fe-151">Obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ba8fe-152">*HostingStartupPackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ba8fe-153">Stránka indexu aplikace čte a vykresluje konfigurační hodnoty pro dva klíče nastavené hostujícím sestavením pro spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="ba8fe-154">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="ba8fe-155">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-155">Console app without an entry point</span></span>

<span data-ttu-id="ba8fe-156">*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="ba8fe-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="ba8fe-157">Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="ba8fe-158">Publikování konzolové aplikace vytváří hostující spouštěcí sestavení, které lze spotřebovat z běhového úložiště.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="ba8fe-159">V tomto procesu se používá Konzolová aplikace bez vstupního bodu, protože:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="ba8fe-160">Soubor závislostí je vyžadován ke využívání hostování při spouštění v sestavení, ve kterém se spouští.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="ba8fe-161">Soubor závislostí je spustitelný App Asset, který je vytvořený publikováním aplikace, ne knihovny.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="ba8fe-162">Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="ba8fe-163">Při vytváření dynamického spuštění hostování:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="ba8fe-164">Hostující spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="ba8fe-165">Obsahuje třídu, která obsahuje `IHostingStartup` implementaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="ba8fe-166">Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pro identifikaci `IHostingStartup` implementační třídy.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="ba8fe-167">Konzolová aplikace se publikuje, aby získala závislosti hostování při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="ba8fe-168">V důsledku publikování konzolové aplikace je, že nevyužité závislosti se oříznou ze souboru závislostí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="ba8fe-169">Soubor závislostí je upraven, aby bylo možné nastavit umístění modulu runtime hostujícího spouštěcí sestavení.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="ba8fe-170">Hostování spouštěcího sestavení a jeho souboru závislostí je umístěno do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="ba8fe-171">Chcete-li zjistit hostující sestavení po spuštění a jeho soubor závislosti, jsou uvedeny ve dvojici proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="ba8fe-172">Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="ba8fe-173">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="ba8fe-174">V následujícím příkladu je obor názvů `StartupEnhancement` a třída `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ba8fe-175">Třída implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="ba8fe-176"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="ba8fe-177">`IHostingStartup.Configure`ve spouštěcím sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, což umožňuje přepsání libovolné konfigurace poskytované hostováním sestavení po spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="ba8fe-178">Při sestavování `IHostingStartup` projektu, soubor závislosti (*.deps.jszapnuto*) nastaví `runtime` umístění sestavení do složky *bin* :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="ba8fe-179">Zobrazí se pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-179">Only part of the file is shown.</span></span> <span data-ttu-id="ba8fe-180">Název sestavení v příkladu je `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="ba8fe-181">Konfigurace poskytovaná spuštěním hostování</span><span class="sxs-lookup"><span data-stu-id="ba8fe-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="ba8fe-182">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="ba8fe-183">Poskytněte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> , aby se konfigurace po spuštění delegátů aplikace načetla <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ba8fe-184">Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="ba8fe-185">Zadejte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> , aby se konfigurace načetla předtím, než se <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> spustí Delegáti aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ba8fe-186">Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="ba8fe-187">Zadejte hostující sestavení pro spuštění</span><span class="sxs-lookup"><span data-stu-id="ba8fe-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="ba8fe-188">V případě knihovny tříd nebo konzolové aplikace dodané po spuštění zadejte název hostujícího spouštěcího sestavení v `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="ba8fe-189">Proměnná prostředí je seznam sestavení oddělených středníky.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="ba8fe-190">Pro atribut jsou kontrolována pouze spouštěcí sestavení hostování `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ba8fe-191">Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="ba8fe-192">Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostování po spouštěcích sestaveních hostitele:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="ba8fe-193">Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="ba8fe-194">Aktivace</span><span class="sxs-lookup"><span data-stu-id="ba8fe-194">Activation</span></span>

<span data-ttu-id="ba8fe-195">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="ba8fe-196">[Běhové úložiště](#runtime-store): Aktivace nevyžaduje pro aktivaci odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="ba8fe-197">Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="ba8fe-198">Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="ba8fe-199">Pro aktivaci se vyžaduje odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="ba8fe-200">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="ba8fe-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="ba8fe-201">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="ba8fe-202">Běhové úložiště</span><span class="sxs-lookup"><span data-stu-id="ba8fe-202">Runtime store</span></span>

<span data-ttu-id="ba8fe-203">Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="ba8fe-204">Rozšířená aplikace nevyžaduje odkaz na sestavení v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="ba8fe-205">Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="ba8fe-206">V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="ba8fe-207">Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="ba8fe-208">**Upravit a umístit soubor závislostí se spouštěním hostitele**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="ba8fe-209">Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="ba8fe-210">`additionalDeps`umožňuje:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="ba8fe-211">Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších *.deps.js* souborů pro sloučení s vlastní *.deps.jsaplikace v* souboru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="ba8fe-212">Zpřístupněte hostující spouštěcí sestavení, které je zjistitelné a spustitelný.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="ba8fe-213">Doporučený postup pro generování dalších souborů závislostí:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="ba8fe-214">Spustit `dotnet publish` v souboru manifestu úložiště modulu runtime odkazovaného v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="ba8fe-215">Odeberte odkaz na manifest z knihoven a `runtime` část výsledného *.deps.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="ba8fe-216">V příkladu projektu `store.manifest/1.0.0` je vlastnost odebrána z `targets` `libraries` oddílu a:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="ba8fe-217">Umístit *.deps.js* do souboru do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="ba8fe-218">`{ADDITIONAL DEPENDENCIES PATH}`: Umístění bylo přidáno do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="ba8fe-219">`{SHARED FRAMEWORK NAME}`: Sdílené rozhraní požadované pro tento další soubor závislostí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="ba8fe-220">`{SHARED FRAMEWORK VERSION}`: Minimální verze sdíleného rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="ba8fe-221">`{ENHANCEMENT ASSEMBLY NAME}`: Název sestavení rozšíření.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="ba8fe-222">V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="ba8fe-223">Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí přidáno umístění souboru další závislosti `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="ba8fe-224">V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="ba8fe-225">Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ba8fe-226">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-226">**Deployment**</span></span>

<span data-ttu-id="ba8fe-227">Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="ba8fe-228">Hostování běhového úložiště při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="ba8fe-229">Hostující soubor závislostí po spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="ba8fe-230">Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` , `DOTNET_SHARED_STORE` a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="ba8fe-231">Spusťte skript z příkazového řádku PowerShellu pro správu v systému nasazení.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="ba8fe-232">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="ba8fe-232">NuGet package</span></span>

<span data-ttu-id="ba8fe-233">Rozšíření spouštění hostitele lze poskytnout v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="ba8fe-234">Balíček má `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="ba8fe-235">Typy spuštění hostování poskytované balíčkem jsou k dispozici v aplikaci pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="ba8fe-236">Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spouštěné v souboru projektu aplikace (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="ba8fe-237">Při použití reference v době kompilace, hostující sestavení po spuštění a všechny jeho závislosti jsou začleněny do souboru závislosti aplikace (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="ba8fe-238">Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="ba8fe-239">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="ba8fe-240">Další informace o balíčcích NuGet a úložišti modulu runtime najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="ba8fe-241">Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="ba8fe-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="ba8fe-242">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="ba8fe-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="ba8fe-243">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="ba8fe-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="ba8fe-244">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-244">Project bin folder</span></span>

<span data-ttu-id="ba8fe-245">Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="ba8fe-246">Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="ba8fe-247">Soubor projektu rozšířené aplikace vytvoří odkaz na sestavení pro spuštění hostování (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="ba8fe-248">Při použití reference v době kompilace, hostující sestavení po spuštění a všechny jeho závislosti jsou začleněny do souboru závislosti aplikace (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="ba8fe-249">Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="ba8fe-250">Projekt, který je k disnáročné.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-250">The consuming project.</span></span>
  * <span data-ttu-id="ba8fe-251">Umístění přístupné pro náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="ba8fe-252">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="ba8fe-253">Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="ba8fe-254">Základní cesta aplikace: složka *bin* , kde se nachází spustitelný soubor aplikace (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-254">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="ba8fe-255">Globální mezipaměť sestavení (GAC): GAC ukládá sestavení, která sdílí několik aplikací .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="ba8fe-256">Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="ba8fe-257">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-257">Sample code</span></span>

<span data-ttu-id="ba8fe-258">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="ba8fe-259">Dvě hostování spouštěcích sestavení (knihovny tříd) nastaví dvojici dvojic klíč-hodnota klíče pro konfiguraci v paměti každý:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="ba8fe-260">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="ba8fe-261">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="ba8fe-262">Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="ba8fe-263">Sestavení přidá do aplikace dvě middlewary při spuštění, které poskytují diagnostické informace o:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="ba8fe-264">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="ba8fe-264">Registered services</span></span>
  * <span data-ttu-id="ba8fe-265">Adresa (schéma, hostitel, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="ba8fe-266">Připojení (Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="ba8fe-267">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="ba8fe-267">Request headers</span></span>
  * <span data-ttu-id="ba8fe-268">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="ba8fe-268">Environment variables</span></span>

<span data-ttu-id="ba8fe-269">Spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-269">To run the sample:</span></span>

<span data-ttu-id="ba8fe-270">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="ba8fe-271">Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="ba8fe-272">Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ba8fe-273">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-273">Compile and run the app.</span></span> <span data-ttu-id="ba8fe-274">Odkaz na balíček se nachází v rozšířené aplikaci (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="ba8fe-275">`<PropertyGroup>`V souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="ba8fe-276">To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ba8fe-277">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou balíčku `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ba8fe-278">Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="ba8fe-279">K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="ba8fe-280">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="ba8fe-281">Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="ba8fe-282">Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ba8fe-283">*bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary.dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="ba8fe-284">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-284">Compile and run the app.</span></span> <span data-ttu-id="ba8fe-285">`<ItemGroup>`V souboru projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="ba8fe-286">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="ba8fe-287">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou knihovny tříd `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ba8fe-288">**Aktivace z běhového úložiště – nasazené sestavení**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="ba8fe-289">Projekt *StartupDiagnostics* pomocí [prostředí PowerShell](/powershell/scripting/powershell-scripting) upraví jeho *StartupDiagnostics.deps.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="ba8fe-290">PowerShell je ve výchozím nastavení nainstalován v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="ba8fe-291">Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace různých verzí PowerShellu](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="ba8fe-292">Spusťte skript *build.ps1* ve složce *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="ba8fe-293">Skript:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-293">The script:</span></span>
   * <span data-ttu-id="ba8fe-294">Vygeneruje `StartupDiagnostics` balíček ve složce *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="ba8fe-295">Vygeneruje běhové úložiště pro `StartupDiagnostics` ve složce *Store* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="ba8fe-296">`dotnet store`Příkaz ve skriptu používá `win7-x64` [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="ba8fe-297">Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="ba8fe-298">Běhové úložiště pro `StartupDiagnostics` by bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="ba8fe-299">Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="ba8fe-300">Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="ba8fe-301">Další závislosti by se později přesunuly na další závislosti uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="ba8fe-302">`StartupDiagnostics`Umístění instalace dalších závislostí uživatele je *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="ba8fe-303">Umístí soubor *deploy.ps1* do složky pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="ba8fe-304">Spusťte skript *deploy.ps1* ve složce pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="ba8fe-305">Skript připojí:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-305">The script appends:</span></span>
   * <span data-ttu-id="ba8fe-306">`StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="ba8fe-307">Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="ba8fe-308">Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="ba8fe-309">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-309">Run the sample app.</span></span>
1. <span data-ttu-id="ba8fe-310">Požádejte o `/services` koncový bod, aby se zobrazily registrované služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="ba8fe-311">Požádejte `/diag` koncový bod, aby zobrazil diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba8fe-312"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="ba8fe-313">Externí knihovna může například použít hostitelskou implementaci při spuštění k poskytnutí dalších poskytovatelů konfigurace nebo služeb do aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="ba8fe-314">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ba8fe-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="ba8fe-315">HostingStartup – atribut</span><span class="sxs-lookup"><span data-stu-id="ba8fe-315">HostingStartup attribute</span></span>

<span data-ttu-id="ba8fe-316">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="ba8fe-317">Pro zadání sestavení nebo sestavení obsahujícího `Startup` třídu se automaticky vyhledá `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ba8fe-318">Seznam sestavení pro vyhledávání `HostingStartup` atributů je načten za běhu z konfigurace ve [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="ba8fe-319">Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="ba8fe-320">Další informace najdete v tématu [webový hostitel: hostování spouštěcích sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [webového hostitele: hostování spouštěcích vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="ba8fe-321">V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="ba8fe-322">Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ba8fe-323">`HostingStartup`Atribut je obvykle umístěn v souboru implementační třídy sestavení, který spouští `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="ba8fe-324">Najít načtená hostování spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="ba8fe-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="ba8fe-325">Chcete-li zjistit načtená hostování spouštěcích sestavení, povolte protokolování a ověřte protokoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="ba8fe-326">Chyby, ke kterým dochází při načítání sestavení, jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="ba8fe-327">Načtená hostující spouštěcí sestavení jsou protokolována na úrovni ladění a všechny chyby jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="ba8fe-328">Zakázat automatické načítání hostujících spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="ba8fe-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="ba8fe-329">Chcete-li zakázat automatické načítání hostujících spouštěcích sestavení, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="ba8fe-330">Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1` :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="ba8fe-331">Zabrání hostování nastavení konfigurace hostitele [po spuštění](xref:fundamentals/host/web-host#prevent-hosting-startup) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="ba8fe-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="ba8fe-333">Chcete-li zabránit tomu, aby se konkrétní hostování spouštěcích sestavení načetlo, nastavte jednu z následujících hodnot na řetězec, který je hostitelem spouštěcích sestavení s oddělovači, aby při spuštění vyloučil:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="ba8fe-334">Hostování nastavení konfigurace hostitele [vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="ba8fe-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="ba8fe-336">Pokud je nastavené nastavení konfigurace hostitele i proměnná prostředí, řídí se chování nastavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="ba8fe-337">Zakázání hostování spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="ba8fe-338">Project</span><span class="sxs-lookup"><span data-stu-id="ba8fe-338">Project</span></span>

<span data-ttu-id="ba8fe-339">Vytvořte hostování po spuštění s některým z následujících typů projektů:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="ba8fe-340">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="ba8fe-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="ba8fe-341">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="ba8fe-342">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="ba8fe-342">Class library</span></span>

<span data-ttu-id="ba8fe-343">Rozšíření spouštění hostitele lze poskytnout v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="ba8fe-344">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ba8fe-345">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor stránku aplikace, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="ba8fe-346">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-346">The class library:</span></span>

* <span data-ttu-id="ba8fe-347">Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ba8fe-348">`ServiceKeyInjection`Přidá dvojici řetězců služby k konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="ba8fe-349">Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu pro spuštění hostingu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="ba8fe-350">`ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="ba8fe-351">*HostingStartupLibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ba8fe-352">Stránka indexu aplikace načte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostujícím sestavením pro spuštění knihovny tříd:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="ba8fe-353">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="ba8fe-354">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="ba8fe-355">Balíček má stejné charakteristiky jako knihovna tříd popsané výše.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="ba8fe-356">Balíček:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-356">The package:</span></span>

* <span data-ttu-id="ba8fe-357">Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ba8fe-358">`ServiceKeyInjection`Přidá do konfigurace aplikace dvojici řetězců služby.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="ba8fe-359">Obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ba8fe-360">*HostingStartupPackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ba8fe-361">Stránka indexu aplikace čte a vykresluje konfigurační hodnoty pro dva klíče nastavené hostujícím sestavením pro spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="ba8fe-362">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="ba8fe-363">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-363">Console app without an entry point</span></span>

<span data-ttu-id="ba8fe-364">*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="ba8fe-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="ba8fe-365">Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="ba8fe-366">Publikování konzolové aplikace vytváří hostující spouštěcí sestavení, které lze spotřebovat z běhového úložiště.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="ba8fe-367">V tomto procesu se používá Konzolová aplikace bez vstupního bodu, protože:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="ba8fe-368">Soubor závislostí je vyžadován ke využívání hostování při spouštění v sestavení, ve kterém se spouští.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="ba8fe-369">Soubor závislostí je spustitelný App Asset, který je vytvořený publikováním aplikace, ne knihovny.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="ba8fe-370">Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="ba8fe-371">Při vytváření dynamického spuštění hostování:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="ba8fe-372">Hostující spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="ba8fe-373">Obsahuje třídu, která obsahuje `IHostingStartup` implementaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="ba8fe-374">Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pro identifikaci `IHostingStartup` implementační třídy.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="ba8fe-375">Konzolová aplikace se publikuje, aby získala závislosti hostování při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="ba8fe-376">V důsledku publikování konzolové aplikace je, že nevyužité závislosti se oříznou ze souboru závislostí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="ba8fe-377">Soubor závislostí je upraven, aby bylo možné nastavit umístění modulu runtime hostujícího spouštěcí sestavení.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="ba8fe-378">Hostování spouštěcího sestavení a jeho souboru závislostí je umístěno do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="ba8fe-379">Chcete-li zjistit hostující sestavení po spuštění a jeho soubor závislosti, jsou uvedeny ve dvojici proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="ba8fe-380">Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="ba8fe-381">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="ba8fe-382">V následujícím příkladu je obor názvů `StartupEnhancement` a třída `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ba8fe-383">Třída implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="ba8fe-384"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="ba8fe-385">`IHostingStartup.Configure`ve spouštěcím sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, což umožňuje přepsání libovolné konfigurace poskytované hostováním sestavení po spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="ba8fe-386">Při sestavování `IHostingStartup` projektu, soubor závislosti (*.deps.jszapnuto*) nastaví `runtime` umístění sestavení do složky *bin* :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="ba8fe-387">Zobrazí se pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-387">Only part of the file is shown.</span></span> <span data-ttu-id="ba8fe-388">Název sestavení v příkladu je `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="ba8fe-389">Konfigurace poskytovaná spuštěním hostování</span><span class="sxs-lookup"><span data-stu-id="ba8fe-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="ba8fe-390">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="ba8fe-391">Poskytněte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> , aby se konfigurace po spuštění delegátů aplikace načetla <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ba8fe-392">Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="ba8fe-393">Zadejte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> , aby se konfigurace načetla předtím, než se <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> spustí Delegáti aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ba8fe-394">Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="ba8fe-395">Zadejte hostující sestavení pro spuštění</span><span class="sxs-lookup"><span data-stu-id="ba8fe-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="ba8fe-396">V případě knihovny tříd nebo konzolové aplikace dodané po spuštění zadejte název hostujícího spouštěcího sestavení v `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="ba8fe-397">Proměnná prostředí je seznam sestavení oddělených středníky.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="ba8fe-398">Pro atribut jsou kontrolována pouze spouštěcí sestavení hostování `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ba8fe-399">Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="ba8fe-400">Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostitele [po spouštěcích sestaveních](xref:fundamentals/host/web-host#hosting-startup-assemblies) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="ba8fe-401">Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="ba8fe-402">Aktivace</span><span class="sxs-lookup"><span data-stu-id="ba8fe-402">Activation</span></span>

<span data-ttu-id="ba8fe-403">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="ba8fe-404">[Běhové úložiště](#runtime-store): Aktivace nevyžaduje pro aktivaci odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="ba8fe-405">Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="ba8fe-406">Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="ba8fe-407">Pro aktivaci se vyžaduje odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="ba8fe-408">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="ba8fe-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="ba8fe-409">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="ba8fe-410">Běhové úložiště</span><span class="sxs-lookup"><span data-stu-id="ba8fe-410">Runtime store</span></span>

<span data-ttu-id="ba8fe-411">Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="ba8fe-412">Rozšířená aplikace nevyžaduje odkaz na sestavení v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="ba8fe-413">Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="ba8fe-414">V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="ba8fe-415">Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="ba8fe-416">**Upravit a umístit soubor závislostí se spouštěním hostitele**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="ba8fe-417">Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="ba8fe-418">`additionalDeps`umožňuje:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="ba8fe-419">Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších *.deps.js* souborů pro sloučení s vlastní *.deps.jsaplikace v* souboru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="ba8fe-420">Zpřístupněte hostující spouštěcí sestavení, které je zjistitelné a spustitelný.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="ba8fe-421">Doporučený postup pro generování dalších souborů závislostí:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="ba8fe-422">Spustit `dotnet publish` v souboru manifestu úložiště modulu runtime odkazovaného v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="ba8fe-423">Odeberte odkaz na manifest z knihoven a `runtime` část výsledného *.deps.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="ba8fe-424">V příkladu projektu `store.manifest/1.0.0` je vlastnost odebrána z `targets` `libraries` oddílu a:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="ba8fe-425">Umístit *.deps.js* do souboru do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="ba8fe-426">`{ADDITIONAL DEPENDENCIES PATH}`: Umístění bylo přidáno do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="ba8fe-427">`{SHARED FRAMEWORK NAME}`: Sdílené rozhraní požadované pro tento další soubor závislostí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="ba8fe-428">`{SHARED FRAMEWORK VERSION}`: Minimální verze sdíleného rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="ba8fe-429">`{ENHANCEMENT ASSEMBLY NAME}`: Název sestavení rozšíření.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="ba8fe-430">V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="ba8fe-431">Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí přidáno umístění souboru další závislosti `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="ba8fe-432">V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="ba8fe-433">Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ba8fe-434">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-434">**Deployment**</span></span>

<span data-ttu-id="ba8fe-435">Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="ba8fe-436">Hostování běhového úložiště při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="ba8fe-437">Hostující soubor závislostí po spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="ba8fe-438">Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` , `DOTNET_SHARED_STORE` a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="ba8fe-439">Spusťte skript z příkazového řádku PowerShellu pro správu v systému nasazení.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="ba8fe-440">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="ba8fe-440">NuGet package</span></span>

<span data-ttu-id="ba8fe-441">Rozšíření spouštění hostitele lze poskytnout v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="ba8fe-442">Balíček má `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="ba8fe-443">Typy spuštění hostování poskytované balíčkem jsou k dispozici v aplikaci pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="ba8fe-444">Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spouštěné v souboru projektu aplikace (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="ba8fe-445">Při použití reference v době kompilace, hostující sestavení po spuštění a všechny jeho závislosti jsou začleněny do souboru závislosti aplikace (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="ba8fe-446">Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="ba8fe-447">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="ba8fe-448">Další informace o balíčcích NuGet a úložišti modulu runtime najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="ba8fe-449">Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="ba8fe-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="ba8fe-450">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="ba8fe-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="ba8fe-451">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="ba8fe-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="ba8fe-452">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-452">Project bin folder</span></span>

<span data-ttu-id="ba8fe-453">Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="ba8fe-454">Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="ba8fe-455">Soubor projektu rozšířené aplikace vytvoří odkaz na sestavení pro spuštění hostování (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="ba8fe-456">Při použití reference v době kompilace, hostující sestavení po spuštění a všechny jeho závislosti jsou začleněny do souboru závislosti aplikace (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="ba8fe-457">Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="ba8fe-458">Projekt, který je k disnáročné.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-458">The consuming project.</span></span>
  * <span data-ttu-id="ba8fe-459">Umístění přístupné pro náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="ba8fe-460">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="ba8fe-461">Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="ba8fe-462">Základní cesta aplikace: složka *bin* , kde se nachází spustitelný soubor aplikace (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-462">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="ba8fe-463">Globální mezipaměť sestavení (GAC): GAC ukládá sestavení, která sdílí několik aplikací .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="ba8fe-464">Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="ba8fe-465">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="ba8fe-465">Sample code</span></span>

<span data-ttu-id="ba8fe-466">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="ba8fe-467">Dvě hostování spouštěcích sestavení (knihovny tříd) nastaví dvojici dvojic klíč-hodnota klíče pro konfiguraci v paměti každý:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="ba8fe-468">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="ba8fe-469">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="ba8fe-470">Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="ba8fe-471">Sestavení přidá do aplikace dvě middlewary při spuštění, které poskytují diagnostické informace o:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="ba8fe-472">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="ba8fe-472">Registered services</span></span>
  * <span data-ttu-id="ba8fe-473">Adresa (schéma, hostitel, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="ba8fe-474">Připojení (Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát)</span><span class="sxs-lookup"><span data-stu-id="ba8fe-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="ba8fe-475">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="ba8fe-475">Request headers</span></span>
  * <span data-ttu-id="ba8fe-476">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="ba8fe-476">Environment variables</span></span>

<span data-ttu-id="ba8fe-477">Spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-477">To run the sample:</span></span>

<span data-ttu-id="ba8fe-478">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="ba8fe-479">Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="ba8fe-480">Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ba8fe-481">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-481">Compile and run the app.</span></span> <span data-ttu-id="ba8fe-482">Odkaz na balíček se nachází v rozšířené aplikaci (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="ba8fe-483">`<PropertyGroup>`V souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="ba8fe-484">To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ba8fe-485">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou balíčku `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ba8fe-486">Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="ba8fe-487">K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :</span><span class="sxs-lookup"><span data-stu-id="ba8fe-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="ba8fe-488">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="ba8fe-489">Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="ba8fe-490">Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ba8fe-491">*bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary.dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="ba8fe-492">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-492">Compile and run the app.</span></span> <span data-ttu-id="ba8fe-493">`<ItemGroup>`V souboru projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="ba8fe-494">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="ba8fe-495">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou knihovny tříd `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ba8fe-496">**Aktivace z běhového úložiště – nasazené sestavení**</span><span class="sxs-lookup"><span data-stu-id="ba8fe-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="ba8fe-497">Projekt *StartupDiagnostics* pomocí [prostředí PowerShell](/powershell/scripting/powershell-scripting) upraví jeho *StartupDiagnostics.deps.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="ba8fe-498">PowerShell je ve výchozím nastavení nainstalován v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="ba8fe-499">Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace různých verzí PowerShellu](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="ba8fe-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="ba8fe-500">Spusťte skript *build.ps1* ve složce *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="ba8fe-501">Skript:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-501">The script:</span></span>
   * <span data-ttu-id="ba8fe-502">Vygeneruje `StartupDiagnostics` balíček ve složce *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="ba8fe-503">Vygeneruje běhové úložiště pro `StartupDiagnostics` ve složce *Store* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="ba8fe-504">`dotnet store`Příkaz ve skriptu používá `win7-x64` [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="ba8fe-505">Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="ba8fe-506">Běhové úložiště pro `StartupDiagnostics` by bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="ba8fe-507">Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="ba8fe-508">Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="ba8fe-509">Další závislosti by se později přesunuly na další závislosti uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="ba8fe-510">`StartupDiagnostics`Umístění instalace dalších závislostí uživatele je *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="ba8fe-511">Umístí soubor *deploy.ps1* do složky pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="ba8fe-512">Spusťte skript *deploy.ps1* ve složce pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="ba8fe-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="ba8fe-513">Skript připojí:</span><span class="sxs-lookup"><span data-stu-id="ba8fe-513">The script appends:</span></span>
   * <span data-ttu-id="ba8fe-514">`StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="ba8fe-515">Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="ba8fe-516">Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="ba8fe-517">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-517">Run the sample app.</span></span>
1. <span data-ttu-id="ba8fe-518">Požádejte o `/services` koncový bod, aby se zobrazily registrované služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="ba8fe-519">Požádejte `/diag` koncový bod, aby zobrazil diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="ba8fe-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
