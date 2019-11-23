---
title: Použití hostování při spuštění sestavení v ASP.NET Core
author: guardrex
description: Zjistěte, jak rozšířit aplikace ASP.NET Core z externího sestavení přes IHostingStartup implementace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c1ba742dda64296348898ec6a15ba725501dcb4f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391013"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="b5632-103">Použití hostování při spuštění sestavení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5632-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="b5632-104">Podle [Luke Latham](https://github.com/guardrex) a [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="b5632-104">By [Luke Latham](https://github.com/guardrex) and [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5632-105">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="b5632-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="b5632-106">Například externí knihovnu můžete hostování implementace spuštění uvést další konfigurace zprostředkovatele nebo služby do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="b5632-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5632-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="b5632-108">Atribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="b5632-108">HostingStartup attribute</span></span>

<span data-ttu-id="b5632-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atribut indikuje přítomnost hostování sestavení po spuštění k aktivaci za běhu.</span><span class="sxs-lookup"><span data-stu-id="b5632-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="b5632-110">Vstupní sestavení nebo sestavení obsahující `Startup` třídy automaticky vyhledávat `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b5632-111">Seznam sestavení, které chcete hledat `HostingStartup` atributy je načtená v době běhu z konfigurace v [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="b5632-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="b5632-112">Načíst seznam sestavení, které chcete vyloučit ze zjišťování z [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="b5632-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="b5632-113">V následujícím příkladu, obor názvů hostování sestavení po spuštění je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b5632-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="b5632-114">Třída obsahující kód hostování spuštění je `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b5632-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b5632-115">`HostingStartup` Atribut se obvykle nachází v hostitelském sestavení po spuštění `IHostingStartup` souboru implementace třídy.</span><span class="sxs-lookup"><span data-stu-id="b5632-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="b5632-116">Zjistit načtená hostingu při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="b5632-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="b5632-117">Ke zřízení načtená hostingu při spuštění sestavení, povolte protokolování a v protokolech aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="b5632-118">Jsou zaznamenány chyby, ke kterým dochází při načítání sestavení.</span><span class="sxs-lookup"><span data-stu-id="b5632-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="b5632-119">Načtená hostingu při spuštění sestavení jsou zaznamenány na úrovni ladění a jsou zaznamenány všechny chyby.</span><span class="sxs-lookup"><span data-stu-id="b5632-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="b5632-120">Vypnout automatické načítání hostování při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="b5632-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="b5632-121">Vypnout automatické načítání hostování při spuštění sestavení, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="b5632-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="b5632-122">Abyste zabránili všechna sestavení po spuštění hostování načítání, nastavte jednu z následujících způsobů `true` nebo `1`:</span><span class="sxs-lookup"><span data-stu-id="b5632-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="b5632-123">Zabránit hostování nastavení konfigurace hostitele po spuštění:</span><span class="sxs-lookup"><span data-stu-id="b5632-123">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="b5632-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="b5632-125">Pokud chcete zabránit konkrétní hostingu při spuštění sestavení z načítání, nastavte na řetězec oddělený středníkem hostování při spuštění sestavení mají vyloučit při spuštění jednu z následujících:</span><span class="sxs-lookup"><span data-stu-id="b5632-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="b5632-126">Hostování nastavení konfigurace vyloučení sestavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="b5632-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="b5632-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="b5632-128">Pokud nastavení konfigurace hostitele a proměnné prostředí jsou nastaveny, nastavení hostitele řídí chování.</span><span class="sxs-lookup"><span data-stu-id="b5632-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="b5632-129">Zakázání hostingu při spuštění sestavení pomocí proměnná hostitele nastavení nebo prostředí globálně zakáže sestavení a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="b5632-130">Project</span><span class="sxs-lookup"><span data-stu-id="b5632-130">Project</span></span>

<span data-ttu-id="b5632-131">Vytvoření hostitelského spouštěcího s jedním z následujících typů projektu:</span><span class="sxs-lookup"><span data-stu-id="b5632-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="b5632-132">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="b5632-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="b5632-133">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="b5632-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="b5632-134">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="b5632-134">Class library</span></span>

