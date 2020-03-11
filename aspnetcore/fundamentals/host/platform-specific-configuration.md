---
title: Použití hostování při spuštění sestavení v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak rozšířit aplikace ASP.NET Core z externího sestavení přes IHostingStartup implementace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 71fd5cf1934b5374e0a393e055db23b98c03b62f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660396"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="f0cfb-103">Použití hostování při spuštění sestavení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0cfb-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="f0cfb-104">Od [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0cfb-105">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="f0cfb-106">Například externí knihovnu můžete hostování implementace spuštění uvést další konfigurace zprostředkovatele nebo služby do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="f0cfb-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f0cfb-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="f0cfb-108">Atribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="f0cfb-108">HostingStartup attribute</span></span>

<span data-ttu-id="f0cfb-109">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="f0cfb-110">Sestavení záznamu nebo sestavení obsahující `Startup` třídy je automaticky prohledáno pro atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f0cfb-111">Seznam sestavení pro hledání atributů `HostingStartup` je načtena za běhu z konfigurace v [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="f0cfb-112">Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="f0cfb-113">V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="f0cfb-114">Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f0cfb-115">Atribut `HostingStartup` se obvykle nachází v souboru `IHostingStartup` implementační třídy sestavení, který spouští.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="f0cfb-116">Zjistit načtená hostingu při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="f0cfb-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="f0cfb-117">Ke zřízení načtená hostingu při spuštění sestavení, povolte protokolování a v protokolech aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="f0cfb-118">Jsou zaznamenány chyby, ke kterým dochází při načítání sestavení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="f0cfb-119">Načtená hostingu při spuštění sestavení jsou zaznamenány na úrovni ladění a jsou zaznamenány všechny chyby.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="f0cfb-120">Vypnout automatické načítání hostování při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="f0cfb-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="f0cfb-121">Vypnout automatické načítání hostování při spuštění sestavení, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="f0cfb-122">Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1`:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="f0cfb-123">Zabránit hostování nastavení konfigurace hostitele po spuštění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-123">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="f0cfb-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="f0cfb-125">Pokud chcete zabránit konkrétní hostingu při spuštění sestavení z načítání, nastavte na řetězec oddělený středníkem hostování při spuštění sestavení mají vyloučit při spuštění jednu z následujících:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="f0cfb-126">Hostování nastavení konfigurace vyloučení sestavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="f0cfb-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="f0cfb-128">Pokud nastavení konfigurace hostitele a proměnné prostředí jsou nastaveny, nastavení hostitele řídí chování.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="f0cfb-129">Zakázání hostingu při spuštění sestavení pomocí proměnná hostitele nastavení nebo prostředí globálně zakáže sestavení a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="f0cfb-130">Project</span><span class="sxs-lookup"><span data-stu-id="f0cfb-130">Project</span></span>

<span data-ttu-id="f0cfb-131">Vytvoření hostitelského spouštěcího s jedním z následujících typů projektu:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="f0cfb-132">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="f0cfb-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="f0cfb-133">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="f0cfb-134">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="f0cfb-134">Class library</span></span>

<span data-ttu-id="f0cfb-135">Hostování rozšíření spuštění lze zadat v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="f0cfb-136">Knihovna obsahuje atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f0cfb-137">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor Pages App, *HostingStartupApp*a knihovny tříd *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="f0cfb-138">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-138">The class library:</span></span>

* <span data-ttu-id="f0cfb-139">Obsahuje hostující spouštěcí třídu `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f0cfb-140">`ServiceKeyInjection` do konfigurace aplikace přidá dvojici řetězců služeb pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="f0cfb-141">Zahrnuje atribut `HostingStartup`, který identifikuje obor názvů a třídu pro spuštění hostingu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="f0cfb-142">Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> `ServiceKeyInjection` třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="f0cfb-143">*HostingStartupLibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f0cfb-144">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče, nastavit podle knihovny tříd hostingu při spuštění sestavení:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="f0cfb-145">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="f0cfb-146">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="f0cfb-147">Balíček má stejné charakteristiky knihovna tříd je popsáno výše.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="f0cfb-148">Balíček:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-148">The package:</span></span>

