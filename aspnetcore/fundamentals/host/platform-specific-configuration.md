---
<span data-ttu-id="f75c1-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f75c1-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f75c1-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f75c1-102">'Blazor'</span></span>
- <span data-ttu-id="f75c1-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f75c1-103">'Identity'</span></span>
- <span data-ttu-id="f75c1-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f75c1-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f75c1-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f75c1-105">'Razor'</span></span>
- <span data-ttu-id="f75c1-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f75c1-106">'SignalR' uid:</span></span> 

---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="f75c1-107">Použití hostování spouštěcích sestavení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f75c1-107">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="f75c1-108">Od [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="f75c1-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f75c1-109"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="f75c1-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="f75c1-110">Externí knihovna může například použít hostitelskou implementaci při spuštění k poskytnutí dalších poskytovatelů konfigurace nebo služeb do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="f75c1-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f75c1-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="f75c1-112">HostingStartup – atribut</span><span class="sxs-lookup"><span data-stu-id="f75c1-112">HostingStartup attribute</span></span>

<span data-ttu-id="f75c1-113">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="f75c1-114">Pro zadání sestavení nebo sestavení obsahujícího `Startup` třídu se automaticky vyhledá `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f75c1-115">Seznam sestavení pro vyhledávání `HostingStartup` atributů je načten za běhu z konfigurace ve [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f75c1-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="f75c1-116">Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f75c1-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="f75c1-117">V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="f75c1-118">Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="f75c1-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f75c1-119">`HostingStartup`Atribut je obvykle umístěn v souboru implementační třídy sestavení, který spouští `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="f75c1-120">Najít načtená hostování spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="f75c1-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="f75c1-121">Chcete-li zjistit načtená hostování spouštěcích sestavení, povolte protokolování a ověřte protokoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="f75c1-122">Chyby, ke kterým dochází při načítání sestavení, jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="f75c1-122">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="f75c1-123">Načtená hostující spouštěcí sestavení jsou protokolována na úrovni ladění a všechny chyby jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="f75c1-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="f75c1-124">Zakázat automatické načítání hostujících spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="f75c1-124">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="f75c1-125">Chcete-li zakázat automatické načítání hostujících spouštěcích sestavení, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f75c1-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="f75c1-126">Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1` :</span><span class="sxs-lookup"><span data-stu-id="f75c1-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="f75c1-127">Zabránit hostování nastavení konfigurace hostitele po spuštění:</span><span class="sxs-lookup"><span data-stu-id="f75c1-127">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="f75c1-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="f75c1-129">Chcete-li zabránit tomu, aby se konkrétní hostování spouštěcích sestavení načetlo, nastavte jednu z následujících hodnot na řetězec, který je hostitelem spouštěcích sestavení s oddělovači, aby při spuštění vyloučil:</span><span class="sxs-lookup"><span data-stu-id="f75c1-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="f75c1-130">Hostování nastavení konfigurace vyloučení sestavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="f75c1-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="f75c1-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="f75c1-132">Pokud je nastavené nastavení konfigurace hostitele i proměnná prostředí, řídí se chování nastavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="f75c1-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="f75c1-133">Zakázání hostování spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="f75c1-134">Project</span><span class="sxs-lookup"><span data-stu-id="f75c1-134">Project</span></span>

<span data-ttu-id="f75c1-135">Vytvořte hostování po spuštění s některým z následujících typů projektů:</span><span class="sxs-lookup"><span data-stu-id="f75c1-135">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="f75c1-136">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="f75c1-136">Class library</span></span>](#class-library)
* [<span data-ttu-id="f75c1-137">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="f75c1-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="f75c1-138">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="f75c1-138">Class library</span></span>

<span data-ttu-id="f75c1-139">Rozšíření spouštění hostitele lze poskytnout v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="f75c1-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="f75c1-140">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-140">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f75c1-141">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor stránku aplikace, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="f75c1-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="f75c1-142">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="f75c1-142">The class library:</span></span>

* <span data-ttu-id="f75c1-143">Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f75c1-144">`ServiceKeyInjection`Přidá dvojici řetězců služby k konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="f75c1-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="f75c1-145">Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu pro spuštění hostingu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="f75c1-146">`ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="f75c1-147">*HostingStartupLibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f75c1-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f75c1-148">Stránka indexu aplikace načte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostujícím sestavením pro spuštění knihovny tříd:</span><span class="sxs-lookup"><span data-stu-id="f75c1-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="f75c1-149">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f75c1-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="f75c1-150">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="f75c1-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="f75c1-151">Balíček má stejné charakteristiky jako knihovna tříd popsané výše.</span><span class="sxs-lookup"><span data-stu-id="f75c1-151">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="f75c1-152">Balíček:</span><span class="sxs-lookup"><span data-stu-id="f75c1-152">The package:</span></span>