<span data-ttu-id="b5632-135">Hostování rozšíření spuštění lze zadat v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="b5632-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="b5632-136">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b5632-137">[Ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zahrnuje aplikace stránky Razor, *HostingStartupApp*a knihovny tříd, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="b5632-137">The [sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="b5632-138">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="b5632-138">The class library:</span></span>

* <span data-ttu-id="b5632-139">Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b5632-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b5632-140">`ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="b5632-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="b5632-141">Zahrnuje `HostingStartup` atribut, který identifikuje obor názvů a třídy spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b5632-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="b5632-142">Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> `ServiceKeyInjection` třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="b5632-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5632-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b5632-144">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče, nastavit podle knihovny tříd hostingu při spuštění sestavení:</span><span class="sxs-lookup"><span data-stu-id="b5632-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="b5632-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5632-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="b5632-146">[Ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) také zahrnuje projektu balíček NuGet, který poskytuje samostatné hostitelské spuštění *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="b5632-146">The [sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="b5632-147">Balíček má stejné charakteristiky knihovna tříd je popsáno výše.</span><span class="sxs-lookup"><span data-stu-id="b5632-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="b5632-148">Balíček:</span><span class="sxs-lookup"><span data-stu-id="b5632-148">The package:</span></span>

* <span data-ttu-id="b5632-149">Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b5632-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b5632-150">`ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="b5632-151">Zahrnuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b5632-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5632-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b5632-153">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče nastavil hostování sestavení po spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="b5632-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="b5632-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5632-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="b5632-155">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="b5632-155">Console app without an entry point</span></span>

<span data-ttu-id="b5632-156">*Tento přístup je dostupná jenom pro aplikace .NET Core, není rozhraní .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="b5632-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="b5632-157">Dynamické vylepšení spuštění hostování, který nevyžaduje odkaz kompilace pro aktivaci lze zadat v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="b5632-158">Publikování aplikace konzoly vytváří hostingu při spuštění sestavení, které mohou být spotřebovány z úložiště modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="b5632-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="b5632-159">Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:</span><span class="sxs-lookup"><span data-stu-id="b5632-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="b5632-160">Soubor závislostí je potřeba využívat hostování spouštění v hostitelském sestavení po spuštění.</span><span class="sxs-lookup"><span data-stu-id="b5632-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="b5632-161">Soubor závislostí je spustitelné aplikace majetek, který je vytvořen a publikovat aplikace, ne z knihovny.</span><span class="sxs-lookup"><span data-stu-id="b5632-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="b5632-162">Knihovny nelze přímo přidat [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje spustitelný projekt, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="b5632-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="b5632-163">Při vytváření dynamické hostování spuštění:</span><span class="sxs-lookup"><span data-stu-id="b5632-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="b5632-164">Hostování sestavení po spuštění je vytvořený z konzoly aplikace bez vstupní bod, který:</span><span class="sxs-lookup"><span data-stu-id="b5632-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="b5632-165">Obsahuje třídu, která obsahuje `IHostingStartup` implementace.</span><span class="sxs-lookup"><span data-stu-id="b5632-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="b5632-166">Zahrnuje [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atribut k identifikaci `IHostingStartup` implementace třídy.</span><span class="sxs-lookup"><span data-stu-id="b5632-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="b5632-167">Publikování aplikace konzoly získat závislosti spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b5632-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="b5632-168">V důsledku publikování aplikace konzoly je, že se ze souboru závislosti oříznut nepoužité závislé součásti.</span><span class="sxs-lookup"><span data-stu-id="b5632-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="b5632-169">Změně souboru závislosti modulu runtime pozici hostingu při spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="b5632-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="b5632-170">Hostování při spuštění sestavení a jeho závislostí souboru se umístí do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="b5632-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="b5632-171">Ke zjištění hostingu při spuštění sestavení a jeho závislostí souboru, jsou uvedeny v pár proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="b5632-172">Odkazy na aplikace konzoly [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) balíčku:</span><span class="sxs-lookup"><span data-stu-id="b5632-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="b5632-173">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="b5632-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="b5632-174">V následujícím příkladu je obor názvů `StartupEnhancement`, a je třída `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b5632-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b5632-175">Třída implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b5632-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="b5632-176">Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidávání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="b5632-177">`IHostingStartup.Configure` v hostitelských spuštění sestavení je volána modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat žádnou konfiguraci poskytované hostujícím sestavení při spuštění uživatelského kódu.</span><span class="sxs-lookup"><span data-stu-id="b5632-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="b5632-178">Při sestavování projektu `IHostingStartup`, soubor závislosti ( *. DEPS. JSON*) nastaví `runtime` umístění sestavení do složky *bin* :</span><span class="sxs-lookup"><span data-stu-id="b5632-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="b5632-179">Je zobrazena pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="b5632-179">Only part of the file is shown.</span></span> <span data-ttu-id="b5632-180">Název sestavení v příkladu je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b5632-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="b5632-181">Konfigurace poskytovaná spuštěním hostování</span><span class="sxs-lookup"><span data-stu-id="b5632-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="b5632-182">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="b5632-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="b5632-183">Poskytněte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pro načtení konfigurace po provedení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegátů aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b5632-184">Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="b5632-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="b5632-185">Zadejte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pro načtení konfigurace před spuštěním delegátů <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b5632-186">Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="b5632-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="b5632-187">Zadejte hostitelské sestavení po spuštění</span><span class="sxs-lookup"><span data-stu-id="b5632-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="b5632-188">Pro třídy knihovny - nebo konzoly aplikace poskytované hostitelem spuštění, zadejte název hostingu při spuštění sestavení `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="b5632-189">Proměnná prostředí je středníkem oddělený seznam sestavení.</span><span class="sxs-lookup"><span data-stu-id="b5632-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="b5632-190">Vyhledávají pouze na hostitelských při spuštění sestavení `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b5632-191">Ukázkové aplikace *HostingStartupApp*, ke zjištění hostování startupy je popsáno výše, je proměnná prostředí je nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="b5632-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="b5632-192">Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostování po spouštěcích sestaveních hostitele:</span><span class="sxs-lookup"><span data-stu-id="b5632-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="b5632-193">Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.</span><span class="sxs-lookup"><span data-stu-id="b5632-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="b5632-194">Aktivace</span><span class="sxs-lookup"><span data-stu-id="b5632-194">Activation</span></span>

<span data-ttu-id="b5632-195">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="b5632-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="b5632-196">[Úložiště modulu runtime](#runtime-store) &ndash; aktivace nevyžaduje, aby kompilace odkaz pro aktivaci.</span><span class="sxs-lookup"><span data-stu-id="b5632-196">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="b5632-197">Ukázková aplikace umístí hostování sestavení po spuštění a soubory závislosti do složky, *nasazení*, které usnadní nasazování hostitelských při spuštění v prostředí multimachine.</span><span class="sxs-lookup"><span data-stu-id="b5632-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="b5632-198">*Nasazení* složka obsahuje také skript Powershellu, který vytvoří nebo změní proměnné prostředí pro nasazení systému k povolení spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b5632-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="b5632-199">Vyžadováno pro aktivaci odkazu za kompilace</span><span class="sxs-lookup"><span data-stu-id="b5632-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="b5632-200">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="b5632-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="b5632-201">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="b5632-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="b5632-202">Úložiště modulu runtime</span><span class="sxs-lookup"><span data-stu-id="b5632-202">Runtime store</span></span>

<span data-ttu-id="b5632-203">Hostování implementace spuštění je umístěn v [úložiště modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="b5632-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="b5632-204">Kompilace odkaz na sestavení není vyžadováno vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="b5632-205">Po spuštění hostování sestavení, modulu runtime úložiště je generována pomocí souboru manifestu projektu a [dotnet Restore](/dotnet/core/tools/dotnet-store) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b5632-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="b5632-206">V ukázkové aplikaci (*RuntimeStore* project) pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="b5632-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="b5632-207">Pro modul runtime ke zjišťování úložiště modulu runtime se umístění úložiště modulu runtime přidá do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="b5632-208">**Upravit a umístěte soubor závislosti spuštění hostování**</span><span class="sxs-lookup"><span data-stu-id="b5632-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="b5632-209">K aktivaci vylepšení bez odkazu na balíček ke zvýšení, zadejte další závislosti modulu runtime s `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="b5632-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="b5632-210">`additionalDeps` umožňuje:</span><span class="sxs-lookup"><span data-stu-id="b5632-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="b5632-211">Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších souborů *. DEPS. JSON* pro sloučení s vlastním souborem *. DEPS. JSON* aplikace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b5632-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="b5632-212">Ujistěte se, hostování sestavení po spuštění zjistitelné a načíst.</span><span class="sxs-lookup"><span data-stu-id="b5632-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="b5632-213">Doporučený postup pro vygenerování Další závislosti souboru je:</span><span class="sxs-lookup"><span data-stu-id="b5632-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="b5632-214">Spustit `dotnet publish` v souboru manifestu úložiště runtime odkazované v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="b5632-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="b5632-215">Odeberte odkaz na manifest z knihoven a část `runtime` výsledného souboru *. DEPS. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b5632-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="b5632-216">Ve vzorovém projektu `store.manifest/1.0.0` vlastnost je odebrána z `targets` a `libraries` části:</span><span class="sxs-lookup"><span data-stu-id="b5632-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="b5632-217">Soubor *. DEPS. JSON* umístěte do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="b5632-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="b5632-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Přidat do umístění `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="b5632-219">`{SHARED FRAMEWORK NAME}` &ndash; Sdílené framework vyžaduje pro tento soubor Další závislosti.</span><span class="sxs-lookup"><span data-stu-id="b5632-219">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="b5632-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimální sdílené verze rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b5632-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="b5632-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; Název sestavení vylepšení.</span><span class="sxs-lookup"><span data-stu-id="b5632-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="b5632-222">V ukázkové aplikaci (*RuntimeStore* projektu), další závislosti soubor se umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="b5632-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="b5632-223">Pro modul runtime ke zjištění umístění úložiště modulu runtime, je do umístění souboru Další závislosti `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="b5632-224">V ukázkové aplikaci (*RuntimeStore* projektu), vytváření úložiště modulu runtime a další závislosti, soubor se provádí pomocí generování [Powershellu](/powershell/scripting/powershell-scripting) skriptu.</span><span class="sxs-lookup"><span data-stu-id="b5632-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="b5632-225">Příklady toho, jak nastavit proměnné prostředí pro různé operační systémy, najdete v článku [používání více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b5632-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b5632-226">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="b5632-226">**Deployment**</span></span>

<span data-ttu-id="b5632-227">Pro usnadnění nasazení hostování při spuštění v prostředí multimachine, vytvoří ukázkovou aplikaci *nasazení* složky v publikované výstup, který obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b5632-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="b5632-228">Hostování úložiště při spuštění modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="b5632-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="b5632-229">Hostování souboru závislostí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b5632-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="b5632-230">Skript prostředí PowerShell, který vytvoří nebo změní `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, a `DOTNET_ADDITIONAL_DEPS` kvůli podpoře aktivace spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b5632-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="b5632-231">Spusťte skript z příkazového řádku pro správu prostředí PowerShell v systému pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="b5632-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="b5632-232">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="b5632-232">NuGet package</span></span>

<span data-ttu-id="b5632-233">Hostování rozšíření spuštění lze zadat balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b5632-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="b5632-234">Balíček nemá `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="b5632-235">Hostování typům spouštění poskytovaný balíček jsou k dispozici do aplikace pomocí jedné z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="b5632-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="b5632-236">Soubor projektu vylepšené aplikace je odkaz na balíček pro hostování při spuštění v souboru projektu vaší aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="b5632-237">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="b5632-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="b5632-238">Vztahuje se na hostování balíček po spuštění sestavení publikovaná [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="b5632-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="b5632-239">Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="b5632-240">Další informace o balíčcích NuGet a úložiště modulu runtime naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b5632-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="b5632-241">Vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="b5632-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="b5632-242">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="b5632-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="b5632-243">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="b5632-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="b5632-244">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="b5632-244">Project bin folder</span></span>

<span data-ttu-id="b5632-245">Hostování rozšíření po spuštění můžete zadat *bin*– nasazení sestavení do vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="b5632-246">Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b5632-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="b5632-247">Soubor projektu vylepšené aplikace je odkaz na sestavení pro spouštění hostování (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="b5632-248">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="b5632-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="b5632-249">Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="b5632-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="b5632-250">Projekt, který je k disnáročné.</span><span class="sxs-lookup"><span data-stu-id="b5632-250">The consuming project.</span></span>
  * <span data-ttu-id="b5632-251">Umístění přístupné pro náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="b5632-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="b5632-252">Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="b5632-253">Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="b5632-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="b5632-254">Základní cesta aplikace &ndash; složku *bin* , kde se nachází spustitelný soubor aplikace ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="b5632-254">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="b5632-255">Globální mezipaměť sestavení (GAC) &ndash; GAC ukládá sestavení, která sdílí několik .NET Frameworkch aplikací.</span><span class="sxs-lookup"><span data-stu-id="b5632-255">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="b5632-256">Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b5632-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="b5632-257">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="b5632-257">Sample code</span></span>

<span data-ttu-id="b5632-258">[Ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:index#how-to-download-a-sample)) ukazuje implementaci scénáře hostingu po spuštění:</span><span class="sxs-lookup"><span data-stu-id="b5632-258">The [sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="b5632-259">Dvě hostování při spuštění sestavení (knihoven tříd) nastavit pár konfigurace v paměti páry klíč hodnota každé:</span><span class="sxs-lookup"><span data-stu-id="b5632-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="b5632-260">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="b5632-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="b5632-261">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="b5632-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="b5632-262">Hostování spuštění je aktivováno z úložiště nasazení sestavení modulu runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="b5632-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="b5632-263">Sestavení přidá do aplikace při spuštění obsahující diagnostické informace o dvou middlewares:</span><span class="sxs-lookup"><span data-stu-id="b5632-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="b5632-264">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="b5632-264">Registered services</span></span>
  * <span data-ttu-id="b5632-265">Adresa (schéma, hostitele, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="b5632-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="b5632-266">Připojení (vzdálenou IP adresu, vzdálený port, místní IP adresu, místní port, klientského certifikátu)</span><span class="sxs-lookup"><span data-stu-id="b5632-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="b5632-267">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="b5632-267">Request headers</span></span>
  * <span data-ttu-id="b5632-268">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5632-268">Environment variables</span></span>

<span data-ttu-id="b5632-269">Ke spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="b5632-269">To run the sample:</span></span>

<span data-ttu-id="b5632-270">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="b5632-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="b5632-271">Kompilace *HostingStartupPackage* balíček s [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b5632-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="b5632-272">Přidat název sestavení daného balíčku *HostingStartupPackage* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b5632-273">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-273">Compile and run the app.</span></span> <span data-ttu-id="b5632-274">Odkaz na balíček je k dispozici vylepšené aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="b5632-275">A `<PropertyGroup>` v projektu aplikace soubor Určuje výstup projektu balíček ( *... / HostingStartupPackage/bin/Debug*) jako zdroj balíčků.</span><span class="sxs-lookup"><span data-stu-id="b5632-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="b5632-276">To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b5632-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b5632-277">Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku shodovat s hodnotami nastavenými balíček `ServiceKeyInjection.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="b5632-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b5632-278">Pokud provedete změny *HostingStartupPackage* projektu a ji znovu zkompilovat, zrušte zaškrtnutí políčka místní mezipaměti balíčku NuGet a zkontrolujte, že *HostingStartupApp* obdrží aktualizovaný balíček a nejsou zastaralé balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b5632-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="b5632-279">Pokud chcete vymazat místní mezipaměť NuGet, spusťte následující [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) příkaz:</span><span class="sxs-lookup"><span data-stu-id="b5632-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="b5632-280">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="b5632-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="b5632-281">Kompilace *HostingStartupLibrary* knihovny tříd pomocí [dotnet sestavení](/dotnet/core/tools/dotnet-build) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b5632-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="b5632-282">Přidat název sestavení knihovny tříd *HostingStartupLibrary* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b5632-283">*bin*– sestavení knihovny tříd aplikace nasadíte tak, že zkopírujete *HostingStartupLibrary.dll* soubor z knihovny tříd zkompilován výstup do aplikace *bin/Debug* složky.</span><span class="sxs-lookup"><span data-stu-id="b5632-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="b5632-284">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-284">Compile and run the app.</span></span> <span data-ttu-id="b5632-285">`<ItemGroup>` v souboru projektu aplikace odkazuje na sestavení knihovny tříd ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="b5632-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="b5632-286">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b5632-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="b5632-287">Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku odpovídat hodnoty nastavené v knihovně tříd `ServiceKeyInjection.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="b5632-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b5632-288">**Aktivace z úložiště nasazení sestavení modulu runtime**</span><span class="sxs-lookup"><span data-stu-id="b5632-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="b5632-289">*StartupDiagnostics* používá projekt [PowerShell](/powershell/scripting/powershell-scripting) k úpravě jeho *StartupDiagnostics.deps.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="b5632-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="b5632-290">Prostředí PowerShell se instaluje standardně na Windows od verze Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="b5632-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="b5632-291">Získat PowerShell na jiných platformách, naleznete v tématu [instalace prostředí Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span><span class="sxs-lookup"><span data-stu-id="b5632-291">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="b5632-292">Spusťte skript *Build. ps1* ve složce *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="b5632-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="b5632-293">Skript:</span><span class="sxs-lookup"><span data-stu-id="b5632-293">The script:</span></span>
   * <span data-ttu-id="b5632-294">Vygeneruje balíček `StartupDiagnostics` ve složce *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="b5632-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="b5632-295">Generuje úložiště modulu runtime pro `StartupDiagnostics` ve složce *Store* .</span><span class="sxs-lookup"><span data-stu-id="b5632-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="b5632-296">Příkaz `dotnet store` ve skriptu používá [identifikátor modulu runtime `win7-x64` (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows.</span><span class="sxs-lookup"><span data-stu-id="b5632-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="b5632-297">Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="b5632-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="b5632-298">Běhové úložiště pro `StartupDiagnostics` bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno.</span><span class="sxs-lookup"><span data-stu-id="b5632-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="b5632-299">Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="b5632-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="b5632-300">Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="b5632-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="b5632-301">Další závislosti by se později přesunuly na další závislosti uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="b5632-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="b5632-302">Uživatel `StartupDiagnostics` další závislosti umístění instalace je *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/3.0.0/StartupDiagnostics. DEPS. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b5632-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="b5632-303">Umístí soubor *Deploy. ps1* do složky pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="b5632-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="b5632-304">Spusťte skript *Deploy. ps1* ve složce pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="b5632-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="b5632-305">Skript připojí:</span><span class="sxs-lookup"><span data-stu-id="b5632-305">The script appends:</span></span>
   * <span data-ttu-id="b5632-306">`StartupDiagnostics` do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b5632-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="b5632-307">Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="b5632-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="b5632-308">Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="b5632-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="b5632-309">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-309">Run the sample app.</span></span>
1. <span data-ttu-id="b5632-310">Žádosti `/services` koncový bod můžete zobrazit aplikace registrované služby.</span><span class="sxs-lookup"><span data-stu-id="b5632-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="b5632-311">Žádosti `/diag` koncový bod můžete zobrazit diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="b5632-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b5632-312">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="b5632-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="b5632-313">Například externí knihovnu můžete hostování implementace spuštění uvést další konfigurace zprostředkovatele nebo služby do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="b5632-314">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5632-314">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="b5632-315">Atribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="b5632-315">HostingStartup attribute</span></span>

<span data-ttu-id="b5632-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atribut indikuje přítomnost hostování sestavení po spuštění k aktivaci za běhu.</span><span class="sxs-lookup"><span data-stu-id="b5632-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="b5632-317">Vstupní sestavení nebo sestavení obsahující `Startup` třídy automaticky vyhledávat `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b5632-318">Seznam sestavení, které chcete hledat `HostingStartup` atributy je načtená v době běhu z konfigurace v [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="b5632-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="b5632-319">Načíst seznam sestavení, které chcete vyloučit ze zjišťování z [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="b5632-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="b5632-320">Další informace najdete v tématu [webového hostitele: hostování při spuštění sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [webového hostitele: sestavení vyloučit při spuštění hostování](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="b5632-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="b5632-321">V následujícím příkladu, obor názvů hostování sestavení po spuštění je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b5632-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="b5632-322">Třída obsahující kód hostování spuštění je `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b5632-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b5632-323">`HostingStartup` Atribut se obvykle nachází v hostitelském sestavení po spuštění `IHostingStartup` souboru implementace třídy.</span><span class="sxs-lookup"><span data-stu-id="b5632-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="b5632-324">Zjistit načtená hostingu při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="b5632-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="b5632-325">Ke zřízení načtená hostingu při spuštění sestavení, povolte protokolování a v protokolech aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="b5632-326">Jsou zaznamenány chyby, ke kterým dochází při načítání sestavení.</span><span class="sxs-lookup"><span data-stu-id="b5632-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="b5632-327">Načtená hostingu při spuštění sestavení jsou zaznamenány na úrovni ladění a jsou zaznamenány všechny chyby.</span><span class="sxs-lookup"><span data-stu-id="b5632-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="b5632-328">Vypnout automatické načítání hostování při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="b5632-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="b5632-329">Vypnout automatické načítání hostování při spuštění sestavení, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="b5632-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="b5632-330">Abyste zabránili všechna sestavení po spuštění hostování načítání, nastavte jednu z následujících způsobů `true` nebo `1`:</span><span class="sxs-lookup"><span data-stu-id="b5632-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="b5632-331">[Zabránit spuštění hostování](xref:fundamentals/host/web-host#prevent-hosting-startup) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5632-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="b5632-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="b5632-333">Pokud chcete zabránit konkrétní hostingu při spuštění sestavení z načítání, nastavte na řetězec oddělený středníkem hostování při spuštění sestavení mají vyloučit při spuštění jednu z následujících:</span><span class="sxs-lookup"><span data-stu-id="b5632-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="b5632-334">[Hostování sestavení vyloučit při spuštění](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5632-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="b5632-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="b5632-336">Pokud nastavení konfigurace hostitele a proměnné prostředí jsou nastaveny, nastavení hostitele řídí chování.</span><span class="sxs-lookup"><span data-stu-id="b5632-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="b5632-337">Zakázání hostingu při spuštění sestavení pomocí proměnná hostitele nastavení nebo prostředí globálně zakáže sestavení a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="b5632-338">Project</span><span class="sxs-lookup"><span data-stu-id="b5632-338">Project</span></span>

<span data-ttu-id="b5632-339">Vytvoření hostitelského spouštěcího s jedním z následujících typů projektu:</span><span class="sxs-lookup"><span data-stu-id="b5632-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="b5632-340">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="b5632-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="b5632-341">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="b5632-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="b5632-342">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="b5632-342">Class library</span></span>

<span data-ttu-id="b5632-343">Hostování rozšíření spuštění lze zadat v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="b5632-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="b5632-344">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b5632-345">[Ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zahrnuje aplikace stránky Razor, *HostingStartupApp*a knihovny tříd, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="b5632-345">The [sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="b5632-346">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="b5632-346">The class library:</span></span>

* <span data-ttu-id="b5632-347">Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b5632-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b5632-348">`ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="b5632-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="b5632-349">Zahrnuje `HostingStartup` atribut, který identifikuje obor názvů a třídy spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b5632-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="b5632-350">Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> `ServiceKeyInjection` třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="b5632-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5632-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b5632-352">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče, nastavit podle knihovny tříd hostingu při spuštění sestavení:</span><span class="sxs-lookup"><span data-stu-id="b5632-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="b5632-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5632-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="b5632-354">[Ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) také zahrnuje projektu balíček NuGet, který poskytuje samostatné hostitelské spuštění *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="b5632-354">The [sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="b5632-355">Balíček má stejné charakteristiky knihovna tříd je popsáno výše.</span><span class="sxs-lookup"><span data-stu-id="b5632-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="b5632-356">Balíček:</span><span class="sxs-lookup"><span data-stu-id="b5632-356">The package:</span></span>

* <span data-ttu-id="b5632-357">Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b5632-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b5632-358">`ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="b5632-359">Zahrnuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b5632-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5632-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b5632-361">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče nastavil hostování sestavení po spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="b5632-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="b5632-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5632-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="b5632-363">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="b5632-363">Console app without an entry point</span></span>

<span data-ttu-id="b5632-364">*Tento přístup je dostupná jenom pro aplikace .NET Core, není rozhraní .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="b5632-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="b5632-365">Dynamické vylepšení spuštění hostování, který nevyžaduje odkaz kompilace pro aktivaci lze zadat v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="b5632-366">Publikování aplikace konzoly vytváří hostingu při spuštění sestavení, které mohou být spotřebovány z úložiště modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="b5632-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="b5632-367">Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:</span><span class="sxs-lookup"><span data-stu-id="b5632-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="b5632-368">Soubor závislostí je potřeba využívat hostování spouštění v hostitelském sestavení po spuštění.</span><span class="sxs-lookup"><span data-stu-id="b5632-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="b5632-369">Soubor závislostí je spustitelné aplikace majetek, který je vytvořen a publikovat aplikace, ne z knihovny.</span><span class="sxs-lookup"><span data-stu-id="b5632-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="b5632-370">Knihovny nelze přímo přidat [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje spustitelný projekt, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="b5632-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="b5632-371">Při vytváření dynamické hostování spuštění:</span><span class="sxs-lookup"><span data-stu-id="b5632-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="b5632-372">Hostování sestavení po spuštění je vytvořený z konzoly aplikace bez vstupní bod, který:</span><span class="sxs-lookup"><span data-stu-id="b5632-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="b5632-373">Obsahuje třídu, která obsahuje `IHostingStartup` implementace.</span><span class="sxs-lookup"><span data-stu-id="b5632-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="b5632-374">Zahrnuje [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atribut k identifikaci `IHostingStartup` implementace třídy.</span><span class="sxs-lookup"><span data-stu-id="b5632-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="b5632-375">Publikování aplikace konzoly získat závislosti spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b5632-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="b5632-376">V důsledku publikování aplikace konzoly je, že se ze souboru závislosti oříznut nepoužité závislé součásti.</span><span class="sxs-lookup"><span data-stu-id="b5632-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="b5632-377">Změně souboru závislosti modulu runtime pozici hostingu při spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="b5632-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="b5632-378">Hostování při spuštění sestavení a jeho závislostí souboru se umístí do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="b5632-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="b5632-379">Ke zjištění hostingu při spuštění sestavení a jeho závislostí souboru, jsou uvedeny v pár proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="b5632-380">Odkazy na aplikace konzoly [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) balíčku:</span><span class="sxs-lookup"><span data-stu-id="b5632-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="b5632-381">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="b5632-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="b5632-382">V následujícím příkladu je obor názvů `StartupEnhancement`, a je třída `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b5632-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b5632-383">Třída implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b5632-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="b5632-384">Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidávání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="b5632-385">`IHostingStartup.Configure` v hostitelských spuštění sestavení je volána modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat žádnou konfiguraci poskytované hostujícím sestavení při spuštění uživatelského kódu.</span><span class="sxs-lookup"><span data-stu-id="b5632-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="b5632-386">Při sestavování projektu `IHostingStartup`, soubor závislosti ( *. DEPS. JSON*) nastaví `runtime` umístění sestavení do složky *bin* :</span><span class="sxs-lookup"><span data-stu-id="b5632-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="b5632-387">Je zobrazena pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="b5632-387">Only part of the file is shown.</span></span> <span data-ttu-id="b5632-388">Název sestavení v příkladu je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b5632-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="b5632-389">Konfigurace poskytovaná spuštěním hostování</span><span class="sxs-lookup"><span data-stu-id="b5632-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="b5632-390">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="b5632-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="b5632-391">Poskytněte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pro načtení konfigurace po provedení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegátů aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b5632-392">Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="b5632-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="b5632-393">Zadejte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pro načtení konfigurace před spuštěním delegátů <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b5632-394">Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="b5632-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="b5632-395">Zadejte hostitelské sestavení po spuštění</span><span class="sxs-lookup"><span data-stu-id="b5632-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="b5632-396">Pro třídy knihovny - nebo konzoly aplikace poskytované hostitelem spuštění, zadejte název hostingu při spuštění sestavení `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="b5632-397">Proměnná prostředí je středníkem oddělený seznam sestavení.</span><span class="sxs-lookup"><span data-stu-id="b5632-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="b5632-398">Vyhledávají pouze na hostitelských při spuštění sestavení `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b5632-399">Ukázkové aplikace *HostingStartupApp*, ke zjištění hostování startupy je popsáno výše, je proměnná prostředí je nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="b5632-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="b5632-400">Hostování sestavení po spuštění můžete nastavit také pomocí [hostování při spuštění sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5632-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="b5632-401">Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.</span><span class="sxs-lookup"><span data-stu-id="b5632-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="b5632-402">Aktivace</span><span class="sxs-lookup"><span data-stu-id="b5632-402">Activation</span></span>

<span data-ttu-id="b5632-403">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="b5632-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="b5632-404">[Úložiště modulu runtime](#runtime-store) &ndash; aktivace nevyžaduje, aby kompilace odkaz pro aktivaci.</span><span class="sxs-lookup"><span data-stu-id="b5632-404">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="b5632-405">Ukázková aplikace umístí hostování sestavení po spuštění a soubory závislosti do složky, *nasazení*, které usnadní nasazování hostitelských při spuštění v prostředí multimachine.</span><span class="sxs-lookup"><span data-stu-id="b5632-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="b5632-406">*Nasazení* složka obsahuje také skript Powershellu, který vytvoří nebo změní proměnné prostředí pro nasazení systému k povolení spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b5632-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="b5632-407">Vyžadováno pro aktivaci odkazu za kompilace</span><span class="sxs-lookup"><span data-stu-id="b5632-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="b5632-408">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="b5632-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="b5632-409">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="b5632-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="b5632-410">Úložiště modulu runtime</span><span class="sxs-lookup"><span data-stu-id="b5632-410">Runtime store</span></span>

<span data-ttu-id="b5632-411">Hostování implementace spuštění je umístěn v [úložiště modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="b5632-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="b5632-412">Kompilace odkaz na sestavení není vyžadováno vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="b5632-413">Po spuštění hostování sestavení, modulu runtime úložiště je generována pomocí souboru manifestu projektu a [dotnet Restore](/dotnet/core/tools/dotnet-store) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b5632-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="b5632-414">V ukázkové aplikaci (*RuntimeStore* project) pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="b5632-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="b5632-415">Pro modul runtime ke zjišťování úložiště modulu runtime se umístění úložiště modulu runtime přidá do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="b5632-416">**Upravit a umístěte soubor závislosti spuštění hostování**</span><span class="sxs-lookup"><span data-stu-id="b5632-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="b5632-417">K aktivaci vylepšení bez odkazu na balíček ke zvýšení, zadejte další závislosti modulu runtime s `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="b5632-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="b5632-418">`additionalDeps` umožňuje:</span><span class="sxs-lookup"><span data-stu-id="b5632-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="b5632-419">Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších souborů *. DEPS. JSON* pro sloučení s vlastním souborem *. DEPS. JSON* aplikace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b5632-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="b5632-420">Ujistěte se, hostování sestavení po spuštění zjistitelné a načíst.</span><span class="sxs-lookup"><span data-stu-id="b5632-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="b5632-421">Doporučený postup pro vygenerování Další závislosti souboru je:</span><span class="sxs-lookup"><span data-stu-id="b5632-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="b5632-422">Spustit `dotnet publish` v souboru manifestu úložiště runtime odkazované v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="b5632-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="b5632-423">Odeberte odkaz na manifest z knihoven a část `runtime` výsledného souboru *. DEPS. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b5632-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="b5632-424">Ve vzorovém projektu `store.manifest/1.0.0` vlastnost je odebrána z `targets` a `libraries` části:</span><span class="sxs-lookup"><span data-stu-id="b5632-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="b5632-425">Soubor *. DEPS. JSON* umístěte do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="b5632-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="b5632-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Přidat do umístění `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="b5632-427">`{SHARED FRAMEWORK NAME}` &ndash; Sdílené framework vyžaduje pro tento soubor Další závislosti.</span><span class="sxs-lookup"><span data-stu-id="b5632-427">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="b5632-428">`{SHARED FRAMEWORK VERSION}` &ndash; Minimální sdílené verze rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b5632-428">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="b5632-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; Název sestavení vylepšení.</span><span class="sxs-lookup"><span data-stu-id="b5632-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="b5632-430">V ukázkové aplikaci (*RuntimeStore* projektu), další závislosti soubor se umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="b5632-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="b5632-431">Pro modul runtime ke zjištění umístění úložiště modulu runtime, je do umístění souboru Další závislosti `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="b5632-432">V ukázkové aplikaci (*RuntimeStore* projektu), vytváření úložiště modulu runtime a další závislosti, soubor se provádí pomocí generování [Powershellu](/powershell/scripting/powershell-scripting) skriptu.</span><span class="sxs-lookup"><span data-stu-id="b5632-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="b5632-433">Příklady toho, jak nastavit proměnné prostředí pro různé operační systémy, najdete v článku [používání více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b5632-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b5632-434">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="b5632-434">**Deployment**</span></span>

<span data-ttu-id="b5632-435">Pro usnadnění nasazení hostování při spuštění v prostředí multimachine, vytvoří ukázkovou aplikaci *nasazení* složky v publikované výstup, který obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b5632-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="b5632-436">Hostování úložiště při spuštění modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="b5632-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="b5632-437">Hostování souboru závislostí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b5632-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="b5632-438">Skript prostředí PowerShell, který vytvoří nebo změní `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, a `DOTNET_ADDITIONAL_DEPS` kvůli podpoře aktivace spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b5632-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="b5632-439">Spusťte skript z příkazového řádku pro správu prostředí PowerShell v systému pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="b5632-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="b5632-440">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="b5632-440">NuGet package</span></span>

<span data-ttu-id="b5632-441">Hostování rozšíření spuštění lze zadat balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b5632-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="b5632-442">Balíček nemá `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b5632-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="b5632-443">Hostování typům spouštění poskytovaný balíček jsou k dispozici do aplikace pomocí jedné z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="b5632-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="b5632-444">Soubor projektu vylepšené aplikace je odkaz na balíček pro hostování při spuštění v souboru projektu vaší aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="b5632-445">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="b5632-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="b5632-446">Vztahuje se na hostování balíček po spuštění sestavení publikovaná [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="b5632-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="b5632-447">Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="b5632-448">Další informace o balíčcích NuGet a úložiště modulu runtime naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b5632-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="b5632-449">Vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="b5632-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="b5632-450">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="b5632-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="b5632-451">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="b5632-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="b5632-452">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="b5632-452">Project bin folder</span></span>

<span data-ttu-id="b5632-453">Hostování rozšíření po spuštění můžete zadat *bin*– nasazení sestavení do vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="b5632-454">Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b5632-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="b5632-455">Soubor projektu vylepšené aplikace je odkaz na sestavení pro spouštění hostování (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="b5632-456">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="b5632-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="b5632-457">Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="b5632-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="b5632-458">Projekt, který je k disnáročné.</span><span class="sxs-lookup"><span data-stu-id="b5632-458">The consuming project.</span></span>
  * <span data-ttu-id="b5632-459">Umístění přístupné pro náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="b5632-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="b5632-460">Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="b5632-461">Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="b5632-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="b5632-462">Základní cesta aplikace &ndash; složku *bin* , kde se nachází spustitelný soubor aplikace ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="b5632-462">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="b5632-463">Globální mezipaměť sestavení (GAC) &ndash; GAC ukládá sestavení, která sdílí několik .NET Frameworkch aplikací.</span><span class="sxs-lookup"><span data-stu-id="b5632-463">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="b5632-464">Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b5632-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="b5632-465">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="b5632-465">Sample code</span></span>

<span data-ttu-id="b5632-466">[Ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:index#how-to-download-a-sample)) ukazuje implementaci scénáře hostingu po spuštění:</span><span class="sxs-lookup"><span data-stu-id="b5632-466">The [sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="b5632-467">Dvě hostování při spuštění sestavení (knihoven tříd) nastavit pár konfigurace v paměti páry klíč hodnota každé:</span><span class="sxs-lookup"><span data-stu-id="b5632-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="b5632-468">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="b5632-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="b5632-469">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="b5632-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="b5632-470">Hostování spuštění je aktivováno z úložiště nasazení sestavení modulu runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="b5632-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="b5632-471">Sestavení přidá do aplikace při spuštění obsahující diagnostické informace o dvou middlewares:</span><span class="sxs-lookup"><span data-stu-id="b5632-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="b5632-472">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="b5632-472">Registered services</span></span>
  * <span data-ttu-id="b5632-473">Adresa (schéma, hostitele, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="b5632-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="b5632-474">Připojení (vzdálenou IP adresu, vzdálený port, místní IP adresu, místní port, klientského certifikátu)</span><span class="sxs-lookup"><span data-stu-id="b5632-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="b5632-475">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="b5632-475">Request headers</span></span>
  * <span data-ttu-id="b5632-476">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5632-476">Environment variables</span></span>

<span data-ttu-id="b5632-477">Ke spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="b5632-477">To run the sample:</span></span>

<span data-ttu-id="b5632-478">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="b5632-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="b5632-479">Kompilace *HostingStartupPackage* balíček s [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b5632-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="b5632-480">Přidat název sestavení daného balíčku *HostingStartupPackage* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b5632-481">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-481">Compile and run the app.</span></span> <span data-ttu-id="b5632-482">Odkaz na balíček je k dispozici vylepšené aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b5632-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="b5632-483">A `<PropertyGroup>` v projektu aplikace soubor Určuje výstup projektu balíček ( *... / HostingStartupPackage/bin/Debug*) jako zdroj balíčků.</span><span class="sxs-lookup"><span data-stu-id="b5632-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="b5632-484">To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b5632-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b5632-485">Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku shodovat s hodnotami nastavenými balíček `ServiceKeyInjection.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="b5632-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b5632-486">Pokud provedete změny *HostingStartupPackage* projektu a ji znovu zkompilovat, zrušte zaškrtnutí políčka místní mezipaměti balíčku NuGet a zkontrolujte, že *HostingStartupApp* obdrží aktualizovaný balíček a nejsou zastaralé balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b5632-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="b5632-487">Pokud chcete vymazat místní mezipaměť NuGet, spusťte následující [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) příkaz:</span><span class="sxs-lookup"><span data-stu-id="b5632-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="b5632-488">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="b5632-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="b5632-489">Kompilace *HostingStartupLibrary* knihovny tříd pomocí [dotnet sestavení](/dotnet/core/tools/dotnet-build) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b5632-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="b5632-490">Přidat název sestavení knihovny tříd *HostingStartupLibrary* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5632-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b5632-491">*bin*– sestavení knihovny tříd aplikace nasadíte tak, že zkopírujete *HostingStartupLibrary.dll* soubor z knihovny tříd zkompilován výstup do aplikace *bin/Debug* složky.</span><span class="sxs-lookup"><span data-stu-id="b5632-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="b5632-492">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-492">Compile and run the app.</span></span> <span data-ttu-id="b5632-493">`<ItemGroup>` Soubor v projektu aplikace odkazuje na sestavení knihovny tříd ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (referenční dokumentace kompilace).</span><span class="sxs-lookup"><span data-stu-id="b5632-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="b5632-494">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b5632-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="b5632-495">Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku odpovídat hodnoty nastavené v knihovně tříd `ServiceKeyInjection.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="b5632-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b5632-496">**Aktivace z úložiště nasazení sestavení modulu runtime**</span><span class="sxs-lookup"><span data-stu-id="b5632-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="b5632-497">*StartupDiagnostics* používá projekt [PowerShell](/powershell/scripting/powershell-scripting) k úpravě jeho *StartupDiagnostics.deps.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="b5632-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="b5632-498">Prostředí PowerShell se instaluje standardně na Windows od verze Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="b5632-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="b5632-499">Získat PowerShell na jiných platformách, naleznete v tématu [instalace prostředí Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span><span class="sxs-lookup"><span data-stu-id="b5632-499">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="b5632-500">Spusťte skript *Build. ps1* ve složce *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="b5632-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="b5632-501">Skript:</span><span class="sxs-lookup"><span data-stu-id="b5632-501">The script:</span></span>
   * <span data-ttu-id="b5632-502">Vygeneruje balíček `StartupDiagnostics` ve složce *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="b5632-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="b5632-503">Generuje úložiště modulu runtime pro `StartupDiagnostics` ve složce *Store* .</span><span class="sxs-lookup"><span data-stu-id="b5632-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="b5632-504">Příkaz `dotnet store` ve skriptu používá [identifikátor modulu runtime `win7-x64` (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows.</span><span class="sxs-lookup"><span data-stu-id="b5632-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="b5632-505">Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="b5632-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="b5632-506">Běhové úložiště pro `StartupDiagnostics` bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno.</span><span class="sxs-lookup"><span data-stu-id="b5632-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="b5632-507">Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="b5632-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="b5632-508">Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="b5632-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="b5632-509">Další závislosti by se později přesunuly na další závislosti uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="b5632-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="b5632-510">Uživatel `StartupDiagnostics` další závislosti umístění instalace je *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/2.2.0/StartupDiagnostics. DEPS. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b5632-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="b5632-511">Umístí soubor *Deploy. ps1* do složky pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="b5632-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="b5632-512">Spusťte skript *Deploy. ps1* ve složce pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="b5632-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="b5632-513">Skript připojí:</span><span class="sxs-lookup"><span data-stu-id="b5632-513">The script appends:</span></span>
   * <span data-ttu-id="b5632-514">`StartupDiagnostics` do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b5632-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="b5632-515">Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="b5632-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="b5632-516">Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="b5632-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="b5632-517">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5632-517">Run the sample app.</span></span>
1. <span data-ttu-id="b5632-518">Žádosti `/services` koncový bod můžete zobrazit aplikace registrované služby.</span><span class="sxs-lookup"><span data-stu-id="b5632-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="b5632-519">Žádosti `/diag` koncový bod můžete zobrazit diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="b5632-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