* <span data-ttu-id="f0cfb-149">Obsahuje hostující spouštěcí třídu `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f0cfb-150">`ServiceKeyInjection` do konfigurace aplikace přidá dvojici řetězců služby.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="f0cfb-151">Zahrnuje atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f0cfb-152">*HostingStartupPackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f0cfb-153">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče nastavil hostování sestavení po spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="f0cfb-154">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="f0cfb-155">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-155">Console app without an entry point</span></span>

<span data-ttu-id="f0cfb-156">*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="f0cfb-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="f0cfb-157">Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="f0cfb-158">Publikování aplikace konzoly vytváří hostingu při spuštění sestavení, které mohou být spotřebovány z úložiště modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="f0cfb-159">Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="f0cfb-160">Soubor závislostí je potřeba využívat hostování spouštění v hostitelském sestavení po spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="f0cfb-161">Soubor závislostí je spustitelné aplikace majetek, který je vytvořen a publikovat aplikace, ne z knihovny.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="f0cfb-162">Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="f0cfb-163">Při vytváření dynamické hostování spuštění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="f0cfb-164">Hostování sestavení po spuštění je vytvořený z konzoly aplikace bez vstupní bod, který:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="f0cfb-165">Obsahuje třídu, která obsahuje implementaci `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="f0cfb-166">Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) , který identifikuje třídu implementace `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="f0cfb-167">Publikování aplikace konzoly získat závislosti spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="f0cfb-168">V důsledku publikování aplikace konzoly je, že se ze souboru závislosti oříznut nepoužité závislé součásti.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="f0cfb-169">Změně souboru závislosti modulu runtime pozici hostingu při spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="f0cfb-170">Hostování při spuštění sestavení a jeho závislostí souboru se umístí do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="f0cfb-171">Ke zjištění hostingu při spuštění sestavení a jeho závislostí souboru, jsou uvedeny v pár proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="f0cfb-172">Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="f0cfb-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="f0cfb-173">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="f0cfb-174">V následujícím příkladu je obor názvů `StartupEnhancement`a třída je `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f0cfb-175">Třída implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="f0cfb-176">Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidávání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="f0cfb-177">`IHostingStartup.Configure` v hostitelském sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat libovolnou konfiguraci poskytnutou hostujícím sestavením po spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="f0cfb-178">Při sestavování projektu `IHostingStartup`, soubor závislosti ( *. DEPS. JSON*) nastaví `runtime` umístění sestavení do složky *bin* :</span><span class="sxs-lookup"><span data-stu-id="f0cfb-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="f0cfb-179">Je zobrazena pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-179">Only part of the file is shown.</span></span> <span data-ttu-id="f0cfb-180">Název sestavení v příkladu je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="f0cfb-181">Konfigurace poskytovaná spuštěním hostování</span><span class="sxs-lookup"><span data-stu-id="f0cfb-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="f0cfb-182">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="f0cfb-183">Poskytněte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pro načtení konfigurace po provedení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegátů aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f0cfb-184">Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="f0cfb-185">Zadejte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pro načtení konfigurace před spuštěním delegátů <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f0cfb-186">Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="f0cfb-187">Zadejte hostitelské sestavení po spuštění</span><span class="sxs-lookup"><span data-stu-id="f0cfb-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="f0cfb-188">V případě knihovny tříd nebo konzolové aplikace dodané jako hostitele zadejte název hostitelského sestavení pro spuštění v proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="f0cfb-189">Proměnná prostředí je středníkem oddělený seznam sestavení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="f0cfb-190">Pro atribut `HostingStartup` je prohledávána pouze spouštěcí sestavení hostingu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f0cfb-191">Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="f0cfb-192">Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostování po spouštěcích sestaveních hostitele:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="f0cfb-193">Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="f0cfb-194">Aktivace</span><span class="sxs-lookup"><span data-stu-id="f0cfb-194">Activation</span></span>

