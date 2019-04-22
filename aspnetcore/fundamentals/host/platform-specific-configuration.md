---
title: Použití hostování při spuštění sestavení v ASP.NET Core
author: guardrex
description: Zjistěte, jak rozšířit aplikace ASP.NET Core z externího sestavení přes IHostingStartup implementace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 04/06/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c2a2e1fbd288ff292c6759d03fae51876cdb5704
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59425072"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="b0528-103">Použití hostování při spuštění sestavení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0528-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="b0528-104">Podle [Luke Latham](https://github.com/guardrex) a [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="b0528-104">By [Luke Latham](https://github.com/guardrex) and [Pavel Krymets](https://github.com/pakrym)</span></span>

<span data-ttu-id="b0528-105">[IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (který je hostitelem spouštěcí) implementace vylepšení přidá do aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="b0528-105">An [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="b0528-106">Například externí knihovnu můžete hostování implementace spuštění uvést další konfigurace zprostředkovatele nebo služby do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="b0528-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0528-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="b0528-108">Atribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="b0528-108">HostingStartup attribute</span></span>

<span data-ttu-id="b0528-109">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) atribut indikuje přítomnost hostování sestavení po spuštění k aktivaci za běhu.</span><span class="sxs-lookup"><span data-stu-id="b0528-109">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="b0528-110">Vstupní sestavení nebo sestavení obsahující `Startup` třídy automaticky vyhledávat `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b0528-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b0528-111">Seznam sestavení, které chcete hledat `HostingStartup` atributy je načtená v době běhu z konfigurace v [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span><span class="sxs-lookup"><span data-stu-id="b0528-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span></span> <span data-ttu-id="b0528-112">Načíst seznam sestavení, které chcete vyloučit ze zjišťování z [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span><span class="sxs-lookup"><span data-stu-id="b0528-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span></span> <span data-ttu-id="b0528-113">Další informace najdete v tématu [webového hostitele: Hostování při spuštění sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [webového hostitele: Po spuštění hostování vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="b0528-113">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="b0528-114">V následujícím příkladu, obor názvů hostování sestavení po spuštění je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b0528-114">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="b0528-115">Třída obsahující kód hostování spuštění je `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b0528-115">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b0528-116">`HostingStartup` Atribut se obvykle nachází v hostitelském sestavení po spuštění `IHostingStartup` souboru implementace třídy.</span><span class="sxs-lookup"><span data-stu-id="b0528-116">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="b0528-117">Zjistit načtená hostingu při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="b0528-117">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="b0528-118">Ke zřízení načtená hostingu při spuštění sestavení, povolte protokolování a v protokolech aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-118">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="b0528-119">Jsou zaznamenány chyby, ke kterým dochází při načítání sestavení.</span><span class="sxs-lookup"><span data-stu-id="b0528-119">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="b0528-120">Načtená hostingu při spuštění sestavení jsou zaznamenány na úrovni ladění a jsou zaznamenány všechny chyby.</span><span class="sxs-lookup"><span data-stu-id="b0528-120">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="b0528-121">Vypnout automatické načítání hostování při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="b0528-121">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="b0528-122">Vypnout automatické načítání hostování při spuštění sestavení, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="b0528-122">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="b0528-123">Abyste zabránili všechna sestavení po spuštění hostování načítání, nastavte jednu z následujících způsobů `true` nebo `1`:</span><span class="sxs-lookup"><span data-stu-id="b0528-123">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="b0528-124">[Zabránit spuštění hostování](xref:fundamentals/host/web-host#prevent-hosting-startup) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b0528-124">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="b0528-125">`ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-125">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="b0528-126">Pokud chcete zabránit konkrétní hostingu při spuštění sestavení z načítání, nastavte na řetězec oddělený středníkem hostování při spuštění sestavení mají vyloučit při spuštění jednu z následujících:</span><span class="sxs-lookup"><span data-stu-id="b0528-126">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="b0528-127">[Hostování sestavení vyloučit při spuštění](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b0528-127">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="b0528-128">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-128">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="b0528-129">Pokud nastavení konfigurace hostitele a proměnné prostředí jsou nastaveny, nastavení hostitele řídí chování.</span><span class="sxs-lookup"><span data-stu-id="b0528-129">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="b0528-130">Zakázání hostingu při spuštění sestavení pomocí proměnná hostitele nastavení nebo prostředí globálně zakáže sestavení a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-130">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="b0528-131">Projekt</span><span class="sxs-lookup"><span data-stu-id="b0528-131">Project</span></span>

<span data-ttu-id="b0528-132">Vytvoření hostitelského spouštěcího s jedním z následujících typů projektu:</span><span class="sxs-lookup"><span data-stu-id="b0528-132">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="b0528-133">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="b0528-133">Class library</span></span>](#class-library)
* [<span data-ttu-id="b0528-134">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="b0528-134">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="b0528-135">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="b0528-135">Class library</span></span>

<span data-ttu-id="b0528-136">Hostování rozšíření spuštění lze zadat v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="b0528-136">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="b0528-137">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b0528-137">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b0528-138">[Ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zahrnuje aplikace stránky Razor, *HostingStartupApp*a knihovny tříd, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="b0528-138">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="b0528-139">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="b0528-139">The class library:</span></span>

* <span data-ttu-id="b0528-140">Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b0528-140">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b0528-141">`ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span><span class="sxs-lookup"><span data-stu-id="b0528-141">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span></span>
* <span data-ttu-id="b0528-142">Zahrnuje `HostingStartup` atribut, který identifikuje obor názvů a třídy spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b0528-142">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="b0528-143">`ServiceKeyInjection` Třídy [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) metoda používá [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-143">The `ServiceKeyInjection` class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span>

<span data-ttu-id="b0528-144">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0528-144">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b0528-145">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče, nastavit podle knihovny tříd hostingu při spuštění sestavení:</span><span class="sxs-lookup"><span data-stu-id="b0528-145">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="b0528-146">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0528-146">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="b0528-147">[Ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) také zahrnuje projektu balíček NuGet, který poskytuje samostatné hostitelské spuštění *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="b0528-147">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="b0528-148">Balíček má stejné charakteristiky knihovna tříd je popsáno výše.</span><span class="sxs-lookup"><span data-stu-id="b0528-148">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="b0528-149">Balíček:</span><span class="sxs-lookup"><span data-stu-id="b0528-149">The package:</span></span>

* <span data-ttu-id="b0528-150">Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b0528-150">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b0528-151">`ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-151">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="b0528-152">Zahrnuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b0528-152">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b0528-153">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0528-153">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b0528-154">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče nastavil hostování sestavení po spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="b0528-154">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="b0528-155">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0528-155">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="b0528-156">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="b0528-156">Console app without an entry point</span></span>

<span data-ttu-id="b0528-157">*Tento přístup je dostupná jenom pro aplikace .NET Core, není rozhraní .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="b0528-157">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="b0528-158">Dynamické vylepšení spuštění hostování, který nevyžaduje odkaz kompilace pro aktivaci lze zadat v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b0528-158">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="b0528-159">Publikování aplikace konzoly vytváří hostingu při spuštění sestavení, které mohou být spotřebovány z úložiště modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="b0528-159">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="b0528-160">Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:</span><span class="sxs-lookup"><span data-stu-id="b0528-160">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="b0528-161">Soubor závislostí je potřeba využívat hostování spouštění v hostitelském sestavení po spuštění.</span><span class="sxs-lookup"><span data-stu-id="b0528-161">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="b0528-162">Soubor závislostí je spustitelné aplikace majetek, který je vytvořen a publikovat aplikace, ne z knihovny.</span><span class="sxs-lookup"><span data-stu-id="b0528-162">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="b0528-163">Knihovny nelze přímo přidat [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje spustitelný projekt, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="b0528-163">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="b0528-164">Při vytváření dynamické hostování spuštění:</span><span class="sxs-lookup"><span data-stu-id="b0528-164">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="b0528-165">Hostování sestavení po spuštění je vytvořený z konzoly aplikace bez vstupní bod, který:</span><span class="sxs-lookup"><span data-stu-id="b0528-165">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="b0528-166">Obsahuje třídu, která obsahuje `IHostingStartup` implementace.</span><span class="sxs-lookup"><span data-stu-id="b0528-166">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="b0528-167">Zahrnuje [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) atribut k identifikaci `IHostingStartup` implementace třídy.</span><span class="sxs-lookup"><span data-stu-id="b0528-167">Includes a [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="b0528-168">Publikování aplikace konzoly získat závislosti spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b0528-168">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="b0528-169">V důsledku publikování aplikace konzoly je, že se ze souboru závislosti oříznut nepoužité závislé součásti.</span><span class="sxs-lookup"><span data-stu-id="b0528-169">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="b0528-170">Změně souboru závislosti modulu runtime pozici hostingu při spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="b0528-170">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="b0528-171">Hostování při spuštění sestavení a jeho závislostí souboru se umístí do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="b0528-171">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="b0528-172">Ke zjištění hostingu při spuštění sestavení a jeho závislostí souboru, jsou uvedeny v pár proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-172">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="b0528-173">Odkazy na aplikace konzoly [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) balíčku:</span><span class="sxs-lookup"><span data-stu-id="b0528-173">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="b0528-174">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) atribut určuje třídu jako implementace `IHostingStartup` pro spuštění při vytváření a načítání [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span><span class="sxs-lookup"><span data-stu-id="b0528-174">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span></span> <span data-ttu-id="b0528-175">V následujícím příkladu je obor názvů `StartupEnhancement`, a je třída `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b0528-175">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b0528-176">Třída implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b0528-176">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="b0528-177">Třídy [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) metoda používá [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-177">The class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="b0528-178">`IHostingStartup.Configure` v hostitelských spuštění sestavení je volána modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat žádnou konfiguraci poskytované hostujícím sestavení při spuštění uživatelského kódu.</span><span class="sxs-lookup"><span data-stu-id="b0528-178">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="b0528-179">Při vytváření `IHostingStartup` závislostí souboru projektu (*\*. deps.json*) nastaví `runtime` umístění sestavení *bin* složky:</span><span class="sxs-lookup"><span data-stu-id="b0528-179">When building an `IHostingStartup` project, the dependencies file (*\*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="b0528-180">Je zobrazena pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="b0528-180">Only part of the file is shown.</span></span> <span data-ttu-id="b0528-181">Název sestavení v příkladu je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b0528-181">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="b0528-182">Konfigurace poskytované spuštění hostování</span><span class="sxs-lookup"><span data-stu-id="b0528-182">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="b0528-183">Zpracování konfigurace v závislosti na tom, jestli chcete konfiguraci spuštění hostování přednost nebo konfiguraci aplikace, přednost dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="b0528-183">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="b0528-184">Zadejte konfiguraci pro aplikace s využitím <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> načíst konfiguraci po aplikace <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> provést delegátů.</span><span class="sxs-lookup"><span data-stu-id="b0528-184">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b0528-185">Konfigurace hostování spuštění má přednost před konfiguraci aplikace, tento přístup.</span><span class="sxs-lookup"><span data-stu-id="b0528-185">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="b0528-186">Zadejte konfiguraci pro aplikace s využitím <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> načíst konfiguraci před aplikace <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> provést delegátů.</span><span class="sxs-lookup"><span data-stu-id="b0528-186">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b0528-187">Hodnoty konfigurace mají přednost před poskytovaným hostování spuštění tento přístup.</span><span class="sxs-lookup"><span data-stu-id="b0528-187">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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
                    "From IHostingStartup: Higher priority than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="b0528-188">Zadejte hostitelské sestavení po spuštění</span><span class="sxs-lookup"><span data-stu-id="b0528-188">Specify the hosting startup assembly</span></span>

<span data-ttu-id="b0528-189">Pro třídy knihovny - nebo konzoly aplikace poskytované hostitelem spuštění, zadejte název hostingu při spuštění sestavení `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-189">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="b0528-190">Proměnná prostředí je středníkem oddělený seznam sestavení.</span><span class="sxs-lookup"><span data-stu-id="b0528-190">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="b0528-191">Vyhledávají pouze na hostitelských při spuštění sestavení `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b0528-191">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b0528-192">Ukázkové aplikace *HostingStartupApp*, ke zjištění hostování startupy je popsáno výše, je proměnná prostředí je nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="b0528-192">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="b0528-193">Hostování sestavení po spuštění můžete nastavit také pomocí [hostování při spuštění sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b0528-193">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="b0528-194">Při hostování více spuštění sestaví jsou k dispozici, jejich [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) metody jsou provedeny v pořadí, že sestavení jsou uvedeny.</span><span class="sxs-lookup"><span data-stu-id="b0528-194">When multiple hosting startup assembles are present, their [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="b0528-195">Aktivace</span><span class="sxs-lookup"><span data-stu-id="b0528-195">Activation</span></span>

<span data-ttu-id="b0528-196">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="b0528-196">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="b0528-197">[Úložiště modulu runtime](#runtime-store) &ndash; aktivace nevyžaduje, aby kompilace odkaz pro aktivaci.</span><span class="sxs-lookup"><span data-stu-id="b0528-197">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="b0528-198">Ukázková aplikace umístí hostování sestavení po spuštění a soubory závislosti do složky, *nasazení*, které usnadní nasazování hostitelských při spuštění v prostředí multimachine.</span><span class="sxs-lookup"><span data-stu-id="b0528-198">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="b0528-199">*Nasazení* složka obsahuje také skript Powershellu, který vytvoří nebo změní proměnné prostředí pro nasazení systému k povolení spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b0528-199">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="b0528-200">Vyžadováno pro aktivaci odkazu za kompilace</span><span class="sxs-lookup"><span data-stu-id="b0528-200">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="b0528-201">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="b0528-201">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="b0528-202">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="b0528-202">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="b0528-203">Úložiště modulu runtime</span><span class="sxs-lookup"><span data-stu-id="b0528-203">Runtime store</span></span>

<span data-ttu-id="b0528-204">Hostování implementace spuštění je umístěn v [úložiště modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="b0528-204">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="b0528-205">Kompilace odkaz na sestavení není vyžadováno vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-205">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="b0528-206">Po spuštění hostování sestavení, modulu runtime úložiště je generována pomocí souboru manifestu projektu a [dotnet Restore](/dotnet/core/tools/dotnet-store) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b0528-206">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```console
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="b0528-207">V ukázkové aplikaci (*RuntimeStore* project) pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="b0528-207">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

``` console
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="b0528-208">Pro modul runtime ke zjišťování úložiště modulu runtime se umístění úložiště modulu runtime přidá do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-208">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="b0528-209">**Upravit a umístěte soubor závislosti spuštění hostování**</span><span class="sxs-lookup"><span data-stu-id="b0528-209">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="b0528-210">K aktivaci vylepšení bez odkazu na balíček ke zvýšení, zadejte další závislosti modulu runtime s `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="b0528-210">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="b0528-211">`additionalDeps` umožňuje:</span><span class="sxs-lookup"><span data-stu-id="b0528-211">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="b0528-212">Rozšíření aplikace knihovny grafu tím, že poskytuje sadu Další  *\*. deps.json* soubory sloučit s aplikace vlastní  *\*. deps.json* souboru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b0528-212">Extend the app's library graph by providing a set of additional *\*.deps.json* files to merge with the app's own *\*.deps.json* file on startup.</span></span>
* <span data-ttu-id="b0528-213">Ujistěte se, hostování sestavení po spuštění zjistitelné a načíst.</span><span class="sxs-lookup"><span data-stu-id="b0528-213">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="b0528-214">Doporučený postup pro vygenerování Další závislosti souboru je:</span><span class="sxs-lookup"><span data-stu-id="b0528-214">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="b0528-215">Spustit `dotnet publish` v souboru manifestu úložiště runtime odkazované v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="b0528-215">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="b0528-216">Odebrat odkaz na manifest z knihovny a `runtime` části výsledné  *\*deps.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="b0528-216">Remove the manifest reference from libraries and the `runtime` section of the resulting *\*deps.json* file.</span></span>

<span data-ttu-id="b0528-217">Ve vzorovém projektu `store.manifest/1.0.0` vlastnost je odebrána z `targets` a `libraries` části:</span><span class="sxs-lookup"><span data-stu-id="b0528-217">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="b0528-218">Místo  *\*. deps.json* souboru do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="b0528-218">Place the *\*.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="b0528-219">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Přidat do umístění `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-219">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="b0528-220">`{SHARED FRAMEWORK NAME}` &ndash; Sdílené framework vyžaduje pro tento soubor Další závislosti.</span><span class="sxs-lookup"><span data-stu-id="b0528-220">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="b0528-221">`{SHARED FRAMEWORK VERSION}` &ndash; Minimální sdílené verze rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b0528-221">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="b0528-222">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; Název sestavení vylepšení.</span><span class="sxs-lookup"><span data-stu-id="b0528-222">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="b0528-223">V ukázkové aplikaci (*RuntimeStore* projektu), další závislosti soubor se umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="b0528-223">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="b0528-224">Pro modul runtime ke zjištění umístění úložiště modulu runtime, je do umístění souboru Další závislosti `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-224">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="b0528-225">V ukázkové aplikaci (*RuntimeStore* projektu), vytváření úložiště modulu runtime a další závislosti, soubor se provádí pomocí generování [Powershellu](/powershell/scripting/powershell-scripting) skriptu.</span><span class="sxs-lookup"><span data-stu-id="b0528-225">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="b0528-226">Příklady toho, jak nastavit proměnné prostředí pro různé operační systémy, najdete v článku [používání více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b0528-226">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b0528-227">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="b0528-227">**Deployment**</span></span>

<span data-ttu-id="b0528-228">Pro usnadnění nasazení hostování při spuštění v prostředí multimachine, vytvoří ukázkovou aplikaci *nasazení* složky v publikované výstup, který obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b0528-228">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="b0528-229">Hostování úložiště při spuštění modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="b0528-229">The hosting startup runtime store.</span></span>
* <span data-ttu-id="b0528-230">Hostování souboru závislostí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b0528-230">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="b0528-231">Skript prostředí PowerShell, který vytvoří nebo změní `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, a `DOTNET_ADDITIONAL_DEPS` kvůli podpoře aktivace spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="b0528-231">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="b0528-232">Spusťte skript z příkazového řádku pro správu prostředí PowerShell v systému pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="b0528-232">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="b0528-233">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="b0528-233">NuGet package</span></span>

<span data-ttu-id="b0528-234">Hostování rozšíření spuštění lze zadat balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b0528-234">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="b0528-235">Balíček nemá `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="b0528-235">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="b0528-236">Hostování typům spouštění poskytovaný balíček jsou k dispozici do aplikace pomocí jedné z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="b0528-236">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="b0528-237">Soubor projektu vylepšené aplikace je odkaz na balíček pro hostování při spuštění v souboru projektu vaší aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b0528-237">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="b0528-238">Kompilace odkazem na místě, hostování při spuštění sestavení a všechny jeho závislosti jsou začleněny do závislostí souboru aplikace (*\*. deps.json*).</span><span class="sxs-lookup"><span data-stu-id="b0528-238">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="b0528-239">Vztahuje se na hostování balíček po spuštění sestavení publikovaná [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="b0528-239">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="b0528-240">Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b0528-240">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="b0528-241">Další informace o balíčcích NuGet a úložiště modulu runtime naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b0528-241">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="b0528-242">Vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="b0528-242">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="b0528-243">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="b0528-243">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="b0528-244">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="b0528-244">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="b0528-245">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="b0528-245">Project bin folder</span></span>

<span data-ttu-id="b0528-246">Hostování rozšíření po spuštění můžete zadat *bin*– nasazení sestavení do vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-246">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="b0528-247">Hostování spuštění typy poskytované sestavení jsou k dispozici do aplikace pomocí jedné z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="b0528-247">The hosting startup types provided by the assembly are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="b0528-248">Soubor projektu vylepšené aplikace je odkaz na sestavení pro spouštění hostování (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b0528-248">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="b0528-249">Kompilace odkazem na místě, hostování při spuštění sestavení a všechny jeho závislosti jsou začleněny do závislostí souboru aplikace (*\*. deps.json*).</span><span class="sxs-lookup"><span data-stu-id="b0528-249">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="b0528-250">Tento postup platí, pokud scénář nasazení volá pro přesun sestavení zkompilované hostování spuštění knihovny (DLL soubor) náročné projektu nebo do umístění přístupné náročné projektem a se kompilace odkazuje na hostování pro spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="b0528-250">This approach applies when the deployment scenario calls for moving the compiled hosting startup library's assembly (DLL file) to the consuming project or to a location accessible by the consuming project and a compile-time reference is made to the hosting startup's assembly.</span></span>
* <span data-ttu-id="b0528-251">Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b0528-251">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

## <a name="sample-code"></a><span data-ttu-id="b0528-252">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="b0528-252">Sample code</span></span>

<span data-ttu-id="b0528-253">[Ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:index#how-to-download-a-sample)) ukazuje implementaci scénáře hostingu po spuštění:</span><span class="sxs-lookup"><span data-stu-id="b0528-253">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="b0528-254">Dvě hostování při spuštění sestavení (knihoven tříd) nastavit pár konfigurace v paměti páry klíč hodnota každé:</span><span class="sxs-lookup"><span data-stu-id="b0528-254">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="b0528-255">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="b0528-255">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="b0528-256">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="b0528-256">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="b0528-257">Hostování spuštění je aktivováno z úložiště nasazení sestavení modulu runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="b0528-257">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="b0528-258">Sestavení přidá do aplikace při spuštění obsahující diagnostické informace o dvou middlewares:</span><span class="sxs-lookup"><span data-stu-id="b0528-258">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="b0528-259">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="b0528-259">Registered services</span></span>
  * <span data-ttu-id="b0528-260">Adresa (schéma, hostitele, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="b0528-260">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="b0528-261">Připojení (vzdálenou IP adresu, vzdálený port, místní IP adresu, místní port, klientského certifikátu)</span><span class="sxs-lookup"><span data-stu-id="b0528-261">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="b0528-262">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="b0528-262">Request headers</span></span>
  * <span data-ttu-id="b0528-263">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b0528-263">Environment variables</span></span>

<span data-ttu-id="b0528-264">Ke spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="b0528-264">To run the sample:</span></span>

<span data-ttu-id="b0528-265">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="b0528-265">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="b0528-266">Kompilace *HostingStartupPackage* balíček s [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b0528-266">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="b0528-267">Přidat název sestavení daného balíčku *HostingStartupPackage* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-267">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b0528-268">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-268">Compile and run the app.</span></span> <span data-ttu-id="b0528-269">Odkaz na balíček je k dispozici vylepšené aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="b0528-269">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="b0528-270">A `<PropertyGroup>` v projektu aplikace soubor Určuje výstup projektu balíček (*... / HostingStartupPackage/bin/Debug*) jako zdroj balíčků.</span><span class="sxs-lookup"><span data-stu-id="b0528-270">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="b0528-271">To umožňuje aplikacím používat balíček bez nahrává se balíček, který má [nuget.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b0528-271">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b0528-272">Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku shodovat s hodnotami nastavenými balíček `ServiceKeyInjection.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="b0528-272">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b0528-273">Pokud provedete změny *HostingStartupPackage* projektu a ji znovu zkompilovat, zrušte zaškrtnutí políčka místní mezipaměti balíčku NuGet a zkontrolujte, že *HostingStartupApp* obdrží aktualizovaný balíček a nejsou zastaralé balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b0528-273">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="b0528-274">Pokud chcete vymazat místní mezipaměť NuGet, spusťte následující [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) příkaz:</span><span class="sxs-lookup"><span data-stu-id="b0528-274">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```console
dotnet nuget locals all --clear
```

<span data-ttu-id="b0528-275">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="b0528-275">**Activation from a class library**</span></span>

1. <span data-ttu-id="b0528-276">Kompilace *HostingStartupLibrary* knihovny tříd pomocí [dotnet sestavení](/dotnet/core/tools/dotnet-build) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b0528-276">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="b0528-277">Přidat název sestavení knihovny tříd *HostingStartupLibrary* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-277">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b0528-278">*bin*– sestavení knihovny tříd aplikace nasadíte tak, že zkopírujete *HostingStartupLibrary.dll* soubor z knihovny tříd zkompilován výstup do aplikace *bin/Debug* složky.</span><span class="sxs-lookup"><span data-stu-id="b0528-278">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="b0528-279">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-279">Compile and run the app.</span></span> <span data-ttu-id="b0528-280">`<ItemGroup>` Soubor v projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (referenční dokumentace kompilace).</span><span class="sxs-lookup"><span data-stu-id="b0528-280">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="b0528-281">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b0528-281">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="b0528-282">Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku odpovídat hodnoty nastavené v knihovně tříd `ServiceKeyInjection.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="b0528-282">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b0528-283">**Aktivace z úložiště nasazení sestavení modulu runtime**</span><span class="sxs-lookup"><span data-stu-id="b0528-283">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="b0528-284">*StartupDiagnostics* používá projekt [PowerShell](/powershell/scripting/powershell-scripting) k úpravě jeho *StartupDiagnostics.deps.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="b0528-284">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="b0528-285">Prostředí PowerShell se instaluje standardně na Windows od verze Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="b0528-285">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="b0528-286">Získat PowerShell na jiných platformách, naleznete v tématu [instalace prostředí Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span><span class="sxs-lookup"><span data-stu-id="b0528-286">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="b0528-287">Spustit *build.ps1* skript v *RuntimeStore* složky.</span><span class="sxs-lookup"><span data-stu-id="b0528-287">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="b0528-288">Tento skript:</span><span class="sxs-lookup"><span data-stu-id="b0528-288">The script:</span></span>
   * <span data-ttu-id="b0528-289">Generuje `StartupDiagnostics` balíčku.</span><span class="sxs-lookup"><span data-stu-id="b0528-289">Generates the `StartupDiagnostics` package.</span></span>
   * <span data-ttu-id="b0528-290">Generuje runtime úložiště pro `StartupDiagnostics` v *ukládání* složky.</span><span class="sxs-lookup"><span data-stu-id="b0528-290">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="b0528-291">`dotnet store` Používá příkaz ve skriptu `win7-x64` [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog) pro hostování spuštění nasazené do Windows.</span><span class="sxs-lookup"><span data-stu-id="b0528-291">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="b0528-292">Při poskytování hostitelských po spuštění pro jiný modul runtime, nahraďte správné identifikátorů RID na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="b0528-292">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span>
   * <span data-ttu-id="b0528-293">Generuje `additionalDeps` pro `StartupDiagnostics` v *additionalDeps/shared/Microsoft.AspNetCore.App/{Shared verzi rozhraní Framework} /* složky.</span><span class="sxs-lookup"><span data-stu-id="b0528-293">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps/shared/Microsoft.AspNetCore.App/{Shared Framework Version}/* folder.</span></span>
   * <span data-ttu-id="b0528-294">Místa *deploy.ps1* soubor *nasazení* složky.</span><span class="sxs-lookup"><span data-stu-id="b0528-294">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="b0528-295">Spustit *deploy.ps1* skript v *nasazení* složky.</span><span class="sxs-lookup"><span data-stu-id="b0528-295">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="b0528-296">Tento skript připojí:</span><span class="sxs-lookup"><span data-stu-id="b0528-296">The script appends:</span></span>
   * <span data-ttu-id="b0528-297">`StartupDiagnostics` Chcete `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-297">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="b0528-298">Hostování spouštěcí závislosti cesty k `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-298">The hosting startup dependencies path to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="b0528-299">Cesta k úložišti runtime k `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0528-299">The runtime store path to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="b0528-300">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b0528-300">Run the sample app.</span></span>
1. <span data-ttu-id="b0528-301">Žádosti `/services` koncový bod můžete zobrazit aplikace registrované služby.</span><span class="sxs-lookup"><span data-stu-id="b0528-301">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="b0528-302">Žádosti `/diag` koncový bod můžete zobrazit diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="b0528-302">Request the `/diag` endpoint to see the diagnostic information.</span></span>
