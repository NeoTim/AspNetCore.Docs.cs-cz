---
title: Použití hostitelských spouštěcích sestavení v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vylepšit aplikaci ASP.NET Core z externího sestavení pomocí implementace IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: ac667b0205f5daad395d86fbe129beb509a044a6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417624"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="86f0b-103">Použití hostitelských spouštěcích sestavení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="86f0b-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="86f0b-104">Podle [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="86f0b-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86f0b-105">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostování při spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="86f0b-106">Externí knihovna může například použít implementaci hostitelského spuštění k poskytování dalších poskytovatelů konfigurace nebo služeb pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="86f0b-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="86f0b-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="86f0b-108">Atribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="86f0b-108">HostingStartup attribute</span></span>

<span data-ttu-id="86f0b-109">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského spouštěcího sestavení, které se aktivuje za běhu.</span><span class="sxs-lookup"><span data-stu-id="86f0b-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="86f0b-110">Sestavení položky nebo sestavení `Startup` obsahující třídu je automaticky `HostingStartup` zkontrolováno pro atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="86f0b-111">Seznam sestavení pro `HostingStartup` vyhledávání atributů je načten za běhu z konfigurace v [souboru WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="86f0b-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="86f0b-112">Seznam sestavení, která mají být vyloučena z zjišťování, je načten z [webu WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="86f0b-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="86f0b-113">V následujícím příkladu je `StartupEnhancement`obor názvů hostitelského spouštěcího sestavení .</span><span class="sxs-lookup"><span data-stu-id="86f0b-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="86f0b-114">Třída obsahující kód hostitelského `StartupEnhancementHostingStartup`spuštění je :</span><span class="sxs-lookup"><span data-stu-id="86f0b-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="86f0b-115">Atribut `HostingStartup` je obvykle umístěn v hostitelském `IHostingStartup` spouštěcím sestavení implementační ho souboru třídy.</span><span class="sxs-lookup"><span data-stu-id="86f0b-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="86f0b-116">Objevte načtená hostitelská spouštěcí sestavení</span><span class="sxs-lookup"><span data-stu-id="86f0b-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="86f0b-117">Chcete-li zjistit načtená sestavení po spuštění hostingu, povolte protokolování a zkontrolujte protokoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="86f0b-118">Chyby, ke kterým dochází při načítání sestavení jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="86f0b-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="86f0b-119">Načtená hostitelská spouštěcí sestavení jsou protokolována na úrovni ladění a jsou protokolovány všechny chyby.</span><span class="sxs-lookup"><span data-stu-id="86f0b-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="86f0b-120">Zakázat automatické načítání hostitelských spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="86f0b-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="86f0b-121">Chcete-li zakázat automatické načítání hostitelských spouštěcích sestavení, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="86f0b-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="86f0b-122">Chcete-li zabránit načítání všech hostitelských spouštěcích `true` sestavení, nastavte jednu z následujících možností na nebo `1`:</span><span class="sxs-lookup"><span data-stu-id="86f0b-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="86f0b-123">Zabránit nastavení konfigurace hostitele po spuštění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-123">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="86f0b-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP`proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="86f0b-125">Chcete-li zabránit načítání konkrétních hostitelských spouštěcích sestavení, nastavte jednu z následujících na řetězec hostitelských spouštěcích sestavení oddělených středníkem, který chcete při spuštění vyloučit:</span><span class="sxs-lookup"><span data-stu-id="86f0b-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="86f0b-126">Hostování nastavení konfigurace hostitele sestavení spouštění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="86f0b-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="86f0b-128">Pokud je nastaveno nastavení konfigurace hostitele i proměnná prostředí, řídí chování nastavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="86f0b-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="86f0b-129">Zakázání hostitelských spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik charakteristik aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="86f0b-130">Project</span><span class="sxs-lookup"><span data-stu-id="86f0b-130">Project</span></span>

<span data-ttu-id="86f0b-131">Vytvořte hostitelské spuštění s jedním z následujících typů projektů:</span><span class="sxs-lookup"><span data-stu-id="86f0b-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="86f0b-132">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="86f0b-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="86f0b-133">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="86f0b-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="86f0b-134">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="86f0b-134">Class library</span></span>

<span data-ttu-id="86f0b-135">Vylepšení spuštění hostingu lze poskytnout v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="86f0b-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="86f0b-136">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="86f0b-137">[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje aplikaci Razor Pages, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="86f0b-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="86f0b-138">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="86f0b-138">The class library:</span></span>

* <span data-ttu-id="86f0b-139">Obsahuje třídu spuštění `ServiceKeyInjection`hostitele , `IHostingStartup`která implementuje .</span><span class="sxs-lookup"><span data-stu-id="86f0b-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="86f0b-140">`ServiceKeyInjection`přidá dvojici řetězců služby do konfigurace aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="86f0b-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="86f0b-141">Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="86f0b-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="86f0b-142">Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> doplněk k přidání vylepšení do aplikace. `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*></span><span class="sxs-lookup"><span data-stu-id="86f0b-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="86f0b-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="86f0b-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="86f0b-144">Stránka Index aplikace čte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostitelským spouštěcím sestavením knihovny tříd:</span><span class="sxs-lookup"><span data-stu-id="86f0b-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="86f0b-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="86f0b-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="86f0b-146">[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) také obsahuje projekt balíčku NuGet, který poskytuje samostatné hostování *spuštění, HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="86f0b-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="86f0b-147">Balíček má stejné vlastnosti knihovny tříd popsaných výše.</span><span class="sxs-lookup"><span data-stu-id="86f0b-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="86f0b-148">Balíček:</span><span class="sxs-lookup"><span data-stu-id="86f0b-148">The package:</span></span>

* <span data-ttu-id="86f0b-149">Obsahuje třídu spuštění `ServiceKeyInjection`hostitele , `IHostingStartup`která implementuje .</span><span class="sxs-lookup"><span data-stu-id="86f0b-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="86f0b-150">`ServiceKeyInjection`přidá do konfigurace aplikace dvojici řetězců služeb.</span><span class="sxs-lookup"><span data-stu-id="86f0b-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="86f0b-151">Zahrnuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="86f0b-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="86f0b-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="86f0b-153">Stránka Index aplikace čte a vykreslí hodnoty konfigurace pro dva klíče nastavené sestavením hostitelského spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="86f0b-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="86f0b-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="86f0b-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="86f0b-155">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="86f0b-155">Console app without an entry point</span></span>

<span data-ttu-id="86f0b-156">*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli rozhraní .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="86f0b-157">Dynamické vylepšení spuštění hostingu, které nevyžaduje odkaz na aktivaci v době kompilace, může být `HostingStartup` poskytnuto v konzolové aplikaci bez vstupního bodu, který obsahuje atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="86f0b-158">Publikování konzolové aplikace vytvoří hostitelské spouštěcí sestavení, které lze spotřebovat z runtime úložiště.</span><span class="sxs-lookup"><span data-stu-id="86f0b-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="86f0b-159">Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:</span><span class="sxs-lookup"><span data-stu-id="86f0b-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="86f0b-160">Soubor závislostí je nutné využívat hostování spuštění v hostitelském spouštěcím sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="86f0b-161">Soubor závislostí je spustitelný prostředek aplikace, který se vytváří publikováním aplikace, nikoli knihovny.</span><span class="sxs-lookup"><span data-stu-id="86f0b-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="86f0b-162">Knihovnu nelze přidat přímo do [úložiště balíčků runtime](/dotnet/core/deploying/runtime-store), což vyžaduje spustitelný projekt, který cílí na sdílený runtime.</span><span class="sxs-lookup"><span data-stu-id="86f0b-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="86f0b-163">Při vytváření dynamického hostování spuštění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="86f0b-164">Hostitelské spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:</span><span class="sxs-lookup"><span data-stu-id="86f0b-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="86f0b-165">Zahrnuje třídu, `IHostingStartup` která obsahuje implementaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="86f0b-166">Zahrnuje [Atribut HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) k `IHostingStartup` identifikaci třídy implementace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="86f0b-167">Konzolová aplikace je publikována, aby získala závislosti spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="86f0b-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="86f0b-168">Důsledkem publikování konzolové aplikace je, že nepoužívané závislosti jsou oříznuty ze souboru závislostí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="86f0b-169">Soubor závislostí je upraven tak, aby nastavil umístění runtime hostitelského spouštěcího sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="86f0b-170">Hostitelské spouštěcí sestavení a jeho soubor závislostí je umístěn do úložiště balíčků runtime.</span><span class="sxs-lookup"><span data-stu-id="86f0b-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="86f0b-171">Chcete-li zjistit hostitelské spouštěcí sestavení a jeho soubor závislostí, jsou uvedeny v dvojici proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="86f0b-172">Konzolová aplikace odkazuje na balíček [Microsoft.AspNetCore.Hosting.Abstractions:](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/)</span><span class="sxs-lookup"><span data-stu-id="86f0b-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="86f0b-173">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako `IHostingStartup` implementaci pro načítání a <xref:Microsoft.AspNetCore.Hosting.IWebHost>provádění při vytváření .</span><span class="sxs-lookup"><span data-stu-id="86f0b-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="86f0b-174">V následujícím příkladu je `StartupEnhancement`obor názvů a `StartupEnhancementHostingStartup`třída je :</span><span class="sxs-lookup"><span data-stu-id="86f0b-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="86f0b-175">Třída implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="86f0b-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="86f0b-176"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> doplněk k přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="86f0b-177">`IHostingStartup.Configure`v hostiteli spouštěcí sestavení je volána runtime před `Startup.Configure` v uživatelském kódu, který umožňuje uživatelský kód přepsat libovolnou konfiguraci poskytovanou sestavení mnoství po spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="86f0b-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="86f0b-178">Při vytváření `IHostingStartup` projektu nastaví soubor závislostí (*.deps.json*) `runtime` umístění sestavení do složky *přihrádky:*</span><span class="sxs-lookup"><span data-stu-id="86f0b-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="86f0b-179">Zobrazí se pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="86f0b-179">Only part of the file is shown.</span></span> <span data-ttu-id="86f0b-180">Název sestavení v příkladu je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="86f0b-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="86f0b-181">Konfigurace poskytovaná spuštěním hostingu</span><span class="sxs-lookup"><span data-stu-id="86f0b-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="86f0b-182">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, zda chcete, aby konfigurace hostitelského spuštění měla přednost nebo aby měla přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="86f0b-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="86f0b-183">Poskytněte konfiguraci aplikaci pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> načtení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> konfigurace po spuštění delegátů aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="86f0b-184">Hostování konfigurace spuštění má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="86f0b-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="86f0b-185">Poskytněte konfiguraci aplikaci pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> načtení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> konfigurace před spuštěním delegátů aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="86f0b-186">Hodnoty konfigurace aplikace mají přednost před hodnotami poskytovanými spuštěním hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="86f0b-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="86f0b-187">Určení hostitelského spouštěcího sestavení</span><span class="sxs-lookup"><span data-stu-id="86f0b-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="86f0b-188">Pro spuštění hostování dodané knihovnou nebo konzolovou aplikací zadejte název `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hostitelského spouštěcího sestavení v proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="86f0b-189">Proměnná prostředí je seznam sestav oddělených středníkem.</span><span class="sxs-lookup"><span data-stu-id="86f0b-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="86f0b-190">Pro tento `HostingStartup` atribut jsou prohledány pouze hostitelské spouštěcí sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="86f0b-191">Pro ukázkovou aplikaci *HostingStartupApp*, chcete-li zjistit hostitelské startupy popsané výše, proměnná prostředí je nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="86f0b-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="86f0b-192">Hostitelské spouštěcí sestavení lze také nastavit pomocí nastavení konfigurace hostitele hostitelských sestavení hostování:</span><span class="sxs-lookup"><span data-stu-id="86f0b-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="86f0b-193">Pokud je k dispozici více <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> hostitelských sestavení po spuštění, jejich metody jsou provedeny v pořadí, ve které jsou uvedena sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="86f0b-194">Aktivace</span><span class="sxs-lookup"><span data-stu-id="86f0b-194">Activation</span></span>

<span data-ttu-id="86f0b-195">Možnosti pro aktivaci pospuštění hostingu jsou:</span><span class="sxs-lookup"><span data-stu-id="86f0b-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="86f0b-196">[Aktivace runtime úložiště](#runtime-store) &ndash; nevyžaduje odkaz na dobu kompilace pro aktivaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-196">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="86f0b-197">Ukázková aplikace umístí hostitelské spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spuštění v prostředí s více počítači.</span><span class="sxs-lookup"><span data-stu-id="86f0b-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="86f0b-198">Složka *nasazení* také obsahuje skript prostředí PowerShell, který vytváří nebo upravuje proměnné prostředí v systému nasazení, aby bylo možné povolit hostování při spuštění.</span><span class="sxs-lookup"><span data-stu-id="86f0b-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="86f0b-199">Odkaz na dobu kompilace potřebný pro aktivaci</span><span class="sxs-lookup"><span data-stu-id="86f0b-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="86f0b-200">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="86f0b-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="86f0b-201">Složka přihrádky projektu</span><span class="sxs-lookup"><span data-stu-id="86f0b-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="86f0b-202">Runtime úložiště</span><span class="sxs-lookup"><span data-stu-id="86f0b-202">Runtime store</span></span>

<span data-ttu-id="86f0b-203">Implementace spuštění hostitele je umístěna v [runtime úložišti](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="86f0b-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="86f0b-204">Odkaz na sestavení v době kompilace není vyžadován vylepšenou aplikací.</span><span class="sxs-lookup"><span data-stu-id="86f0b-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="86f0b-205">Po vytvoření hostitelského spuštění je generováno runtime úložiště pomocí souboru manifestu projektu a příkazu [dotnet store.](/dotnet/core/tools/dotnet-store)</span><span class="sxs-lookup"><span data-stu-id="86f0b-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="86f0b-206">V ukázkové aplikaci *(RuntimeStore* project) se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="86f0b-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="86f0b-207">Pro runtime ke zjištění runtime úložiště, umístění runtime úložiště `DOTNET_SHARED_STORE` je přidán do proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="86f0b-208">**Úprava a umístění souboru závislostí hostitelského spuštění**</span><span class="sxs-lookup"><span data-stu-id="86f0b-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="86f0b-209">Chcete-li aktivovat vylepšení bez odkazu na balíček vylepšení, `additionalDeps`zadejte další závislosti na běhu s .</span><span class="sxs-lookup"><span data-stu-id="86f0b-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="86f0b-210">`additionalDeps`umožňuje:</span><span class="sxs-lookup"><span data-stu-id="86f0b-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="86f0b-211">Rozšiřte graf knihovny aplikace poskytnutím sady dalších souborů *.deps.json,* které se při spuštění sloučí s vlastním souborem *.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="86f0b-212">Nastavení hostitelského spouštěcího sestavení zjistitelné a načítatelné.</span><span class="sxs-lookup"><span data-stu-id="86f0b-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="86f0b-213">Doporučený mdla pro generování dalšího souboru závislostí je:</span><span class="sxs-lookup"><span data-stu-id="86f0b-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="86f0b-214">Spusťte `dotnet publish` v souboru manifestu runtime store, na který odkazuje předchozí část.</span><span class="sxs-lookup"><span data-stu-id="86f0b-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="86f0b-215">Odeberte odkaz na manifest `runtime` z knihoven a části výsledného souboru *.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="86f0b-216">V ukázkovém `store.manifest/1.0.0` projektu je vlastnost `targets` `libraries` odebrána z oddílu a:</span><span class="sxs-lookup"><span data-stu-id="86f0b-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="86f0b-217">Umístěte soubor *.deps.json* do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="86f0b-218">`{ADDITIONAL DEPENDENCIES PATH}`&ndash; Umístění přidané `DOTNET_ADDITIONAL_DEPS` do proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="86f0b-219">`{SHARED FRAMEWORK NAME}`&ndash; Pro tento další soubor závislostí je vyžadovánsdílený rámec.</span><span class="sxs-lookup"><span data-stu-id="86f0b-219">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="86f0b-220">`{SHARED FRAMEWORK VERSION}`&ndash; Minimální verze sdílené ho rámce.</span><span class="sxs-lookup"><span data-stu-id="86f0b-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="86f0b-221">`{ENHANCEMENT ASSEMBLY NAME}`&ndash; Název sestavení vylepšení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="86f0b-222">V ukázkové aplikaci (projekt*RuntimeStore)* se další soubor závislostí umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="86f0b-223">Pro runtime ke zjištění umístění úložiště runtime je do proměnné `DOTNET_ADDITIONAL_DEPS` prostředí přidáno další umístění souboru závislostí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="86f0b-224">V ukázkové aplikaci *(runtimestore* projektu), vytváření runtime úložiště a generování další soubor závislostí se provádí pomocí skriptu [Prostředí PowerShell.](/powershell/scripting/powershell-scripting)</span><span class="sxs-lookup"><span data-stu-id="86f0b-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="86f0b-225">Příklady nastavení proměnných prostředí pro různé operační systémy naleznete v tématu [Použití více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="86f0b-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="86f0b-226">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="86f0b-226">**Deployment**</span></span>

<span data-ttu-id="86f0b-227">Pro usnadnění nasazení hostitelského spuštění v prostředí s více počítači vytvoří ukázková aplikace složku *nasazení* v publikovaném výstupu, která obsahuje:</span><span class="sxs-lookup"><span data-stu-id="86f0b-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="86f0b-228">Hostitelské spouštěcí runtime úložiště.</span><span class="sxs-lookup"><span data-stu-id="86f0b-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="86f0b-229">Hostitelský soubor závislostí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="86f0b-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="86f0b-230">Skript prostředí PowerShell, který vytváří `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`nebo `DOTNET_SHARED_STORE`upravuje `DOTNET_ADDITIONAL_DEPS` , a podporuje aktivaci hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="86f0b-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="86f0b-231">Spusťte skript z příkazového řádku prostředí PowerShell pro správu v systému nasazení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="86f0b-232">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="86f0b-232">NuGet package</span></span>

<span data-ttu-id="86f0b-233">Vylepšení spuštění hostingu může být k dispozici v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="86f0b-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="86f0b-234">Balíček `HostingStartup` má atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="86f0b-235">Typy spuštění hostování poskytované balíčkem jsou k dispozici aplikaci pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="86f0b-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="86f0b-236">Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spuštění v souboru projektu aplikace (odkaz na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="86f0b-237">S odkazem na dobu kompilace na místě, hostování spouštěcí sestavení a všechny jeho závislosti jsou začleněny do souboru závislostí aplikace (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="86f0b-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="86f0b-238">Tento přístup se vztahuje na balíček sestavení hostitelského spuštění publikovaného pro [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="86f0b-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="86f0b-239">Soubor závislostí hostitelského spuštění je k dispozici rozšířené aplikaci, jak je popsáno v části [Runtime store](#runtime-store) (bez odkazu na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="86f0b-240">Další informace o balíčcích NuGet a runtime úložišti naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="86f0b-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="86f0b-241">Jak vytvořit balíček NuGet s nástroji křížové platformy</span><span class="sxs-lookup"><span data-stu-id="86f0b-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="86f0b-242">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="86f0b-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="86f0b-243">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="86f0b-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="86f0b-244">Složka přihrádky projektu</span><span class="sxs-lookup"><span data-stu-id="86f0b-244">Project bin folder</span></span>

<span data-ttu-id="86f0b-245">Vylepšení spouštění hostingu může být poskytnuto sestavením nasazeným *přihrádkou*v rozšířené aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="86f0b-246">Typy spuštění hostování poskytované sestavením jsou k dispozici aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="86f0b-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="86f0b-247">Soubor projektu rozšířené aplikace odkazuje na hostování spuštění (odkaz na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="86f0b-248">S odkazem na dobu kompilace na místě, hostování spouštěcí sestavení a všechny jeho závislosti jsou začleněny do souboru závislostí aplikace (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="86f0b-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="86f0b-249">Tento přístup platí, když scénář nasazení volá pro vytvoření odkazu na sestavení hostitelského spuštění *(soubor dll)* a přesunutí sestavení buď:</span><span class="sxs-lookup"><span data-stu-id="86f0b-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="86f0b-250">Náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="86f0b-250">The consuming project.</span></span>
  * <span data-ttu-id="86f0b-251">Umístění přístupné náročným projektem.</span><span class="sxs-lookup"><span data-stu-id="86f0b-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="86f0b-252">Soubor závislostí hostitelského spuštění je k dispozici rozšířené aplikaci, jak je popsáno v části [Runtime store](#runtime-store) (bez odkazu na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="86f0b-253">Při cílení na rozhraní .NET Framework je sestavení načítatelné ve výchozím kontextu zatížení, což v rozhraní .NET Framework znamená, že sestavení je umístěno v některém z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="86f0b-254">Cesta &ndash; k základně aplikace Složka *přihrádky,* ve které je umístěn spustitelný soubor aplikace (*EXE*).</span><span class="sxs-lookup"><span data-stu-id="86f0b-254">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="86f0b-255">Globální mezipaměti sestavení &ndash; (GAC) GAC ukládá sestavení, které sdílejí několik aplikací rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="86f0b-255">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="86f0b-256">Další informace naleznete v [tématu Postup: Instalace sestavení do globální mezipaměti sestavení](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) v dokumentaci rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="86f0b-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="86f0b-257">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="86f0b-257">Sample code</span></span>

<span data-ttu-id="86f0b-258">[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak stáhnout](xref:index#how-to-download-a-sample)) ukazuje hostování scénářů implementace spuštění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="86f0b-259">Dvě hostitelská spouštěcí sestavení (knihovny tříd) nastavují dvojici párů klíč-hodnota konfigurace v paměti:</span><span class="sxs-lookup"><span data-stu-id="86f0b-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="86f0b-260">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="86f0b-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="86f0b-261">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="86f0b-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="86f0b-262">Hostitelské spuštění je aktivováno z runtime store nasazeného sestavení (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="86f0b-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="86f0b-263">Sestavení přidá do aplikace při spuštění dva middlewares, které poskytují diagnostické informace o:</span><span class="sxs-lookup"><span data-stu-id="86f0b-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="86f0b-264">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="86f0b-264">Registered services</span></span>
  * <span data-ttu-id="86f0b-265">Adresa (schéma, hostitel, základna cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="86f0b-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="86f0b-266">Připojení (vzdálená IP adresa, vzdálený port, lokální IP, místní port, klientský certifikát)</span><span class="sxs-lookup"><span data-stu-id="86f0b-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="86f0b-267">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="86f0b-267">Request headers</span></span>
  * <span data-ttu-id="86f0b-268">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="86f0b-268">Environment variables</span></span>

<span data-ttu-id="86f0b-269">Spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="86f0b-269">To run the sample:</span></span>

<span data-ttu-id="86f0b-270">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="86f0b-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="86f0b-271">Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet pack.](/dotnet/core/tools/dotnet-pack)</span><span class="sxs-lookup"><span data-stu-id="86f0b-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="86f0b-272">Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="86f0b-273">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-273">Compile and run the app.</span></span> <span data-ttu-id="86f0b-274">Odkaz na balíček je k dispozici v rozšířené aplikaci (odkaz na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="86f0b-275">A `<PropertyGroup>` v souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku.</span><span class="sxs-lookup"><span data-stu-id="86f0b-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="86f0b-276">To umožňuje aplikaci používat balíček bez nahrání balíčku do [nuget.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="86f0b-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="86f0b-277">Všimněte si, že hodnoty konfiguračního klíče služby vykreslené na stránce Index odpovídají hodnotám nastaveným `ServiceKeyInjection.Configure` metodou balíčku.</span><span class="sxs-lookup"><span data-stu-id="86f0b-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="86f0b-278">Pokud provedete změny v projektu *HostingStartupPackage* a znovu jej zkompilovat, zrušte místní mezipaměti balíčku NuGet, abyste zajistili, že *HostingStartupApp* obdrží aktualizovaný balíček a ne zastaralý balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="86f0b-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="86f0b-279">Chcete-li vymazat místní mezipaměti NuGet, spusťte následující příkaz [místní chod dotnet nuget:](/dotnet/core/tools/dotnet-nuget-locals)</span><span class="sxs-lookup"><span data-stu-id="86f0b-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="86f0b-280">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="86f0b-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="86f0b-281">Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet build.](/dotnet/core/tools/dotnet-build)</span><span class="sxs-lookup"><span data-stu-id="86f0b-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="86f0b-282">Přidejte název sestavení knihovny tříd *hostingStartupLibrary* do proměnné `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="86f0b-283">*bin*-nasadit sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary.dll* z kompilovaného výstupu knihovny tříd do složky *bin/Ladění* aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="86f0b-284">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-284">Compile and run the app.</span></span> <span data-ttu-id="86f0b-285">Soubor `<ItemGroup>` projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (odkaz na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="86f0b-286">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="86f0b-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="86f0b-287">Všimněte si, že hodnoty konfiguračního klíče služby vykreslené `ServiceKeyInjection.Configure` na stránce Index odpovídají hodnotám nastaveným metodou knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="86f0b-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="86f0b-288">**Aktivace z runtime store nasazeného sestavení**</span><span class="sxs-lookup"><span data-stu-id="86f0b-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="86f0b-289">Projekt *StartupDiagnostics* používá [prostředí PowerShell](/powershell/scripting/powershell-scripting) k úpravě souboru *StartupDiagnostics.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="86f0b-290">Prostředí PowerShell je ve výchozím nastavení nainstalováno v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="86f0b-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="86f0b-291">Informace o získání prostředí PowerShell na jiných platformách naleznete [v tématu Instalace různých verzí prostředí PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="86f0b-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="86f0b-292">Spusťte skript *build.ps1* ve složce *RuntimeStore.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="86f0b-293">Skript:</span><span class="sxs-lookup"><span data-stu-id="86f0b-293">The script:</span></span>
   * <span data-ttu-id="86f0b-294">Generuje balíček ve `StartupDiagnostics` složce *obj\packages.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="86f0b-295">Generuje runtime úložiště `StartupDiagnostics` pro ve složce *úložiště.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="86f0b-296">Příkaz `dotnet store` ve skriptu `win7-x64` používá [identifikátor runtime (RID)](/dotnet/core/rid-catalog) pro hostitelské spuštění nasazené v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="86f0b-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="86f0b-297">Při poskytování hostitelského spuštění pro jiný běhový čas nahraďte správný rid na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="86f0b-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="86f0b-298">Runtime úložiště `StartupDiagnostics` pro by později přesunuta do úložiště runtime uživatele nebo systému v počítači, kde bude spotřebována sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="86f0b-299">Umístění instalace úložiště runtime `StartupDiagnostics` uživatele pro sestavení je *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="86f0b-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="86f0b-300">Generuje `additionalDeps` for `StartupDiagnostics` ve složce *additionalDeps.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="86f0b-301">Další závislosti by později byly přesunuty do dalších závislostí uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="86f0b-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="86f0b-302">Umístění `StartupDiagnostics` instalace dalších závislostí uživatele *mj.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="86f0b-303">Umístí soubor *deploy.ps1* do složky *nasazení.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="86f0b-304">Spusťte skript *deploy.ps1* ve složce *nasazení.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="86f0b-305">Skript se připojí:</span><span class="sxs-lookup"><span data-stu-id="86f0b-305">The script appends:</span></span>
   * <span data-ttu-id="86f0b-306">`StartupDiagnostics`k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="86f0b-307">Cesta k hostitelským závislostem při spuštění (ve složce `DOTNET_ADDITIONAL_DEPS` *nasazení* projektu RuntimeStore) do proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="86f0b-308">Cesta runtime úložiště (ve složce *nasazení* projektu RuntimeStore) do proměnné `DOTNET_SHARED_STORE` prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="86f0b-309">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-309">Run the sample app.</span></span>
1. <span data-ttu-id="86f0b-310">Požádejte `/services` koncový bod, abyste viděli registrované služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="86f0b-311">Požádejte `/diag` koncový bod, abyste viděli diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="86f0b-312">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostování při spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="86f0b-313">Externí knihovna může například použít implementaci hostitelského spuštění k poskytování dalších poskytovatelů konfigurace nebo služeb pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="86f0b-314">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="86f0b-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="86f0b-315">Atribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="86f0b-315">HostingStartup attribute</span></span>

<span data-ttu-id="86f0b-316">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského spouštěcího sestavení, které se aktivuje za běhu.</span><span class="sxs-lookup"><span data-stu-id="86f0b-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="86f0b-317">Sestavení položky nebo sestavení `Startup` obsahující třídu je automaticky `HostingStartup` zkontrolováno pro atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="86f0b-318">Seznam sestavení pro `HostingStartup` vyhledávání atributů je načten za běhu z konfigurace v [souboru WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="86f0b-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="86f0b-319">Seznam sestavení, která mají být vyloučena z zjišťování, je načten z [webu WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="86f0b-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="86f0b-320">Další informace naleznete v [tématu Web Host: Hostování spouštěcích sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [Web Host: Hostování sestavení vyloučení spuštění](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="86f0b-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="86f0b-321">V následujícím příkladu je `StartupEnhancement`obor názvů hostitelského spouštěcího sestavení .</span><span class="sxs-lookup"><span data-stu-id="86f0b-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="86f0b-322">Třída obsahující kód hostitelského `StartupEnhancementHostingStartup`spuštění je :</span><span class="sxs-lookup"><span data-stu-id="86f0b-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="86f0b-323">Atribut `HostingStartup` je obvykle umístěn v hostitelském `IHostingStartup` spouštěcím sestavení implementační ho souboru třídy.</span><span class="sxs-lookup"><span data-stu-id="86f0b-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="86f0b-324">Objevte načtená hostitelská spouštěcí sestavení</span><span class="sxs-lookup"><span data-stu-id="86f0b-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="86f0b-325">Chcete-li zjistit načtená sestavení po spuštění hostingu, povolte protokolování a zkontrolujte protokoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="86f0b-326">Chyby, ke kterým dochází při načítání sestavení jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="86f0b-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="86f0b-327">Načtená hostitelská spouštěcí sestavení jsou protokolována na úrovni ladění a jsou protokolovány všechny chyby.</span><span class="sxs-lookup"><span data-stu-id="86f0b-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="86f0b-328">Zakázat automatické načítání hostitelských spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="86f0b-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="86f0b-329">Chcete-li zakázat automatické načítání hostitelských spouštěcích sestavení, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="86f0b-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="86f0b-330">Chcete-li zabránit načítání všech hostitelských spouštěcích `true` sestavení, nastavte jednu z následujících možností na nebo `1`:</span><span class="sxs-lookup"><span data-stu-id="86f0b-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="86f0b-331">Zabránit nastavení konfigurace hostitele [po spuštění.](xref:fundamentals/host/web-host#prevent-hosting-startup)</span><span class="sxs-lookup"><span data-stu-id="86f0b-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="86f0b-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP`proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="86f0b-333">Chcete-li zabránit načítání konkrétních hostitelských spouštěcích sestavení, nastavte jednu z následujících na řetězec hostitelských spouštěcích sestavení oddělených středníkem, který chcete při spuštění vyloučit:</span><span class="sxs-lookup"><span data-stu-id="86f0b-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="86f0b-334">Hostování nastavení konfigurace hostitele [sestavení spouštění.](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)</span><span class="sxs-lookup"><span data-stu-id="86f0b-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="86f0b-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="86f0b-336">Pokud je nastaveno nastavení konfigurace hostitele i proměnná prostředí, řídí chování nastavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="86f0b-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="86f0b-337">Zakázání hostitelských spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik charakteristik aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="86f0b-338">Project</span><span class="sxs-lookup"><span data-stu-id="86f0b-338">Project</span></span>

<span data-ttu-id="86f0b-339">Vytvořte hostitelské spuštění s jedním z následujících typů projektů:</span><span class="sxs-lookup"><span data-stu-id="86f0b-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="86f0b-340">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="86f0b-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="86f0b-341">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="86f0b-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="86f0b-342">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="86f0b-342">Class library</span></span>

<span data-ttu-id="86f0b-343">Vylepšení spuštění hostingu lze poskytnout v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="86f0b-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="86f0b-344">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="86f0b-345">[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje aplikaci Razor Pages, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="86f0b-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="86f0b-346">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="86f0b-346">The class library:</span></span>

* <span data-ttu-id="86f0b-347">Obsahuje třídu spuštění `ServiceKeyInjection`hostitele , `IHostingStartup`která implementuje .</span><span class="sxs-lookup"><span data-stu-id="86f0b-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="86f0b-348">`ServiceKeyInjection`přidá dvojici řetězců služby do konfigurace aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="86f0b-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="86f0b-349">Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="86f0b-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="86f0b-350">Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> doplněk k přidání vylepšení do aplikace. `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*></span><span class="sxs-lookup"><span data-stu-id="86f0b-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="86f0b-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="86f0b-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="86f0b-352">Stránka Index aplikace čte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostitelským spouštěcím sestavením knihovny tříd:</span><span class="sxs-lookup"><span data-stu-id="86f0b-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="86f0b-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="86f0b-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="86f0b-354">[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) také obsahuje projekt balíčku NuGet, který poskytuje samostatné hostování *spuštění, HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="86f0b-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="86f0b-355">Balíček má stejné vlastnosti knihovny tříd popsaných výše.</span><span class="sxs-lookup"><span data-stu-id="86f0b-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="86f0b-356">Balíček:</span><span class="sxs-lookup"><span data-stu-id="86f0b-356">The package:</span></span>

* <span data-ttu-id="86f0b-357">Obsahuje třídu spuštění `ServiceKeyInjection`hostitele , `IHostingStartup`která implementuje .</span><span class="sxs-lookup"><span data-stu-id="86f0b-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="86f0b-358">`ServiceKeyInjection`přidá do konfigurace aplikace dvojici řetězců služeb.</span><span class="sxs-lookup"><span data-stu-id="86f0b-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="86f0b-359">Zahrnuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="86f0b-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="86f0b-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="86f0b-361">Stránka Index aplikace čte a vykreslí hodnoty konfigurace pro dva klíče nastavené sestavením hostitelského spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="86f0b-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="86f0b-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="86f0b-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="86f0b-363">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="86f0b-363">Console app without an entry point</span></span>

<span data-ttu-id="86f0b-364">*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli rozhraní .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="86f0b-365">Dynamické vylepšení spuštění hostingu, které nevyžaduje odkaz na aktivaci v době kompilace, může být `HostingStartup` poskytnuto v konzolové aplikaci bez vstupního bodu, který obsahuje atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="86f0b-366">Publikování konzolové aplikace vytvoří hostitelské spouštěcí sestavení, které lze spotřebovat z runtime úložiště.</span><span class="sxs-lookup"><span data-stu-id="86f0b-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="86f0b-367">Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:</span><span class="sxs-lookup"><span data-stu-id="86f0b-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="86f0b-368">Soubor závislostí je nutné využívat hostování spuštění v hostitelském spouštěcím sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="86f0b-369">Soubor závislostí je spustitelný prostředek aplikace, který se vytváří publikováním aplikace, nikoli knihovny.</span><span class="sxs-lookup"><span data-stu-id="86f0b-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="86f0b-370">Knihovnu nelze přidat přímo do [úložiště balíčků runtime](/dotnet/core/deploying/runtime-store), což vyžaduje spustitelný projekt, který cílí na sdílený runtime.</span><span class="sxs-lookup"><span data-stu-id="86f0b-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="86f0b-371">Při vytváření dynamického hostování spuštění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="86f0b-372">Hostitelské spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:</span><span class="sxs-lookup"><span data-stu-id="86f0b-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="86f0b-373">Zahrnuje třídu, `IHostingStartup` která obsahuje implementaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="86f0b-374">Zahrnuje [Atribut HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) k `IHostingStartup` identifikaci třídy implementace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="86f0b-375">Konzolová aplikace je publikována, aby získala závislosti spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="86f0b-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="86f0b-376">Důsledkem publikování konzolové aplikace je, že nepoužívané závislosti jsou oříznuty ze souboru závislostí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="86f0b-377">Soubor závislostí je upraven tak, aby nastavil umístění runtime hostitelského spouštěcího sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="86f0b-378">Hostitelské spouštěcí sestavení a jeho soubor závislostí je umístěn do úložiště balíčků runtime.</span><span class="sxs-lookup"><span data-stu-id="86f0b-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="86f0b-379">Chcete-li zjistit hostitelské spouštěcí sestavení a jeho soubor závislostí, jsou uvedeny v dvojici proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="86f0b-380">Konzolová aplikace odkazuje na balíček [Microsoft.AspNetCore.Hosting.Abstractions:](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/)</span><span class="sxs-lookup"><span data-stu-id="86f0b-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="86f0b-381">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako `IHostingStartup` implementaci pro načítání a <xref:Microsoft.AspNetCore.Hosting.IWebHost>provádění při vytváření .</span><span class="sxs-lookup"><span data-stu-id="86f0b-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="86f0b-382">V následujícím příkladu je `StartupEnhancement`obor názvů a `StartupEnhancementHostingStartup`třída je :</span><span class="sxs-lookup"><span data-stu-id="86f0b-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="86f0b-383">Třída implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="86f0b-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="86f0b-384"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> doplněk k přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="86f0b-385">`IHostingStartup.Configure`v hostiteli spouštěcí sestavení je volána runtime před `Startup.Configure` v uživatelském kódu, který umožňuje uživatelský kód přepsat libovolnou konfiguraci poskytovanou sestavení mnoství po spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="86f0b-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="86f0b-386">Při vytváření `IHostingStartup` projektu nastaví soubor závislostí (*.deps.json*) `runtime` umístění sestavení do složky *přihrádky:*</span><span class="sxs-lookup"><span data-stu-id="86f0b-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="86f0b-387">Zobrazí se pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="86f0b-387">Only part of the file is shown.</span></span> <span data-ttu-id="86f0b-388">Název sestavení v příkladu je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="86f0b-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="86f0b-389">Konfigurace poskytovaná spuštěním hostingu</span><span class="sxs-lookup"><span data-stu-id="86f0b-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="86f0b-390">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, zda chcete, aby konfigurace hostitelského spuštění měla přednost nebo aby měla přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="86f0b-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="86f0b-391">Poskytněte konfiguraci aplikaci pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> načtení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> konfigurace po spuštění delegátů aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="86f0b-392">Hostování konfigurace spuštění má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="86f0b-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="86f0b-393">Poskytněte konfiguraci aplikaci pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> načtení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> konfigurace před spuštěním delegátů aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="86f0b-394">Hodnoty konfigurace aplikace mají přednost před hodnotami poskytovanými spuštěním hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="86f0b-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="86f0b-395">Určení hostitelského spouštěcího sestavení</span><span class="sxs-lookup"><span data-stu-id="86f0b-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="86f0b-396">Pro spuštění hostování dodané knihovnou nebo konzolovou aplikací zadejte název `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hostitelského spouštěcího sestavení v proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="86f0b-397">Proměnná prostředí je seznam sestav oddělených středníkem.</span><span class="sxs-lookup"><span data-stu-id="86f0b-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="86f0b-398">Pro tento `HostingStartup` atribut jsou prohledány pouze hostitelské spouštěcí sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="86f0b-399">Pro ukázkovou aplikaci *HostingStartupApp*, chcete-li zjistit hostitelské startupy popsané výše, proměnná prostředí je nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="86f0b-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="86f0b-400">Hostitelské spouštěcí sestavení lze také nastavit pomocí nastavení konfigurace hostitele hostitelských sestavení [hostování.](xref:fundamentals/host/web-host#hosting-startup-assemblies)</span><span class="sxs-lookup"><span data-stu-id="86f0b-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="86f0b-401">Pokud je k dispozici více <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> hostitelských sestavení po spuštění, jejich metody jsou provedeny v pořadí, ve které jsou uvedena sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="86f0b-402">Aktivace</span><span class="sxs-lookup"><span data-stu-id="86f0b-402">Activation</span></span>

<span data-ttu-id="86f0b-403">Možnosti pro aktivaci pospuštění hostingu jsou:</span><span class="sxs-lookup"><span data-stu-id="86f0b-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="86f0b-404">[Aktivace runtime úložiště](#runtime-store) &ndash; nevyžaduje odkaz na dobu kompilace pro aktivaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-404">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="86f0b-405">Ukázková aplikace umístí hostitelské spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spuštění v prostředí s více počítači.</span><span class="sxs-lookup"><span data-stu-id="86f0b-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="86f0b-406">Složka *nasazení* také obsahuje skript prostředí PowerShell, který vytváří nebo upravuje proměnné prostředí v systému nasazení, aby bylo možné povolit hostování při spuštění.</span><span class="sxs-lookup"><span data-stu-id="86f0b-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="86f0b-407">Odkaz na dobu kompilace potřebný pro aktivaci</span><span class="sxs-lookup"><span data-stu-id="86f0b-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="86f0b-408">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="86f0b-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="86f0b-409">Složka přihrádky projektu</span><span class="sxs-lookup"><span data-stu-id="86f0b-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="86f0b-410">Runtime úložiště</span><span class="sxs-lookup"><span data-stu-id="86f0b-410">Runtime store</span></span>

<span data-ttu-id="86f0b-411">Implementace spuštění hostitele je umístěna v [runtime úložišti](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="86f0b-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="86f0b-412">Odkaz na sestavení v době kompilace není vyžadován vylepšenou aplikací.</span><span class="sxs-lookup"><span data-stu-id="86f0b-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="86f0b-413">Po vytvoření hostitelského spuštění je generováno runtime úložiště pomocí souboru manifestu projektu a příkazu [dotnet store.](/dotnet/core/tools/dotnet-store)</span><span class="sxs-lookup"><span data-stu-id="86f0b-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="86f0b-414">V ukázkové aplikaci *(RuntimeStore* project) se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="86f0b-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="86f0b-415">Pro runtime ke zjištění runtime úložiště, umístění runtime úložiště `DOTNET_SHARED_STORE` je přidán do proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="86f0b-416">**Úprava a umístění souboru závislostí hostitelského spuštění**</span><span class="sxs-lookup"><span data-stu-id="86f0b-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="86f0b-417">Chcete-li aktivovat vylepšení bez odkazu na balíček vylepšení, `additionalDeps`zadejte další závislosti na běhu s .</span><span class="sxs-lookup"><span data-stu-id="86f0b-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="86f0b-418">`additionalDeps`umožňuje:</span><span class="sxs-lookup"><span data-stu-id="86f0b-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="86f0b-419">Rozšiřte graf knihovny aplikace poskytnutím sady dalších souborů *.deps.json,* které se při spuštění sloučí s vlastním souborem *.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="86f0b-420">Nastavení hostitelského spouštěcího sestavení zjistitelné a načítatelné.</span><span class="sxs-lookup"><span data-stu-id="86f0b-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="86f0b-421">Doporučený mdla pro generování dalšího souboru závislostí je:</span><span class="sxs-lookup"><span data-stu-id="86f0b-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="86f0b-422">Spusťte `dotnet publish` v souboru manifestu runtime store, na který odkazuje předchozí část.</span><span class="sxs-lookup"><span data-stu-id="86f0b-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="86f0b-423">Odeberte odkaz na manifest `runtime` z knihoven a části výsledného souboru *.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="86f0b-424">V ukázkovém `store.manifest/1.0.0` projektu je vlastnost `targets` `libraries` odebrána z oddílu a:</span><span class="sxs-lookup"><span data-stu-id="86f0b-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="86f0b-425">Umístěte soubor *.deps.json* do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="86f0b-426">`{ADDITIONAL DEPENDENCIES PATH}`&ndash; Umístění přidané `DOTNET_ADDITIONAL_DEPS` do proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="86f0b-427">`{SHARED FRAMEWORK NAME}`&ndash; Pro tento další soubor závislostí je vyžadovánsdílený rámec.</span><span class="sxs-lookup"><span data-stu-id="86f0b-427">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="86f0b-428">`{SHARED FRAMEWORK VERSION}`&ndash; Minimální verze sdílené ho rámce.</span><span class="sxs-lookup"><span data-stu-id="86f0b-428">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="86f0b-429">`{ENHANCEMENT ASSEMBLY NAME}`&ndash; Název sestavení vylepšení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="86f0b-430">V ukázkové aplikaci (projekt*RuntimeStore)* se další soubor závislostí umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="86f0b-431">Pro runtime ke zjištění umístění úložiště runtime je do proměnné `DOTNET_ADDITIONAL_DEPS` prostředí přidáno další umístění souboru závislostí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="86f0b-432">V ukázkové aplikaci *(runtimestore* projektu), vytváření runtime úložiště a generování další soubor závislostí se provádí pomocí skriptu [Prostředí PowerShell.](/powershell/scripting/powershell-scripting)</span><span class="sxs-lookup"><span data-stu-id="86f0b-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="86f0b-433">Příklady nastavení proměnných prostředí pro různé operační systémy naleznete v tématu [Použití více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="86f0b-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="86f0b-434">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="86f0b-434">**Deployment**</span></span>

<span data-ttu-id="86f0b-435">Pro usnadnění nasazení hostitelského spuštění v prostředí s více počítači vytvoří ukázková aplikace složku *nasazení* v publikovaném výstupu, která obsahuje:</span><span class="sxs-lookup"><span data-stu-id="86f0b-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="86f0b-436">Hostitelské spouštěcí runtime úložiště.</span><span class="sxs-lookup"><span data-stu-id="86f0b-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="86f0b-437">Hostitelský soubor závislostí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="86f0b-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="86f0b-438">Skript prostředí PowerShell, který vytváří `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`nebo `DOTNET_SHARED_STORE`upravuje `DOTNET_ADDITIONAL_DEPS` , a podporuje aktivaci hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="86f0b-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="86f0b-439">Spusťte skript z příkazového řádku prostředí PowerShell pro správu v systému nasazení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="86f0b-440">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="86f0b-440">NuGet package</span></span>

<span data-ttu-id="86f0b-441">Vylepšení spuštění hostingu může být k dispozici v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="86f0b-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="86f0b-442">Balíček `HostingStartup` má atribut.</span><span class="sxs-lookup"><span data-stu-id="86f0b-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="86f0b-443">Typy spuštění hostování poskytované balíčkem jsou k dispozici aplikaci pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="86f0b-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="86f0b-444">Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spuštění v souboru projektu aplikace (odkaz na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="86f0b-445">S odkazem na dobu kompilace na místě, hostování spouštěcí sestavení a všechny jeho závislosti jsou začleněny do souboru závislostí aplikace (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="86f0b-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="86f0b-446">Tento přístup se vztahuje na balíček sestavení hostitelského spuštění publikovaného pro [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="86f0b-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="86f0b-447">Soubor závislostí hostitelského spuštění je k dispozici rozšířené aplikaci, jak je popsáno v části [Runtime store](#runtime-store) (bez odkazu na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="86f0b-448">Další informace o balíčcích NuGet a runtime úložišti naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="86f0b-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="86f0b-449">Jak vytvořit balíček NuGet s nástroji křížové platformy</span><span class="sxs-lookup"><span data-stu-id="86f0b-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="86f0b-450">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="86f0b-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="86f0b-451">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="86f0b-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="86f0b-452">Složka přihrádky projektu</span><span class="sxs-lookup"><span data-stu-id="86f0b-452">Project bin folder</span></span>

<span data-ttu-id="86f0b-453">Vylepšení spouštění hostingu může být poskytnuto sestavením nasazeným *přihrádkou*v rozšířené aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="86f0b-454">Typy spuštění hostování poskytované sestavením jsou k dispozici aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="86f0b-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="86f0b-455">Soubor projektu rozšířené aplikace odkazuje na hostování spuštění (odkaz na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="86f0b-456">S odkazem na dobu kompilace na místě, hostování spouštěcí sestavení a všechny jeho závislosti jsou začleněny do souboru závislostí aplikace (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="86f0b-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="86f0b-457">Tento přístup platí, když scénář nasazení volá pro vytvoření odkazu na sestavení hostitelského spuštění *(soubor dll)* a přesunutí sestavení buď:</span><span class="sxs-lookup"><span data-stu-id="86f0b-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="86f0b-458">Náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="86f0b-458">The consuming project.</span></span>
  * <span data-ttu-id="86f0b-459">Umístění přístupné náročným projektem.</span><span class="sxs-lookup"><span data-stu-id="86f0b-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="86f0b-460">Soubor závislostí hostitelského spuštění je k dispozici rozšířené aplikaci, jak je popsáno v části [Runtime store](#runtime-store) (bez odkazu na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="86f0b-461">Při cílení na rozhraní .NET Framework je sestavení načítatelné ve výchozím kontextu zatížení, což v rozhraní .NET Framework znamená, že sestavení je umístěno v některém z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="86f0b-462">Cesta &ndash; k základně aplikace Složka *přihrádky,* ve které je umístěn spustitelný soubor aplikace (*EXE*).</span><span class="sxs-lookup"><span data-stu-id="86f0b-462">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="86f0b-463">Globální mezipaměti sestavení &ndash; (GAC) GAC ukládá sestavení, které sdílejí několik aplikací rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="86f0b-463">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="86f0b-464">Další informace naleznete v [tématu Postup: Instalace sestavení do globální mezipaměti sestavení](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) v dokumentaci rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="86f0b-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="86f0b-465">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="86f0b-465">Sample code</span></span>

<span data-ttu-id="86f0b-466">[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak stáhnout](xref:index#how-to-download-a-sample)) ukazuje hostování scénářů implementace spuštění:</span><span class="sxs-lookup"><span data-stu-id="86f0b-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="86f0b-467">Dvě hostitelská spouštěcí sestavení (knihovny tříd) nastavují dvojici párů klíč-hodnota konfigurace v paměti:</span><span class="sxs-lookup"><span data-stu-id="86f0b-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="86f0b-468">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="86f0b-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="86f0b-469">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="86f0b-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="86f0b-470">Hostitelské spuštění je aktivováno z runtime store nasazeného sestavení (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="86f0b-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="86f0b-471">Sestavení přidá do aplikace při spuštění dva middlewares, které poskytují diagnostické informace o:</span><span class="sxs-lookup"><span data-stu-id="86f0b-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="86f0b-472">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="86f0b-472">Registered services</span></span>
  * <span data-ttu-id="86f0b-473">Adresa (schéma, hostitel, základna cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="86f0b-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="86f0b-474">Připojení (vzdálená IP adresa, vzdálený port, lokální IP, místní port, klientský certifikát)</span><span class="sxs-lookup"><span data-stu-id="86f0b-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="86f0b-475">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="86f0b-475">Request headers</span></span>
  * <span data-ttu-id="86f0b-476">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="86f0b-476">Environment variables</span></span>

<span data-ttu-id="86f0b-477">Spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="86f0b-477">To run the sample:</span></span>

<span data-ttu-id="86f0b-478">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="86f0b-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="86f0b-479">Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet pack.](/dotnet/core/tools/dotnet-pack)</span><span class="sxs-lookup"><span data-stu-id="86f0b-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="86f0b-480">Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="86f0b-481">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-481">Compile and run the app.</span></span> <span data-ttu-id="86f0b-482">Odkaz na balíček je k dispozici v rozšířené aplikaci (odkaz na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="86f0b-483">A `<PropertyGroup>` v souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku.</span><span class="sxs-lookup"><span data-stu-id="86f0b-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="86f0b-484">To umožňuje aplikaci používat balíček bez nahrání balíčku do [nuget.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="86f0b-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="86f0b-485">Všimněte si, že hodnoty konfiguračního klíče služby vykreslené na stránce Index odpovídají hodnotám nastaveným `ServiceKeyInjection.Configure` metodou balíčku.</span><span class="sxs-lookup"><span data-stu-id="86f0b-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="86f0b-486">Pokud provedete změny v projektu *HostingStartupPackage* a znovu jej zkompilovat, zrušte místní mezipaměti balíčku NuGet, abyste zajistili, že *HostingStartupApp* obdrží aktualizovaný balíček a ne zastaralý balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="86f0b-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="86f0b-487">Chcete-li vymazat místní mezipaměti NuGet, spusťte následující příkaz [místní chod dotnet nuget:](/dotnet/core/tools/dotnet-nuget-locals)</span><span class="sxs-lookup"><span data-stu-id="86f0b-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="86f0b-488">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="86f0b-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="86f0b-489">Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet build.](/dotnet/core/tools/dotnet-build)</span><span class="sxs-lookup"><span data-stu-id="86f0b-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="86f0b-490">Přidejte název sestavení knihovny tříd *hostingStartupLibrary* do proměnné `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="86f0b-491">*bin*-nasadit sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary.dll* z kompilovaného výstupu knihovny tříd do složky *bin/Ladění* aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="86f0b-492">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-492">Compile and run the app.</span></span> <span data-ttu-id="86f0b-493">Soubor `<ItemGroup>` projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (odkaz na kompilaci).</span><span class="sxs-lookup"><span data-stu-id="86f0b-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="86f0b-494">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="86f0b-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="86f0b-495">Všimněte si, že hodnoty konfiguračního klíče služby vykreslené `ServiceKeyInjection.Configure` na stránce Index odpovídají hodnotám nastaveným metodou knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="86f0b-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="86f0b-496">**Aktivace z runtime store nasazeného sestavení**</span><span class="sxs-lookup"><span data-stu-id="86f0b-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="86f0b-497">Projekt *StartupDiagnostics* používá [prostředí PowerShell](/powershell/scripting/powershell-scripting) k úpravě souboru *StartupDiagnostics.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="86f0b-498">Prostředí PowerShell je ve výchozím nastavení nainstalováno v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="86f0b-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="86f0b-499">Informace o získání prostředí PowerShell na jiných platformách naleznete [v tématu Instalace různých verzí prostředí PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="86f0b-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="86f0b-500">Spusťte skript *build.ps1* ve složce *RuntimeStore.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="86f0b-501">Skript:</span><span class="sxs-lookup"><span data-stu-id="86f0b-501">The script:</span></span>
   * <span data-ttu-id="86f0b-502">Generuje balíček ve `StartupDiagnostics` složce *obj\packages.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="86f0b-503">Generuje runtime úložiště `StartupDiagnostics` pro ve složce *úložiště.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="86f0b-504">Příkaz `dotnet store` ve skriptu `win7-x64` používá [identifikátor runtime (RID)](/dotnet/core/rid-catalog) pro hostitelské spuštění nasazené v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="86f0b-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="86f0b-505">Při poskytování hostitelského spuštění pro jiný běhový čas nahraďte správný rid na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="86f0b-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="86f0b-506">Runtime úložiště `StartupDiagnostics` pro by později přesunuta do úložiště runtime uživatele nebo systému v počítači, kde bude spotřebována sestavení.</span><span class="sxs-lookup"><span data-stu-id="86f0b-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="86f0b-507">Umístění instalace úložiště runtime `StartupDiagnostics` uživatele pro sestavení je *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="86f0b-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="86f0b-508">Generuje `additionalDeps` for `StartupDiagnostics` ve složce *additionalDeps.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="86f0b-509">Další závislosti by později byly přesunuty do dalších závislostí uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="86f0b-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="86f0b-510">Umístění `StartupDiagnostics` instalace dalších závislostí uživatele *mj.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="86f0b-511">Umístí soubor *deploy.ps1* do složky *nasazení.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="86f0b-512">Spusťte skript *deploy.ps1* ve složce *nasazení.*</span><span class="sxs-lookup"><span data-stu-id="86f0b-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="86f0b-513">Skript se připojí:</span><span class="sxs-lookup"><span data-stu-id="86f0b-513">The script appends:</span></span>
   * <span data-ttu-id="86f0b-514">`StartupDiagnostics`k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="86f0b-515">Cesta k hostitelským závislostem při spuštění (ve složce `DOTNET_ADDITIONAL_DEPS` *nasazení* projektu RuntimeStore) do proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="86f0b-516">Cesta runtime úložiště (ve složce *nasazení* projektu RuntimeStore) do proměnné `DOTNET_SHARED_STORE` prostředí.</span><span class="sxs-lookup"><span data-stu-id="86f0b-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="86f0b-517">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86f0b-517">Run the sample app.</span></span>
1. <span data-ttu-id="86f0b-518">Požádejte `/services` koncový bod, abyste viděli registrované služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="86f0b-519">Požádejte `/diag` koncový bod, abyste viděli diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="86f0b-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