<span data-ttu-id="f0cfb-195">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="f0cfb-196">[Běhové úložiště](#runtime-store) &ndash; aktivace nevyžaduje pro aktivaci odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-196">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="f0cfb-197">Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="f0cfb-198">Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="f0cfb-199">Vyžadováno pro aktivaci odkazu za kompilace</span><span class="sxs-lookup"><span data-stu-id="f0cfb-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="f0cfb-200">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="f0cfb-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="f0cfb-201">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="f0cfb-202">Úložiště modulu runtime</span><span class="sxs-lookup"><span data-stu-id="f0cfb-202">Runtime store</span></span>

<span data-ttu-id="f0cfb-203">Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="f0cfb-204">Kompilace odkaz na sestavení není vyžadováno vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="f0cfb-205">Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="f0cfb-206">V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="f0cfb-207">Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do proměnné prostředí `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="f0cfb-208">**Upravit a umístit soubor závislostí se spouštěním hostitele**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="f0cfb-209">Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="f0cfb-210">`additionalDeps` vám umožní:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="f0cfb-211">Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších souborů *. DEPS. JSON* pro sloučení s vlastním souborem *. DEPS. JSON* aplikace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="f0cfb-212">Ujistěte se, hostování sestavení po spuštění zjistitelné a načíst.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="f0cfb-213">Doporučený postup pro vygenerování Další závislosti souboru je:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="f0cfb-214">Spusťte `dotnet publish` v souboru manifestu běhového úložiště, na který se odkazuje v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="f0cfb-215">Odeberte odkaz na manifest z knihoven a část `runtime` výsledného souboru *. DEPS. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="f0cfb-216">V příkladu projektu je vlastnost `store.manifest/1.0.0` odebrána z `targets` a `libraries` části:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="f0cfb-217">Soubor *. DEPS. JSON* umístěte do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="f0cfb-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; umístění přidané do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="f0cfb-219">pro tento soubor dalších závislostí se vyžaduje `{SHARED FRAMEWORK NAME}` &ndash; sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-219">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="f0cfb-220">`{SHARED FRAMEWORK VERSION}` &ndash; minimální verzi sdíleného rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="f0cfb-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; název sestavení rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="f0cfb-222">V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="f0cfb-223">Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí `DOTNET_ADDITIONAL_DEPS` přidáno umístění souborů další závislosti.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="f0cfb-224">V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="f0cfb-225">Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f0cfb-226">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-226">**Deployment**</span></span>

<span data-ttu-id="f0cfb-227">Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="f0cfb-228">Hostování úložiště při spuštění modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="f0cfb-229">Hostování souboru závislostí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="f0cfb-230">Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="f0cfb-231">Spusťte skript z příkazového řádku pro správu prostředí PowerShell v systému pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="f0cfb-232">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="f0cfb-232">NuGet package</span></span>

<span data-ttu-id="f0cfb-233">Hostování rozšíření spuštění lze zadat balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="f0cfb-234">Balíček má atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="f0cfb-235">Hostování typům spouštění poskytovaný balíček jsou k dispozici do aplikace pomocí jedné z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="f0cfb-236">Soubor projektu vylepšené aplikace je odkaz na balíček pro hostování při spuštění v souboru projektu vaší aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="f0cfb-237">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f0cfb-238">Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="f0cfb-239">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="f0cfb-240">Další informace o balíčcích NuGet a úložiště modulu runtime naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="f0cfb-241">Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="f0cfb-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="f0cfb-242">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="f0cfb-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="f0cfb-243">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="f0cfb-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="f0cfb-244">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-244">Project bin folder</span></span>

<span data-ttu-id="f0cfb-245">Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="f0cfb-246">Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="f0cfb-247">Soubor projektu vylepšené aplikace je odkaz na sestavení pro spouštění hostování (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="f0cfb-248">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f0cfb-249">Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="f0cfb-250">Projekt, který je k disnáročné.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-250">The consuming project.</span></span>
  * <span data-ttu-id="f0cfb-251">Umístění přístupné pro náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="f0cfb-252">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="f0cfb-253">Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="f0cfb-254">Základní cesta aplikace &ndash; složku *bin* , kde se nachází spustitelný soubor aplikace ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-254">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="f0cfb-255">Globální mezipaměť sestavení (GAC) &ndash; GAC ukládá sestavení, která sdílí několik .NET Frameworkch aplikací.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-255">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="f0cfb-256">Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="f0cfb-257">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-257">Sample code</span></span>

<span data-ttu-id="f0cfb-258">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="f0cfb-259">Dvě hostování při spuštění sestavení (knihoven tříd) nastavit pár konfigurace v paměti páry klíč hodnota každé:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="f0cfb-260">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="f0cfb-261">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="f0cfb-262">Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="f0cfb-263">Sestavení přidá do aplikace při spuštění obsahující diagnostické informace o dvou middlewares:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="f0cfb-264">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="f0cfb-264">Registered services</span></span>
  * <span data-ttu-id="f0cfb-265">Adresa (schéma, hostitele, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="f0cfb-266">Připojení (vzdálenou IP adresu, vzdálený port, místní IP adresu, místní port, klientského certifikátu)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="f0cfb-267">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="f0cfb-267">Request headers</span></span>
  * <span data-ttu-id="f0cfb-268">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="f0cfb-268">Environment variables</span></span>

<span data-ttu-id="f0cfb-269">Ke spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-269">To run the sample:</span></span>

<span data-ttu-id="f0cfb-270">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="f0cfb-271">Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="f0cfb-272">Přidejte název sestavení balíčku *HostingStartupPackage* do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f0cfb-273">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-273">Compile and run the app.</span></span> <span data-ttu-id="f0cfb-274">Odkaz na balíček je k dispozici vylepšené aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="f0cfb-275">`<PropertyGroup>` v souboru projektu aplikace určuje výstup projektu balíčku ( *.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="f0cfb-276">To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f0cfb-277">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným `ServiceKeyInjection.Configure` metodou balíčku.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f0cfb-278">Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="f0cfb-279">K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :</span><span class="sxs-lookup"><span data-stu-id="f0cfb-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="f0cfb-280">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="f0cfb-281">Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="f0cfb-282">Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f0cfb-283">*bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary. dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="f0cfb-284">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-284">Compile and run the app.</span></span> <span data-ttu-id="f0cfb-285">`<ItemGroup>` v souboru projektu aplikace odkazuje na sestavení knihovny tříd ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="f0cfb-286">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="f0cfb-287">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou `ServiceKeyInjection.Configure` knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f0cfb-288">**Aktivace z běhového úložiště – nasazené sestavení**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="f0cfb-289">Projekt *StartupDiagnostics* používá k úpravě souboru *StartupDiagnostics. DEPS. JSON* [PowerShell](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="f0cfb-290">Prostředí PowerShell se instaluje standardně na Windows od verze Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="f0cfb-291">Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace prostředí Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-291">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="f0cfb-292">Spusťte skript *Build. ps1* ve složce *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="f0cfb-293">Skript:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-293">The script:</span></span>
   * <span data-ttu-id="f0cfb-294">Vygeneruje balíček `StartupDiagnostics` ve složce *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="f0cfb-295">Generuje úložiště modulu runtime pro `StartupDiagnostics` ve složce *Store* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="f0cfb-296">Příkaz `dotnet store` ve skriptu používá [identifikátor modulu runtime `win7-x64` (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="f0cfb-297">Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="f0cfb-298">Běhové úložiště pro `StartupDiagnostics` bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="f0cfb-299">Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="f0cfb-300">Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="f0cfb-301">Další závislosti by se později přesunuly na další závislosti uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="f0cfb-302">Uživatel `StartupDiagnostics` další závislosti umístění instalace je *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/3.0.0/StartupDiagnostics. DEPS. JSON*.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="f0cfb-303">Umístí soubor *Deploy. ps1* do složky pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="f0cfb-304">Spusťte skript *Deploy. ps1* ve složce pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="f0cfb-305">Skript připojí:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-305">The script appends:</span></span>
   * <span data-ttu-id="f0cfb-306">`StartupDiagnostics` do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="f0cfb-307">Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="f0cfb-308">Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="f0cfb-309">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-309">Run the sample app.</span></span>
1. <span data-ttu-id="f0cfb-310">Požádejte o `/services` koncový bod pro zobrazení registrovaných služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="f0cfb-311">Vyžádejte `/diag` koncový bod, aby se zobrazily diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f0cfb-312">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="f0cfb-313">Například externí knihovnu můžete hostování implementace spuštění uvést další konfigurace zprostředkovatele nebo služby do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="f0cfb-314">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f0cfb-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="f0cfb-315">Atribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="f0cfb-315">HostingStartup attribute</span></span>

<span data-ttu-id="f0cfb-316">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="f0cfb-317">Sestavení záznamu nebo sestavení obsahující `Startup` třídy je automaticky prohledáno pro atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f0cfb-318">Seznam sestavení pro hledání atributů `HostingStartup` je načtena za běhu z konfigurace v [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="f0cfb-319">Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="f0cfb-320">Další informace najdete v tématu [webový hostitel: hostování spouštěcích sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [webového hostitele: hostování spouštěcích vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="f0cfb-321">V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="f0cfb-322">Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f0cfb-323">Atribut `HostingStartup` se obvykle nachází v souboru `IHostingStartup` implementační třídy sestavení, který spouští.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="f0cfb-324">Zjistit načtená hostingu při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="f0cfb-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="f0cfb-325">Ke zřízení načtená hostingu při spuštění sestavení, povolte protokolování a v protokolech aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="f0cfb-326">Jsou zaznamenány chyby, ke kterým dochází při načítání sestavení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="f0cfb-327">Načtená hostingu při spuštění sestavení jsou zaznamenány na úrovni ladění a jsou zaznamenány všechny chyby.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="f0cfb-328">Vypnout automatické načítání hostování při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="f0cfb-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="f0cfb-329">Vypnout automatické načítání hostování při spuštění sestavení, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="f0cfb-330">Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1`:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="f0cfb-331">Zabrání hostování nastavení konfigurace hostitele [po spuštění](xref:fundamentals/host/web-host#prevent-hosting-startup) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="f0cfb-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="f0cfb-333">Pokud chcete zabránit konkrétní hostingu při spuštění sestavení z načítání, nastavte na řetězec oddělený středníkem hostování při spuštění sestavení mají vyloučit při spuštění jednu z následujících:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="f0cfb-334">Hostování nastavení konfigurace hostitele [vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="f0cfb-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="f0cfb-336">Pokud nastavení konfigurace hostitele a proměnné prostředí jsou nastaveny, nastavení hostitele řídí chování.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="f0cfb-337">Zakázání hostingu při spuštění sestavení pomocí proměnná hostitele nastavení nebo prostředí globálně zakáže sestavení a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="f0cfb-338">Project</span><span class="sxs-lookup"><span data-stu-id="f0cfb-338">Project</span></span>

<span data-ttu-id="f0cfb-339">Vytvoření hostitelského spouštěcího s jedním z následujících typů projektu:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="f0cfb-340">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="f0cfb-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="f0cfb-341">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="f0cfb-342">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="f0cfb-342">Class library</span></span>

<span data-ttu-id="f0cfb-343">Hostování rozšíření spuštění lze zadat v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="f0cfb-344">Knihovna obsahuje atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f0cfb-345">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor Pages App, *HostingStartupApp*a knihovny tříd *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="f0cfb-346">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-346">The class library:</span></span>

* <span data-ttu-id="f0cfb-347">Obsahuje hostující spouštěcí třídu `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f0cfb-348">`ServiceKeyInjection` do konfigurace aplikace přidá dvojici řetězců služeb pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="f0cfb-349">Zahrnuje atribut `HostingStartup`, který identifikuje obor názvů a třídu pro spuštění hostingu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="f0cfb-350">Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> `ServiceKeyInjection` třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="f0cfb-351">*HostingStartupLibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f0cfb-352">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče, nastavit podle knihovny tříd hostingu při spuštění sestavení:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="f0cfb-353">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="f0cfb-354">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="f0cfb-355">Balíček má stejné charakteristiky knihovna tříd je popsáno výše.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="f0cfb-356">Balíček:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-356">The package:</span></span>

* <span data-ttu-id="f0cfb-357">Obsahuje hostující spouštěcí třídu `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f0cfb-358">`ServiceKeyInjection` do konfigurace aplikace přidá dvojici řetězců služby.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="f0cfb-359">Zahrnuje atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f0cfb-360">*HostingStartupPackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f0cfb-361">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče nastavil hostování sestavení po spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="f0cfb-362">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="f0cfb-363">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-363">Console app without an entry point</span></span>

<span data-ttu-id="f0cfb-364">*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="f0cfb-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="f0cfb-365">Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="f0cfb-366">Publikování aplikace konzoly vytváří hostingu při spuštění sestavení, které mohou být spotřebovány z úložiště modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="f0cfb-367">Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="f0cfb-368">Soubor závislostí je potřeba využívat hostování spouštění v hostitelském sestavení po spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="f0cfb-369">Soubor závislostí je spustitelné aplikace majetek, který je vytvořen a publikovat aplikace, ne z knihovny.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="f0cfb-370">Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="f0cfb-371">Při vytváření dynamické hostování spuštění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="f0cfb-372">Hostování sestavení po spuštění je vytvořený z konzoly aplikace bez vstupní bod, který:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="f0cfb-373">Obsahuje třídu, která obsahuje implementaci `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="f0cfb-374">Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) , který identifikuje třídu implementace `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="f0cfb-375">Publikování aplikace konzoly získat závislosti spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="f0cfb-376">V důsledku publikování aplikace konzoly je, že se ze souboru závislosti oříznut nepoužité závislé součásti.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="f0cfb-377">Změně souboru závislosti modulu runtime pozici hostingu při spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="f0cfb-378">Hostování při spuštění sestavení a jeho závislostí souboru se umístí do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="f0cfb-379">Ke zjištění hostingu při spuštění sestavení a jeho závislostí souboru, jsou uvedeny v pár proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="f0cfb-380">Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="f0cfb-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="f0cfb-381">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="f0cfb-382">V následujícím příkladu je obor názvů `StartupEnhancement`a třída je `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f0cfb-383">Třída implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="f0cfb-384">Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidávání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="f0cfb-385">`IHostingStartup.Configure` v hostitelském sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat libovolnou konfiguraci poskytnutou hostujícím sestavením po spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="f0cfb-386">Při sestavování projektu `IHostingStartup`, soubor závislosti ( *. DEPS. JSON*) nastaví `runtime` umístění sestavení do složky *bin* :</span><span class="sxs-lookup"><span data-stu-id="f0cfb-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="f0cfb-387">Je zobrazena pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-387">Only part of the file is shown.</span></span> <span data-ttu-id="f0cfb-388">Název sestavení v příkladu je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="f0cfb-389">Konfigurace poskytovaná spuštěním hostování</span><span class="sxs-lookup"><span data-stu-id="f0cfb-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="f0cfb-390">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="f0cfb-391">Poskytněte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pro načtení konfigurace po provedení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegátů aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f0cfb-392">Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="f0cfb-393">Zadejte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pro načtení konfigurace před spuštěním delegátů <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f0cfb-394">Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="f0cfb-395">Zadejte hostitelské sestavení po spuštění</span><span class="sxs-lookup"><span data-stu-id="f0cfb-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="f0cfb-396">V případě knihovny tříd nebo konzolové aplikace dodané jako hostitele zadejte název hostitelského sestavení pro spuštění v proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="f0cfb-397">Proměnná prostředí je středníkem oddělený seznam sestavení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="f0cfb-398">Pro atribut `HostingStartup` je prohledávána pouze spouštěcí sestavení hostingu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f0cfb-399">Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="f0cfb-400">Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostitele [po spouštěcích sestaveních](xref:fundamentals/host/web-host#hosting-startup-assemblies) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="f0cfb-401">Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="f0cfb-402">Aktivace</span><span class="sxs-lookup"><span data-stu-id="f0cfb-402">Activation</span></span>

<span data-ttu-id="f0cfb-403">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="f0cfb-404">[Běhové úložiště](#runtime-store) &ndash; aktivace nevyžaduje pro aktivaci odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-404">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="f0cfb-405">Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="f0cfb-406">Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="f0cfb-407">Vyžadováno pro aktivaci odkazu za kompilace</span><span class="sxs-lookup"><span data-stu-id="f0cfb-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="f0cfb-408">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="f0cfb-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="f0cfb-409">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="f0cfb-410">Úložiště modulu runtime</span><span class="sxs-lookup"><span data-stu-id="f0cfb-410">Runtime store</span></span>

<span data-ttu-id="f0cfb-411">Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="f0cfb-412">Kompilace odkaz na sestavení není vyžadováno vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="f0cfb-413">Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="f0cfb-414">V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="f0cfb-415">Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do proměnné prostředí `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="f0cfb-416">**Upravit a umístit soubor závislostí se spouštěním hostitele**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="f0cfb-417">Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="f0cfb-418">`additionalDeps` vám umožní:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="f0cfb-419">Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších souborů *. DEPS. JSON* pro sloučení s vlastním souborem *. DEPS. JSON* aplikace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="f0cfb-420">Ujistěte se, hostování sestavení po spuštění zjistitelné a načíst.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="f0cfb-421">Doporučený postup pro vygenerování Další závislosti souboru je:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="f0cfb-422">Spusťte `dotnet publish` v souboru manifestu běhového úložiště, na který se odkazuje v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="f0cfb-423">Odeberte odkaz na manifest z knihoven a část `runtime` výsledného souboru *. DEPS. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="f0cfb-424">V příkladu projektu je vlastnost `store.manifest/1.0.0` odebrána z `targets` a `libraries` části:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="f0cfb-425">Soubor *. DEPS. JSON* umístěte do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="f0cfb-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; umístění přidané do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="f0cfb-427">pro tento soubor dalších závislostí se vyžaduje `{SHARED FRAMEWORK NAME}` &ndash; sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-427">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="f0cfb-428">`{SHARED FRAMEWORK VERSION}` &ndash; minimální verzi sdíleného rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-428">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="f0cfb-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; název sestavení rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="f0cfb-430">V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="f0cfb-431">Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí `DOTNET_ADDITIONAL_DEPS` přidáno umístění souborů další závislosti.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="f0cfb-432">V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="f0cfb-433">Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f0cfb-434">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-434">**Deployment**</span></span>

<span data-ttu-id="f0cfb-435">Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="f0cfb-436">Hostování úložiště při spuštění modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="f0cfb-437">Hostování souboru závislostí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="f0cfb-438">Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="f0cfb-439">Spusťte skript z příkazového řádku pro správu prostředí PowerShell v systému pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="f0cfb-440">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="f0cfb-440">NuGet package</span></span>

<span data-ttu-id="f0cfb-441">Hostování rozšíření spuštění lze zadat balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="f0cfb-442">Balíček má atribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="f0cfb-443">Hostování typům spouštění poskytovaný balíček jsou k dispozici do aplikace pomocí jedné z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="f0cfb-444">Soubor projektu vylepšené aplikace je odkaz na balíček pro hostování při spuštění v souboru projektu vaší aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="f0cfb-445">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f0cfb-446">Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="f0cfb-447">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="f0cfb-448">Další informace o balíčcích NuGet a úložiště modulu runtime naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="f0cfb-449">Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="f0cfb-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="f0cfb-450">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="f0cfb-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="f0cfb-451">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="f0cfb-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="f0cfb-452">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-452">Project bin folder</span></span>

<span data-ttu-id="f0cfb-453">Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="f0cfb-454">Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="f0cfb-455">Soubor projektu vylepšené aplikace je odkaz na sestavení pro spouštění hostování (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="f0cfb-456">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f0cfb-457">Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="f0cfb-458">Projekt, který je k disnáročné.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-458">The consuming project.</span></span>
  * <span data-ttu-id="f0cfb-459">Umístění přístupné pro náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="f0cfb-460">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="f0cfb-461">Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="f0cfb-462">Základní cesta aplikace &ndash; složku *bin* , kde se nachází spustitelný soubor aplikace ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-462">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="f0cfb-463">Globální mezipaměť sestavení (GAC) &ndash; GAC ukládá sestavení, která sdílí několik .NET Frameworkch aplikací.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-463">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="f0cfb-464">Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="f0cfb-465">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="f0cfb-465">Sample code</span></span>

<span data-ttu-id="f0cfb-466">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="f0cfb-467">Dvě hostování při spuštění sestavení (knihoven tříd) nastavit pár konfigurace v paměti páry klíč hodnota každé:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="f0cfb-468">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="f0cfb-469">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="f0cfb-470">Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="f0cfb-471">Sestavení přidá do aplikace při spuštění obsahující diagnostické informace o dvou middlewares:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="f0cfb-472">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="f0cfb-472">Registered services</span></span>
  * <span data-ttu-id="f0cfb-473">Adresa (schéma, hostitele, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="f0cfb-474">Připojení (vzdálenou IP adresu, vzdálený port, místní IP adresu, místní port, klientského certifikátu)</span><span class="sxs-lookup"><span data-stu-id="f0cfb-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="f0cfb-475">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="f0cfb-475">Request headers</span></span>
  * <span data-ttu-id="f0cfb-476">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="f0cfb-476">Environment variables</span></span>

<span data-ttu-id="f0cfb-477">Ke spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-477">To run the sample:</span></span>

<span data-ttu-id="f0cfb-478">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="f0cfb-479">Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="f0cfb-480">Přidejte název sestavení balíčku *HostingStartupPackage* do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f0cfb-481">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-481">Compile and run the app.</span></span> <span data-ttu-id="f0cfb-482">Odkaz na balíček je k dispozici vylepšené aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="f0cfb-483">`<PropertyGroup>` v souboru projektu aplikace určuje výstup projektu balíčku ( *.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="f0cfb-484">To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f0cfb-485">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným `ServiceKeyInjection.Configure` metodou balíčku.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f0cfb-486">Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="f0cfb-487">K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :</span><span class="sxs-lookup"><span data-stu-id="f0cfb-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="f0cfb-488">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="f0cfb-489">Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="f0cfb-490">Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f0cfb-491">*bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary. dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="f0cfb-492">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-492">Compile and run the app.</span></span> <span data-ttu-id="f0cfb-493">`<ItemGroup>` v souboru projektu aplikace odkazuje na sestavení knihovny tříd ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="f0cfb-494">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="f0cfb-495">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou `ServiceKeyInjection.Configure` knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f0cfb-496">**Aktivace z běhového úložiště – nasazené sestavení**</span><span class="sxs-lookup"><span data-stu-id="f0cfb-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="f0cfb-497">Projekt *StartupDiagnostics* používá k úpravě souboru *StartupDiagnostics. DEPS. JSON* [PowerShell](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="f0cfb-498">Prostředí PowerShell se instaluje standardně na Windows od verze Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="f0cfb-499">Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace prostředí Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span><span class="sxs-lookup"><span data-stu-id="f0cfb-499">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="f0cfb-500">Spusťte skript *Build. ps1* ve složce *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="f0cfb-501">Skript:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-501">The script:</span></span>
   * <span data-ttu-id="f0cfb-502">Vygeneruje balíček `StartupDiagnostics` ve složce *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="f0cfb-503">Generuje úložiště modulu runtime pro `StartupDiagnostics` ve složce *Store* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="f0cfb-504">Příkaz `dotnet store` ve skriptu používá [identifikátor modulu runtime `win7-x64` (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="f0cfb-505">Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="f0cfb-506">Běhové úložiště pro `StartupDiagnostics` bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="f0cfb-507">Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="f0cfb-508">Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="f0cfb-509">Další závislosti by se později přesunuly na další závislosti uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="f0cfb-510">Uživatel `StartupDiagnostics` další závislosti umístění instalace je *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/2.2.0/StartupDiagnostics. DEPS. JSON*.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="f0cfb-511">Umístí soubor *Deploy. ps1* do složky pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="f0cfb-512">Spusťte skript *Deploy. ps1* ve složce pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="f0cfb-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="f0cfb-513">Skript připojí:</span><span class="sxs-lookup"><span data-stu-id="f0cfb-513">The script appends:</span></span>
   * <span data-ttu-id="f0cfb-514">`StartupDiagnostics` do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="f0cfb-515">Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="f0cfb-516">Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="f0cfb-517">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-517">Run the sample app.</span></span>
1. <span data-ttu-id="f0cfb-518">Požádejte o `/services` koncový bod pro zobrazení registrovaných služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="f0cfb-519">Vyžádejte `/diag` koncový bod, aby se zobrazily diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="f0cfb-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