* <span data-ttu-id="f75c1-153">Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f75c1-154">`ServiceKeyInjection`Přidá do konfigurace aplikace dvojici řetězců služby.</span><span class="sxs-lookup"><span data-stu-id="f75c1-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="f75c1-155">Obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f75c1-156">*HostingStartupPackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f75c1-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f75c1-157">Stránka indexu aplikace čte a vykresluje konfigurační hodnoty pro dva klíče nastavené hostujícím sestavením pro spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="f75c1-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="f75c1-158">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f75c1-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="f75c1-159">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="f75c1-159">Console app without an entry point</span></span>

<span data-ttu-id="f75c1-160">*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="f75c1-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="f75c1-161">Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="f75c1-162">Publikování konzolové aplikace vytváří hostující spouštěcí sestavení, které lze spotřebovat z běhového úložiště.</span><span class="sxs-lookup"><span data-stu-id="f75c1-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="f75c1-163">V tomto procesu se používá Konzolová aplikace bez vstupního bodu, protože:</span><span class="sxs-lookup"><span data-stu-id="f75c1-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="f75c1-164">Soubor závislostí je vyžadován ke využívání hostování při spouštění v sestavení, ve kterém se spouští.</span><span class="sxs-lookup"><span data-stu-id="f75c1-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="f75c1-165">Soubor závislostí je spustitelný App Asset, který je vytvořený publikováním aplikace, ne knihovny.</span><span class="sxs-lookup"><span data-stu-id="f75c1-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="f75c1-166">Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="f75c1-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="f75c1-167">Při vytváření dynamického spuštění hostování:</span><span class="sxs-lookup"><span data-stu-id="f75c1-167">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="f75c1-168">Hostující spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:</span><span class="sxs-lookup"><span data-stu-id="f75c1-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="f75c1-169">Obsahuje třídu, která obsahuje `IHostingStartup` implementaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="f75c1-170">Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pro identifikaci `IHostingStartup` implementační třídy.</span><span class="sxs-lookup"><span data-stu-id="f75c1-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="f75c1-171">Konzolová aplikace se publikuje, aby získala závislosti hostování při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-171">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="f75c1-172">V důsledku publikování konzolové aplikace je, že nevyužité závislosti se oříznou ze souboru závislostí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="f75c1-173">Soubor závislostí je upraven, aby bylo možné nastavit umístění modulu runtime hostujícího spouštěcí sestavení.</span><span class="sxs-lookup"><span data-stu-id="f75c1-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="f75c1-174">Hostování spouštěcího sestavení a jeho souboru závislostí je umístěno do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="f75c1-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="f75c1-175">Chcete-li zjistit hostující sestavení po spuštění a jeho soubor závislosti, jsou uvedeny ve dvojici proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="f75c1-176">Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="f75c1-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="f75c1-177">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="f75c1-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="f75c1-178">V následujícím příkladu je obor názvů `StartupEnhancement` a třída `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="f75c1-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f75c1-179">Třída implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="f75c1-180"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="f75c1-181">`IHostingStartup.Configure`ve spouštěcím sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, což umožňuje přepsání libovolné konfigurace poskytované hostováním sestavení po spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="f75c1-182">Při sestavování `IHostingStartup` projektu, soubor závislosti (*. DEPS. JSON*) nastaví `runtime` umístění sestavení do složky *bin* :</span><span class="sxs-lookup"><span data-stu-id="f75c1-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="f75c1-183">Zobrazí se pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="f75c1-183">Only part of the file is shown.</span></span> <span data-ttu-id="f75c1-184">Název sestavení v příkladu je `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="f75c1-185">Konfigurace poskytovaná spuštěním hostování</span><span class="sxs-lookup"><span data-stu-id="f75c1-185">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="f75c1-186">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="f75c1-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="f75c1-187">Poskytněte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> , aby se konfigurace po spuštění delegátů aplikace načetla <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="f75c1-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f75c1-188">Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="f75c1-189">Zadejte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> , aby se konfigurace načetla předtím, než se <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> spustí Delegáti aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f75c1-190">Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="f75c1-191">Zadejte hostující sestavení pro spuštění</span><span class="sxs-lookup"><span data-stu-id="f75c1-191">Specify the hosting startup assembly</span></span>

<span data-ttu-id="f75c1-192">V případě knihovny tříd nebo konzolové aplikace dodané po spuštění zadejte název hostujícího spouštěcího sestavení v `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="f75c1-193">Proměnná prostředí je seznam sestavení oddělených středníky.</span><span class="sxs-lookup"><span data-stu-id="f75c1-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="f75c1-194">Pro atribut jsou kontrolována pouze spouštěcí sestavení hostování `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f75c1-195">Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="f75c1-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="f75c1-196">Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostování po spouštěcích sestaveních hostitele:</span><span class="sxs-lookup"><span data-stu-id="f75c1-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="f75c1-197">Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.</span><span class="sxs-lookup"><span data-stu-id="f75c1-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="f75c1-198">Aktivace</span><span class="sxs-lookup"><span data-stu-id="f75c1-198">Activation</span></span>

<span data-ttu-id="f75c1-199">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="f75c1-199">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="f75c1-200">[Běhové úložiště](#runtime-store): Aktivace nevyžaduje pro aktivaci odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="f75c1-201">Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači.</span><span class="sxs-lookup"><span data-stu-id="f75c1-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="f75c1-202">Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="f75c1-203">Pro aktivaci se vyžaduje odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-203">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="f75c1-204">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="f75c1-204">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="f75c1-205">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="f75c1-205">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="f75c1-206">Běhové úložiště</span><span class="sxs-lookup"><span data-stu-id="f75c1-206">Runtime store</span></span>

<span data-ttu-id="f75c1-207">Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="f75c1-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="f75c1-208">Rozšířená aplikace nevyžaduje odkaz na sestavení v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="f75c1-209">Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="f75c1-210">V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f75c1-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="f75c1-211">Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="f75c1-212">**Upravit a umístit soubor závislostí se spouštěním hostitele**</span><span class="sxs-lookup"><span data-stu-id="f75c1-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="f75c1-213">Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="f75c1-214">`additionalDeps`umožňuje:</span><span class="sxs-lookup"><span data-stu-id="f75c1-214">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="f75c1-215">Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších souborů *. DEPS. JSON* pro sloučení s vlastním souborem *. DEPS. JSON* aplikace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="f75c1-216">Zpřístupněte hostující spouštěcí sestavení, které je zjistitelné a spustitelný.</span><span class="sxs-lookup"><span data-stu-id="f75c1-216">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="f75c1-217">Doporučený postup pro generování dalších souborů závislostí:</span><span class="sxs-lookup"><span data-stu-id="f75c1-217">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="f75c1-218">Spustit `dotnet publish` v souboru manifestu úložiště modulu runtime odkazovaného v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="f75c1-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="f75c1-219">Odeberte odkaz na manifest z knihoven a `runtime` část výsledného souboru *. DEPS. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="f75c1-220">V příkladu projektu `store.manifest/1.0.0` je vlastnost odebrána z `targets` `libraries` oddílu a:</span><span class="sxs-lookup"><span data-stu-id="f75c1-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="f75c1-221">Soubor *. DEPS. JSON* umístěte do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="f75c1-221">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="f75c1-222">`{ADDITIONAL DEPENDENCIES PATH}`: Umístění bylo přidáno do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="f75c1-223">`{SHARED FRAMEWORK NAME}`: Sdílené rozhraní požadované pro tento další soubor závislostí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="f75c1-224">`{SHARED FRAMEWORK VERSION}`: Minimální verze sdíleného rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f75c1-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="f75c1-225">`{ENHANCEMENT ASSEMBLY NAME}`: Název sestavení rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f75c1-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="f75c1-226">V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="f75c1-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="f75c1-227">Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí přidáno umístění souboru další závislosti `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="f75c1-228">V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="f75c1-229">Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f75c1-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f75c1-230">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="f75c1-230">**Deployment**</span></span>

<span data-ttu-id="f75c1-231">Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:</span><span class="sxs-lookup"><span data-stu-id="f75c1-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="f75c1-232">Hostování běhového úložiště při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-232">The hosting startup runtime store.</span></span>
* <span data-ttu-id="f75c1-233">Hostující soubor závislostí po spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-233">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="f75c1-234">Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` , `DOTNET_SHARED_STORE` a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="f75c1-235">Spusťte skript z příkazového řádku PowerShellu pro správu v systému nasazení.</span><span class="sxs-lookup"><span data-stu-id="f75c1-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="f75c1-236">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="f75c1-236">NuGet package</span></span>

<span data-ttu-id="f75c1-237">Rozšíření spouštění hostitele lze poskytnout v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="f75c1-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="f75c1-238">Balíček má `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-238">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="f75c1-239">Typy spuštění hostování poskytované balíčkem jsou k dispozici v aplikaci pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f75c1-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="f75c1-240">Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spouštěné v souboru projektu aplikace (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f75c1-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="f75c1-241">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace (*. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="f75c1-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f75c1-242">Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="f75c1-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="f75c1-243">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="f75c1-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="f75c1-244">Další informace o balíčcích NuGet a úložišti modulu runtime najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f75c1-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="f75c1-245">Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="f75c1-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="f75c1-246">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="f75c1-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="f75c1-247">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="f75c1-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="f75c1-248">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="f75c1-248">Project bin folder</span></span>

<span data-ttu-id="f75c1-249">Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="f75c1-250">Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f75c1-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="f75c1-251">Soubor projektu rozšířené aplikace vytvoří odkaz na sestavení pro spuštění hostování (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f75c1-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="f75c1-252">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace (*. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="f75c1-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f75c1-253">Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="f75c1-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="f75c1-254">Projekt, který je k disnáročné.</span><span class="sxs-lookup"><span data-stu-id="f75c1-254">The consuming project.</span></span>
  * <span data-ttu-id="f75c1-255">Umístění přístupné pro náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="f75c1-255">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="f75c1-256">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="f75c1-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="f75c1-257">Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="f75c1-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="f75c1-258">Základní cesta aplikace: složka *bin* , kde se nachází spustitelný soubor aplikace (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="f75c1-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="f75c1-259">Globální mezipaměť sestavení (GAC): GAC ukládá sestavení, která sdílí několik aplikací .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f75c1-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="f75c1-260">Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f75c1-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="f75c1-261">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="f75c1-261">Sample code</span></span>

<span data-ttu-id="f75c1-262">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:</span><span class="sxs-lookup"><span data-stu-id="f75c1-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="f75c1-263">Dvě hostování spouštěcích sestavení (knihovny tříd) nastaví dvojici dvojic klíč-hodnota klíče pro konfiguraci v paměti každý:</span><span class="sxs-lookup"><span data-stu-id="f75c1-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="f75c1-264">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="f75c1-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="f75c1-265">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="f75c1-265">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="f75c1-266">Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="f75c1-267">Sestavení přidá do aplikace dvě middlewary při spuštění, které poskytují diagnostické informace o:</span><span class="sxs-lookup"><span data-stu-id="f75c1-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="f75c1-268">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="f75c1-268">Registered services</span></span>
  * <span data-ttu-id="f75c1-269">Adresa (schéma, hostitel, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="f75c1-269">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="f75c1-270">Připojení (Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát)</span><span class="sxs-lookup"><span data-stu-id="f75c1-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="f75c1-271">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="f75c1-271">Request headers</span></span>
  * <span data-ttu-id="f75c1-272">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="f75c1-272">Environment variables</span></span>

<span data-ttu-id="f75c1-273">Spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="f75c1-273">To run the sample:</span></span>

<span data-ttu-id="f75c1-274">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="f75c1-274">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="f75c1-275">Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="f75c1-276">Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f75c1-277">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-277">Compile and run the app.</span></span> <span data-ttu-id="f75c1-278">Odkaz na balíček se nachází v rozšířené aplikaci (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f75c1-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="f75c1-279">`<PropertyGroup>`V souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku.</span><span class="sxs-lookup"><span data-stu-id="f75c1-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="f75c1-280">To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f75c1-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f75c1-281">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou balíčku `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f75c1-282">Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f75c1-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="f75c1-283">K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :</span><span class="sxs-lookup"><span data-stu-id="f75c1-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="f75c1-284">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="f75c1-284">**Activation from a class library**</span></span>

1. <span data-ttu-id="f75c1-285">Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="f75c1-286">Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f75c1-287">*bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary. dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="f75c1-288">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-288">Compile and run the app.</span></span> <span data-ttu-id="f75c1-289">`<ItemGroup>`V souboru projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f75c1-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="f75c1-290">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f75c1-290">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="f75c1-291">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou knihovny tříd `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f75c1-292">**Aktivace z běhového úložiště – nasazené sestavení**</span><span class="sxs-lookup"><span data-stu-id="f75c1-292">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="f75c1-293">Projekt *StartupDiagnostics* používá k úpravě souboru *StartupDiagnostics. DEPS. JSON* [PowerShell](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="f75c1-294">PowerShell je ve výchozím nastavení nainstalován v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="f75c1-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="f75c1-295">Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace různých verzí PowerShellu](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="f75c1-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="f75c1-296">Spusťte skript *Build. ps1* ve složce *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="f75c1-297">Skript:</span><span class="sxs-lookup"><span data-stu-id="f75c1-297">The script:</span></span>
   * <span data-ttu-id="f75c1-298">Vygeneruje `StartupDiagnostics` balíček ve složce *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="f75c1-299">Vygeneruje běhové úložiště pro `StartupDiagnostics` ve složce *Store* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="f75c1-300">`dotnet store`Příkaz ve skriptu používá `win7-x64` [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows.</span><span class="sxs-lookup"><span data-stu-id="f75c1-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="f75c1-301">Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="f75c1-302">Běhové úložiště pro `StartupDiagnostics` by bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno.</span><span class="sxs-lookup"><span data-stu-id="f75c1-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="f75c1-303">Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="f75c1-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="f75c1-304">Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="f75c1-305">Další závislosti by se později přesunuly na další závislosti uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="f75c1-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="f75c1-306">Umístění pro `StartupDiagnostics` instalaci dalších závislostí uživatele je *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/3.0.0/StartupDiagnostics. DEPS. JSON*.</span><span class="sxs-lookup"><span data-stu-id="f75c1-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="f75c1-307">Umístí soubor *Deploy. ps1* do složky pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="f75c1-308">Spusťte skript *Deploy. ps1* ve složce pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="f75c1-309">Skript připojí:</span><span class="sxs-lookup"><span data-stu-id="f75c1-309">The script appends:</span></span>
   * <span data-ttu-id="f75c1-310">`StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="f75c1-311">Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="f75c1-312">Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="f75c1-313">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-313">Run the sample app.</span></span>
1. <span data-ttu-id="f75c1-314">Požádejte o `/services` koncový bod, aby se zobrazily registrované služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-314">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="f75c1-315">Požádejte `/diag` koncový bod, aby zobrazil diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f75c1-316"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="f75c1-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="f75c1-317">Externí knihovna může například použít hostitelskou implementaci při spuštění k poskytnutí dalších poskytovatelů konfigurace nebo služeb do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="f75c1-318">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f75c1-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="f75c1-319">HostingStartup – atribut</span><span class="sxs-lookup"><span data-stu-id="f75c1-319">HostingStartup attribute</span></span>

<span data-ttu-id="f75c1-320">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="f75c1-321">Pro zadání sestavení nebo sestavení obsahujícího `Startup` třídu se automaticky vyhledá `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f75c1-322">Seznam sestavení pro vyhledávání `HostingStartup` atributů je načten za běhu z konfigurace ve [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f75c1-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="f75c1-323">Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f75c1-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="f75c1-324">Další informace najdete v tématu [webový hostitel: hostování spouštěcích sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [webového hostitele: hostování spouštěcích vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="f75c1-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="f75c1-325">V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="f75c1-326">Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="f75c1-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f75c1-327">`HostingStartup`Atribut je obvykle umístěn v souboru implementační třídy sestavení, který spouští `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="f75c1-328">Najít načtená hostování spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="f75c1-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="f75c1-329">Chcete-li zjistit načtená hostování spouštěcích sestavení, povolte protokolování a ověřte protokoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="f75c1-330">Chyby, ke kterým dochází při načítání sestavení, jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="f75c1-330">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="f75c1-331">Načtená hostující spouštěcí sestavení jsou protokolována na úrovni ladění a všechny chyby jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="f75c1-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="f75c1-332">Zakázat automatické načítání hostujících spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="f75c1-332">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="f75c1-333">Chcete-li zakázat automatické načítání hostujících spouštěcích sestavení, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f75c1-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="f75c1-334">Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1` :</span><span class="sxs-lookup"><span data-stu-id="f75c1-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="f75c1-335">Zabrání hostování nastavení konfigurace hostitele [po spuštění](xref:fundamentals/host/web-host#prevent-hosting-startup) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="f75c1-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="f75c1-337">Chcete-li zabránit tomu, aby se konkrétní hostování spouštěcích sestavení načetlo, nastavte jednu z následujících hodnot na řetězec, který je hostitelem spouštěcích sestavení s oddělovači, aby při spuštění vyloučil:</span><span class="sxs-lookup"><span data-stu-id="f75c1-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="f75c1-338">Hostování nastavení konfigurace hostitele [vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)</span><span class="sxs-lookup"><span data-stu-id="f75c1-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="f75c1-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="f75c1-340">Pokud je nastavené nastavení konfigurace hostitele i proměnná prostředí, řídí se chování nastavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="f75c1-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="f75c1-341">Zakázání hostování spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="f75c1-342">Project</span><span class="sxs-lookup"><span data-stu-id="f75c1-342">Project</span></span>

<span data-ttu-id="f75c1-343">Vytvořte hostování po spuštění s některým z následujících typů projektů:</span><span class="sxs-lookup"><span data-stu-id="f75c1-343">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="f75c1-344">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="f75c1-344">Class library</span></span>](#class-library)
* [<span data-ttu-id="f75c1-345">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="f75c1-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="f75c1-346">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="f75c1-346">Class library</span></span>

<span data-ttu-id="f75c1-347">Rozšíření spouštění hostitele lze poskytnout v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="f75c1-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="f75c1-348">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-348">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f75c1-349">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor stránku aplikace, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="f75c1-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="f75c1-350">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="f75c1-350">The class library:</span></span>

* <span data-ttu-id="f75c1-351">Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f75c1-352">`ServiceKeyInjection`Přidá dvojici řetězců služby k konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="f75c1-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="f75c1-353">Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu pro spuštění hostingu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="f75c1-354">`ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="f75c1-355">*HostingStartupLibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f75c1-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f75c1-356">Stránka indexu aplikace načte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostujícím sestavením pro spuštění knihovny tříd:</span><span class="sxs-lookup"><span data-stu-id="f75c1-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="f75c1-357">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f75c1-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="f75c1-358">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="f75c1-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="f75c1-359">Balíček má stejné charakteristiky jako knihovna tříd popsané výše.</span><span class="sxs-lookup"><span data-stu-id="f75c1-359">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="f75c1-360">Balíček:</span><span class="sxs-lookup"><span data-stu-id="f75c1-360">The package:</span></span>

* <span data-ttu-id="f75c1-361">Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f75c1-362">`ServiceKeyInjection`Přidá do konfigurace aplikace dvojici řetězců služby.</span><span class="sxs-lookup"><span data-stu-id="f75c1-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="f75c1-363">Obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f75c1-364">*HostingStartupPackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f75c1-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f75c1-365">Stránka indexu aplikace čte a vykresluje konfigurační hodnoty pro dva klíče nastavené hostujícím sestavením pro spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="f75c1-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="f75c1-366">*HostingStartupApp/pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f75c1-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="f75c1-367">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="f75c1-367">Console app without an entry point</span></span>

<span data-ttu-id="f75c1-368">*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="f75c1-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="f75c1-369">Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="f75c1-370">Publikování konzolové aplikace vytváří hostující spouštěcí sestavení, které lze spotřebovat z běhového úložiště.</span><span class="sxs-lookup"><span data-stu-id="f75c1-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="f75c1-371">V tomto procesu se používá Konzolová aplikace bez vstupního bodu, protože:</span><span class="sxs-lookup"><span data-stu-id="f75c1-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="f75c1-372">Soubor závislostí je vyžadován ke využívání hostování při spouštění v sestavení, ve kterém se spouští.</span><span class="sxs-lookup"><span data-stu-id="f75c1-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="f75c1-373">Soubor závislostí je spustitelný App Asset, který je vytvořený publikováním aplikace, ne knihovny.</span><span class="sxs-lookup"><span data-stu-id="f75c1-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="f75c1-374">Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="f75c1-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="f75c1-375">Při vytváření dynamického spuštění hostování:</span><span class="sxs-lookup"><span data-stu-id="f75c1-375">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="f75c1-376">Hostující spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:</span><span class="sxs-lookup"><span data-stu-id="f75c1-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="f75c1-377">Obsahuje třídu, která obsahuje `IHostingStartup` implementaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="f75c1-378">Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pro identifikaci `IHostingStartup` implementační třídy.</span><span class="sxs-lookup"><span data-stu-id="f75c1-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="f75c1-379">Konzolová aplikace se publikuje, aby získala závislosti hostování při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-379">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="f75c1-380">V důsledku publikování konzolové aplikace je, že nevyužité závislosti se oříznou ze souboru závislostí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="f75c1-381">Soubor závislostí je upraven, aby bylo možné nastavit umístění modulu runtime hostujícího spouštěcí sestavení.</span><span class="sxs-lookup"><span data-stu-id="f75c1-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="f75c1-382">Hostování spouštěcího sestavení a jeho souboru závislostí je umístěno do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="f75c1-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="f75c1-383">Chcete-li zjistit hostující sestavení po spuštění a jeho soubor závislosti, jsou uvedeny ve dvojici proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="f75c1-384">Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="f75c1-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="f75c1-385">Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="f75c1-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="f75c1-386">V následujícím příkladu je obor názvů `StartupEnhancement` a třída `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="f75c1-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f75c1-387">Třída implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="f75c1-388"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="f75c1-389">`IHostingStartup.Configure`ve spouštěcím sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, což umožňuje přepsání libovolné konfigurace poskytované hostováním sestavení po spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="f75c1-390">Při sestavování `IHostingStartup` projektu, soubor závislosti (*. DEPS. JSON*) nastaví `runtime` umístění sestavení do složky *bin* :</span><span class="sxs-lookup"><span data-stu-id="f75c1-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="f75c1-391">Zobrazí se pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="f75c1-391">Only part of the file is shown.</span></span> <span data-ttu-id="f75c1-392">Název sestavení v příkladu je `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-392">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="f75c1-393">Konfigurace poskytovaná spuštěním hostování</span><span class="sxs-lookup"><span data-stu-id="f75c1-393">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="f75c1-394">Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="f75c1-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="f75c1-395">Poskytněte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> , aby se konfigurace po spuštění delegátů aplikace načetla <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="f75c1-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f75c1-396">Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="f75c1-397">Zadejte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> , aby se konfigurace načetla předtím, než se <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> spustí Delegáti aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f75c1-398">Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="f75c1-399">Zadejte hostující sestavení pro spuštění</span><span class="sxs-lookup"><span data-stu-id="f75c1-399">Specify the hosting startup assembly</span></span>

<span data-ttu-id="f75c1-400">V případě knihovny tříd nebo konzolové aplikace dodané po spuštění zadejte název hostujícího spouštěcího sestavení v `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="f75c1-401">Proměnná prostředí je seznam sestavení oddělených středníky.</span><span class="sxs-lookup"><span data-stu-id="f75c1-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="f75c1-402">Pro atribut jsou kontrolována pouze spouštěcí sestavení hostování `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f75c1-403">Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="f75c1-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="f75c1-404">Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostitele [po spouštěcích sestaveních](xref:fundamentals/host/web-host#hosting-startup-assemblies) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="f75c1-405">Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.</span><span class="sxs-lookup"><span data-stu-id="f75c1-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="f75c1-406">Aktivace</span><span class="sxs-lookup"><span data-stu-id="f75c1-406">Activation</span></span>

<span data-ttu-id="f75c1-407">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="f75c1-407">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="f75c1-408">[Běhové úložiště](#runtime-store): Aktivace nevyžaduje pro aktivaci odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="f75c1-409">Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači.</span><span class="sxs-lookup"><span data-stu-id="f75c1-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="f75c1-410">Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="f75c1-411">Pro aktivaci se vyžaduje odkaz na čas kompilace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-411">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="f75c1-412">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="f75c1-412">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="f75c1-413">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="f75c1-413">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="f75c1-414">Běhové úložiště</span><span class="sxs-lookup"><span data-stu-id="f75c1-414">Runtime store</span></span>

<span data-ttu-id="f75c1-415">Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="f75c1-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="f75c1-416">Rozšířená aplikace nevyžaduje odkaz na sestavení v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="f75c1-417">Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="f75c1-418">V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f75c1-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="f75c1-419">Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="f75c1-420">**Upravit a umístit soubor závislostí se spouštěním hostitele**</span><span class="sxs-lookup"><span data-stu-id="f75c1-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="f75c1-421">Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="f75c1-422">`additionalDeps`umožňuje:</span><span class="sxs-lookup"><span data-stu-id="f75c1-422">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="f75c1-423">Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších souborů *. DEPS. JSON* pro sloučení s vlastním souborem *. DEPS. JSON* aplikace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="f75c1-424">Zpřístupněte hostující spouštěcí sestavení, které je zjistitelné a spustitelný.</span><span class="sxs-lookup"><span data-stu-id="f75c1-424">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="f75c1-425">Doporučený postup pro generování dalších souborů závislostí:</span><span class="sxs-lookup"><span data-stu-id="f75c1-425">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="f75c1-426">Spustit `dotnet publish` v souboru manifestu úložiště modulu runtime odkazovaného v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="f75c1-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="f75c1-427">Odeberte odkaz na manifest z knihoven a `runtime` část výsledného souboru *. DEPS. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="f75c1-428">V příkladu projektu `store.manifest/1.0.0` je vlastnost odebrána z `targets` `libraries` oddílu a:</span><span class="sxs-lookup"><span data-stu-id="f75c1-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="f75c1-429">Soubor *. DEPS. JSON* umístěte do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="f75c1-429">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="f75c1-430">`{ADDITIONAL DEPENDENCIES PATH}`: Umístění bylo přidáno do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="f75c1-431">`{SHARED FRAMEWORK NAME}`: Sdílené rozhraní požadované pro tento další soubor závislostí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="f75c1-432">`{SHARED FRAMEWORK VERSION}`: Minimální verze sdíleného rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f75c1-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="f75c1-433">`{ENHANCEMENT ASSEMBLY NAME}`: Název sestavení rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f75c1-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="f75c1-434">V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:</span><span class="sxs-lookup"><span data-stu-id="f75c1-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="f75c1-435">Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí přidáno umístění souboru další závislosti `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="f75c1-436">V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="f75c1-437">Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f75c1-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f75c1-438">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="f75c1-438">**Deployment**</span></span>

<span data-ttu-id="f75c1-439">Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:</span><span class="sxs-lookup"><span data-stu-id="f75c1-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="f75c1-440">Hostování běhového úložiště při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-440">The hosting startup runtime store.</span></span>
* <span data-ttu-id="f75c1-441">Hostující soubor závislostí po spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-441">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="f75c1-442">Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` , `DOTNET_SHARED_STORE` a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění.</span><span class="sxs-lookup"><span data-stu-id="f75c1-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="f75c1-443">Spusťte skript z příkazového řádku PowerShellu pro správu v systému nasazení.</span><span class="sxs-lookup"><span data-stu-id="f75c1-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="f75c1-444">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="f75c1-444">NuGet package</span></span>

<span data-ttu-id="f75c1-445">Rozšíření spouštění hostitele lze poskytnout v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="f75c1-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="f75c1-446">Balíček má `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="f75c1-446">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="f75c1-447">Typy spuštění hostování poskytované balíčkem jsou k dispozici v aplikaci pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f75c1-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="f75c1-448">Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spouštěné v souboru projektu aplikace (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f75c1-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="f75c1-449">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace (*. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="f75c1-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f75c1-450">Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="f75c1-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="f75c1-451">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="f75c1-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="f75c1-452">Další informace o balíčcích NuGet a úložišti modulu runtime najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f75c1-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="f75c1-453">Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="f75c1-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="f75c1-454">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="f75c1-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="f75c1-455">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="f75c1-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="f75c1-456">Složka Bin projektu</span><span class="sxs-lookup"><span data-stu-id="f75c1-456">Project bin folder</span></span>

<span data-ttu-id="f75c1-457">Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="f75c1-458">Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f75c1-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="f75c1-459">Soubor projektu rozšířené aplikace vytvoří odkaz na sestavení pro spuštění hostování (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f75c1-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="f75c1-460">V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace (*. DEPS. JSON*).</span><span class="sxs-lookup"><span data-stu-id="f75c1-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f75c1-461">Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="f75c1-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="f75c1-462">Projekt, který je k disnáročné.</span><span class="sxs-lookup"><span data-stu-id="f75c1-462">The consuming project.</span></span>
  * <span data-ttu-id="f75c1-463">Umístění přístupné pro náročný projekt.</span><span class="sxs-lookup"><span data-stu-id="f75c1-463">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="f75c1-464">K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).</span><span class="sxs-lookup"><span data-stu-id="f75c1-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="f75c1-465">Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="f75c1-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="f75c1-466">Základní cesta aplikace: složka *bin* , kde se nachází spustitelný soubor aplikace (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="f75c1-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="f75c1-467">Globální mezipaměť sestavení (GAC): GAC ukládá sestavení, která sdílí několik aplikací .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f75c1-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="f75c1-468">Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f75c1-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="f75c1-469">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="f75c1-469">Sample code</span></span>

<span data-ttu-id="f75c1-470">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:</span><span class="sxs-lookup"><span data-stu-id="f75c1-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="f75c1-471">Dvě hostování spouštěcích sestavení (knihovny tříd) nastaví dvojici dvojic klíč-hodnota klíče pro konfiguraci v paměti každý:</span><span class="sxs-lookup"><span data-stu-id="f75c1-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="f75c1-472">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="f75c1-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="f75c1-473">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="f75c1-473">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="f75c1-474">Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="f75c1-475">Sestavení přidá do aplikace dvě middlewary při spuštění, které poskytují diagnostické informace o:</span><span class="sxs-lookup"><span data-stu-id="f75c1-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="f75c1-476">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="f75c1-476">Registered services</span></span>
  * <span data-ttu-id="f75c1-477">Adresa (schéma, hostitel, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="f75c1-477">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="f75c1-478">Připojení (Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát)</span><span class="sxs-lookup"><span data-stu-id="f75c1-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="f75c1-479">Hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="f75c1-479">Request headers</span></span>
  * <span data-ttu-id="f75c1-480">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="f75c1-480">Environment variables</span></span>

<span data-ttu-id="f75c1-481">Spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="f75c1-481">To run the sample:</span></span>

<span data-ttu-id="f75c1-482">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="f75c1-482">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="f75c1-483">Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="f75c1-484">Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f75c1-485">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-485">Compile and run the app.</span></span> <span data-ttu-id="f75c1-486">Odkaz na balíček se nachází v rozšířené aplikaci (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f75c1-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="f75c1-487">`<PropertyGroup>`V souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku.</span><span class="sxs-lookup"><span data-stu-id="f75c1-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="f75c1-488">To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f75c1-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f75c1-489">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou balíčku `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f75c1-490">Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f75c1-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="f75c1-491">K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :</span><span class="sxs-lookup"><span data-stu-id="f75c1-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="f75c1-492">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="f75c1-492">**Activation from a class library**</span></span>

1. <span data-ttu-id="f75c1-493">Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="f75c1-494">Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f75c1-495">*bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary. dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="f75c1-496">Zkompilujte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-496">Compile and run the app.</span></span> <span data-ttu-id="f75c1-497">`<ItemGroup>`V souboru projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci).</span><span class="sxs-lookup"><span data-stu-id="f75c1-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="f75c1-498">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f75c1-498">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="f75c1-499">Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou knihovny tříd `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f75c1-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f75c1-500">**Aktivace z běhového úložiště – nasazené sestavení**</span><span class="sxs-lookup"><span data-stu-id="f75c1-500">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="f75c1-501">Projekt *StartupDiagnostics* používá k úpravě souboru *StartupDiagnostics. DEPS. JSON* [PowerShell](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="f75c1-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="f75c1-502">PowerShell je ve výchozím nastavení nainstalován v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="f75c1-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="f75c1-503">Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace různých verzí PowerShellu](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="f75c1-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="f75c1-504">Spusťte skript *Build. ps1* ve složce *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="f75c1-505">Skript:</span><span class="sxs-lookup"><span data-stu-id="f75c1-505">The script:</span></span>
   * <span data-ttu-id="f75c1-506">Vygeneruje `StartupDiagnostics` balíček ve složce *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="f75c1-507">Vygeneruje běhové úložiště pro `StartupDiagnostics` ve složce *Store* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="f75c1-508">`dotnet store`Příkaz ve skriptu používá `win7-x64` [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows.</span><span class="sxs-lookup"><span data-stu-id="f75c1-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="f75c1-509">Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu.</span><span class="sxs-lookup"><span data-stu-id="f75c1-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="f75c1-510">Běhové úložiště pro `StartupDiagnostics` by bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno.</span><span class="sxs-lookup"><span data-stu-id="f75c1-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="f75c1-511">Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="f75c1-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="f75c1-512">Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="f75c1-513">Další závislosti by se později přesunuly na další závislosti uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="f75c1-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="f75c1-514">Umístění pro `StartupDiagnostics` instalaci dalších závislostí uživatele je *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/2.2.0/StartupDiagnostics. DEPS. JSON*.</span><span class="sxs-lookup"><span data-stu-id="f75c1-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="f75c1-515">Umístí soubor *Deploy. ps1* do složky pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="f75c1-516">Spusťte skript *Deploy. ps1* ve složce pro *nasazení* .</span><span class="sxs-lookup"><span data-stu-id="f75c1-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="f75c1-517">Skript připojí:</span><span class="sxs-lookup"><span data-stu-id="f75c1-517">The script appends:</span></span>
   * <span data-ttu-id="f75c1-518">`StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="f75c1-519">Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="f75c1-520">Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do `DOTNET_SHARED_STORE` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f75c1-520">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="f75c1-521">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f75c1-521">Run the sample app.</span></span>
1. <span data-ttu-id="f75c1-522">Požádejte o `/services` koncový bod, aby se zobrazily registrované služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-522">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="f75c1-523">Požádejte `/diag` koncový bod, aby zobrazil diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="f75c1-523">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
